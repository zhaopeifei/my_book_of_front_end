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

## 方法定义&传参&绑定this

---

### 方法定义及绑定this

使用箭头函数，直接在定义的时候就可以绑定this，一般情况下，直接使用箭头函数就好。不建议使用bind，使用bind的话还需要在constructor中进行绑定this，一来是要在两个地方写，二来，很多时候并不需要constructor函数。

```
class FooterBarBasic extends Component {
    beforePay = (id) => {
        this.props.beforePay(id);
    }

    render() {
        return (
            <div className="footer-bar">
                <button onClick={() => { this.beforePay(this.props.id); }}>购买</button>
            </div>
        );
    }
}
```

### 尽量不要传参

上面加入函数需要参数，则需要使用`onClick={() => { this.beforePay(this.props.id); }`或者\``onClick={this.beforePay.bind(this.this.props.id) }`\`的方式。但是，这两种方式都会动态生成函数，会造成浪费。可以说，只要在JSX给函数传参，那么久会动态生成函数，所以，要避免传递参数。需要的参数尽量在函数内部通过props或者state来获取。

```
class FooterBarBasic extends Component {
    beforePay = () => {
        this.props.beforePay(this.props.id);
    }

    render() {
        return (
            <div className="footer-bar">
                <button onClick={this.beforePay}>购买</button>
            </div>
        );
    }
}
```



