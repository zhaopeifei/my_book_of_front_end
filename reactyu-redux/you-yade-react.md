# 优雅的React写法

## 组件层级

---

### 容器组件与展示组件

容器组件负责业务逻辑，展示组件负责渲染，展示组件尽量做成无状态组件。

```
class Container extends Component {
    getData() => {
        this.props.getData();
    }

    render() {
        return (
            <div className="container">
            {
                props.children && props.children.map((item, index) => {
                    return (
                        <ChildCard
                            key={item.id}
                            {...item}
                        />
                    );
                })
            }
            </div>
        )
    }
}
```

```
import './index.scss'

const ChildCard = ({name, avatar}) => {
    return (
        <div className="ChildCard">
            <img className="avatar" src={avatar} alt="孩子头像" />
            <div calssName="name">{name}</div>
        </div>
    );
}

export default ChildCard;
```

在设计组件的时候，把页面分为逻辑部分和渲染部分，逻辑部分尽量放到container容器组件中，尽量不负责渲染等工作，仅仅负责将展示组件给组合在一起，有利于逻辑的复用；而展示展示部分，按照一定的粒度分为多个展示组件，每个组件内部负责css样式和JSX结构，尽量不要使用状态，将展示组件写成上面的无状态组件，看起来更加清晰、轻量，复用也更加简单。

### 高阶组件（组合和继承）

在不需要用高阶组件的地方尽量不要用高阶组件，要用高阶组件的情况下，尽量选择组合的方式。

```
class FooterBar extends Component {
    render() {
        if (utils.getParams(coupon) === 'QQZONEVIP') {
            return <FooterBarQQzondVip />
        }
        return <FooterBarBasic />
    }
}
```

```
class FooterBarQQzondVip extends Component {
    beforePay = () => {
        this.props.judgeQQzoneVip()
        .then(() => {
             this.setState({ isGoPay: true });   
        }).catch(() => {
            console.log('not qq zone vip');
        })
    }

    render() {
        return (
            <FooterBarBasic
                beforePay={this.beforePay}
                isGoPay={this.state.isGoPay}
            />
        );
    }
}
```

```
class FooterBarBasic extends Component {
    componentWillReceiveProps(nextProps) {
        if(nextProps.isGoPay && !this.props.isGoPay) {
            this.pay();
        }
    }

    beforePay = () => {
        this.props.beforePay();
    }

    pay = () => { // do something }

    render() {
        return (
            <div className="footer-bar">
                <button onClick={this.beforePay}>购买</button>
            </div>
        );
    }
}
```

## 方法

---

### 方法定义及绑定this

使用箭头函数，直接在定义的时候就可以绑定this，一般情况下，直接使用箭头函数就好。不建议使用bind，使用bind的话还需要在constructor中进行绑定this，一来是要在两个地方写，二来，很多时候并不需要constructor函数。

```
class FooterBarBasic extends Component {
    pay = (id) => {
        this.props.beforePay(id);
    }

    render() {
        return (
            <div className="footer-bar">
                <button onClick={() => { this.pay(this.props.id); }}>购买</button>
            </div>
        );
    }
}
```

### 尽量不要传参

上面加入函数需要参数，则需要使用`onClick={() => { this.beforePay(this.props.id); }`或者\``onClick={this.beforePay.bind(this.this.props.id) }`\`的方式。但是，这两种方式都会动态生成函数，会造成浪费。可以说，只要在JSX给函数传参，那么久会动态生成函数，所以，要避免传递参数。需要的参数尽量在函数内部通过props或者state来获取。

```
class FooterBarBasic extends Component {
    pay = () => {
        this.props.beforePay(this.props.id);
    }

    render() {
        return (
            <div className="footer-bar">
                <button onClick={this.pay}>购买</button>
            </div>
        );
    }
}
```

### 方法长度

一个方法的长度不要超过20行，超过的话，就需要进行拆分。特别是生命周期中的函数，一定不要太长。

## 变量、解构、赋值

---

现在我们都是用的ES6的写法，尽量使用const、let，避免使用var来定义变量。在React中，props以及props中的值都是不建议做修改的，所以上级组件传过来的props数据，使用const来定义；自己在组件中定义的可能改变的变量使用let。优先级为：const &gt; let &gt;&gt; var。

另外，在进行对象变量赋值时，尽量使用简写，如果名称不一致，可以进行解构赋值。例如：

```
class FooterBarBasic extends Component {
    pay = () => {
        const {
            course_id,
            isjoin,
            cou_id: coupon_id, // 解构命名
        } = this.props;
        
        this.props.pay({
            course_id,
            isjoin,
            cou_id, // 最后一项也要带逗号，方便代码比对和添加参数
        });
    }

    render() {
        return (
            <div className="footer-bar">
                <button onClick={this.pay}>购买</button>
            </div>
        );
    }
}
```

## 使用Promise

在React和Redux中，我们经常需要发起ajax请求来获取数据。每次发起ajax请求后，都需要dispatch来把获取的数据加到props中，这个就需要新建actionType以及reducer函数。然而，有些时候，我们只需要知道成功或者失败，并不需要获取数据，此时就可以使用Promise来处理。使用promise不仅可以避免react中ajax请求发起和处理结果的隔离感，更能少些很多代码。但是，promise中的数据获取和新的props的数据时间顺序是不可控的，如果对获取值的时间顺序有要求，不要相信promise中返回的数据（因为这个时候，props的新数据可能还没有拿到）。

```
class FooterBarBasic extends Component {
    pay = () => {
        const {
            course_id,
            isjoin,
            cou_id: coupon_id, // 解构命名
        } = this.props;
        
        this.props.pay({
            course_id,
            isjoin,
            cou_id, // 最后一项也要带逗号，方便代码比对和添加参数
        }).then(() => {
            location.href = 'http://fudao.qq.com/pay_success.html';
        }).catch(() => {
            alert('支付失败，请重试!');
        });
    }

    render() {
        return (
            <div className="footer-bar">
                <button onClick={this.pay}>购买</button>
            </div>
        );
    }
}
```

## Reducer的简写

避免reducer中大量重复的代码。

```
const reduer = function (state = {}, action) {
  switch (action.type) {
    case TYPE1:
    case TYPE2:
    case TYPE3:
      return Object.assign(...state, action.data); // 一样的处理方式进行合并
      
    case PAID: 
      return Object.assign(...state, action.data, {paid: true});
      
    default:
      return state;
  }
};   
```

## 生命周期函数

除了render函数是必须的外，其他生命函数在不需要的时候都不要写。constructor只有在需要定义state的时候是必须的。




