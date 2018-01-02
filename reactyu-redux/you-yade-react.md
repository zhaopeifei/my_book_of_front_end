# 优雅的React写法

## 组件

---

尽量分模块划分为不同的组件，每个组件文件中最多只能有一个组件类，且组件的样式划分到每个组件中。文件结构如下：

* course\_pay // 页面名称
  * AreaDetail // 组件文件夹
    * index.jsx  // 组件jsx
    * index.scss // 组件样式
  * Coupon
    * index.jsx
    * index.scss
  * FooterBar
    * index.jsx
    * index.scss
  * offpack
    * phone.png
    * time.png
  * Container.jsx // 容器组件
  * index.html 
  * index.jsx // 组合action，连接store和页面
  * index.scss// 容器层面样式
  * preload-init.js
  * reducer.js
  * store.js

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
import './index.scss'; // 通过这种方式来引入本组件的css

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

### PureComponent

pureComponent可以帮助我们做浅比较，从一定程度上避免重复渲染。

```
import React, { PureComponent } from 'react';

class Child extends PureComponent {
  render (
    <div className="child">{this.props.name}</div>
  );
}

export default Child;
```

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

### PropTypes

使用PropTypes做React类型检查，一来可以在开发环境下协助定位问题，二来可以清晰的看到组件接口及含义。propTypes类型检查只有在dev模式下有用，在Production下没有用，应该进行去除，可以使用`transform-react-remove-prop-types`的库来进行移除proptypes操作。

```
import React from 'react';
import PropTypes from 'prop-types';

import './index.scss'

const ChildCard = ({name, avatar}) => {
  return (
    <div className="ChildCard">
    <img className="avatar" src={avatar} alt="孩子头像" />
    <div calssName="name">{name}</div>
    </div>
  );
}

ChildCard.propTypes = {
  name: PropTypes.string.isRequired, // 名称
  avatar: PropTypes.string, // 头像
}

export default ChildCard;
```

## defaultProps

不要检查某个props值是否存在，而是使用defaultProps去预设Props。

## 

## 方法

---

### 生命周期函数

除了render函数是必须的外，其他生命函数在不需要的时候都不要写。constructor只有在需要定义state的时候是必须的。

### 事件响应函数命名

事件响应函数满足以下条件：

1. 第一个单词为handle；
2. 最后一个单词为要响应的的事件，如Click；
3. 中间词汇根据功能来定义。

### 方法定义及绑定this

使用箭头函数，直接在定义的时候就可以绑定this，一般情况下，直接使用箭头函数就好。除了生命周期函数外，都使用箭头函数。

```
class FooterBarBasic extends Component {
  handlePayClick = (id) => {
    this.props.beforePay(id);
 }

  render() {
    return (
      <div className="footer-bar">
        <button onClick={() => { this.handlePayClick(this.props.id); }}>购买</button>
      </div>
    );
  }
}
```

### 尽量不要传参

上面加入函数需要参数，则需要使用`onClick={() => { this.beforePay(this.props.id); }`或者\``onClick={this.beforePay.bind(this.this.props.id) }`\`的方式。但是，这两种方式都会动态生成函数，会造成浪费。可以说，只要在JSX给函数传参，那么久会动态生成函数，所以，要避免传递参数。需要的参数尽量在函数内部通过props或者state来获取。

```
class FooterBarBasic extends Component {
  handlePayClick = () => {
    this.props.beforePay(this.props.id);
  }

  render() {
    return (
      <div className="footer-bar">
        <button onClick={this.handlePayClick}>购买</button>
      </div>
    );
  }
}
```

### 方法长度

一个方法的长度不要超过20行，超过的话，就需要进行拆分。特别是生命周期中的函数，一定不要太长。

### 使用get、set

在render中，有时候需要缓存一些数据或者判断条件等，此时最好使用get或者set。

    // bad
    class Coupon extends Component {
      render() {
        let valueSales = props.sales 
          ? (`省 ¥${props.sales}`)
          : (props.coupon_disc 
            ? ('最多省¥' + (props.coupon_disc / 100).toFixed(2))
            : '无可用优惠券');

        return (
          <div className="coupon">
            <label className="formLabel">优惠券</label>
            <span className={valueClass}>{valueSales}</span>
          </div>
        )
      }
    }

把一些值放到get属性中去获取

    // good
    class Coupon extends Component {
      get valueSales () {
        if (props.sales) {
          return `省 ¥${props.sales}`;
        } else if (props.coupon_disc) {
          return '最多省¥' + (props.coupon_disc / 100).toFixed(2));
        }

        return '无可用优惠券';
      }

      render() {
        return (
          <div className="coupon">
            <label className="formLabel">优惠券</label>
            <span className={valueClass}>{this.valueSales}</span>
          </div>
        )
      }
    }

## 变量

---

### const、let

现在我们都是用的ES6的写法，尽量使用const、let，避免使用var来定义变量。在React中，props以及props中的值都是不建议做修改的，所以上级组件传过来的props数据，使用const来定义；自己在组件中定义的可能改变的变量使用let。优先级为：const &gt; let &gt;&gt; var。

### 赋值与解构

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

## 

## 异步请求

---

### Promise

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

## 

## Redux

---

### Reducer简写

避免reducer中大量重复的代码。

```
const reduer = function (state = {}, action) {
  switch (action.type) {
    case TYPE1:
    case TYPE2:
    case TYPE3:
      return Object.assign({}, state, action.data); // 一样的处理方式进行合并

    case PAID: 
      return Object.assign({}, state, action.data, {paid: true});

    default:
      return state;
  }
};
```

### combineReducer

随着应用变得复杂，需要对reducer函数进行拆分，拆分后的每一块独立负责管理state的一部分。combineReducer函数可以帮助我们组合reducer函数，它的作用仅仅如此，我们也可以自己来组合reducer。

```
export default combineReducer({
  course: courseReducer,
  pay: payReducer,
})
```

## 调试工具

---

store.js中可以使用如下写法，有如下功能：

1. dev模式下，可以使用redux-logger功能；
2. dev模式下，可以使用chrome内嵌的redux-devtools工具；
3. dev模式下，可以使用eruda的手机内嵌控制台台；
4. dev模式下，可以使用react-perf性能测试工具；
5. production模式下，不会有上述库。

```
import { createStore, applyMiddleware } from 'redux';
import thunkMiddleware from 'redux-thunk';
import reducer from './reducers';


let middleware = applyMiddleware(thunkMiddleware);


if (process.env.NODE_ENV !== 'production') {
  // dev 模式
  let createLogger = require('redux-logger');
  let reduxDevTool = require('redux-devtools-extension'); // redux工具
  let eruda = require('eruda'); // 手机内嵌控制台
  let Perf = require('react-addons-perf'); // react性能测试


  middleware = applyMiddleware(thunkMiddleware, createLogger());
  middleware = reduxDevTool.composeWithDevTools(middleware);
  eruda.init();
  window.Perf = Perf;
}


let __initialState = window.__initialState;


export default createStore(
    reducer,
    __initialState,
    middleware
);
```

## 

## 代码优雅写法

---

* 渠道上报

```
ext: ['', 'QQWALLET', 'QZONEVIP', 'QQVIP'].indexOf(utils.getParams('coupon'))
```

* 组合action

```
import {
  getCourseDetail,
  checkSubpayStatus,
} from 'pages/action_creators/course';

import {
  pay,
  resetPayFailCode,
} from 'pages/action_creators/pay';

const action = Object.assign({
  getCourseDetail,
  checkSubpayStatus,
  pay,
  resetPayFailCode,
});
```

* 使用模板字符串

  const url = \```${location.protocol}//fudao.qq.com/other_pay.html``

  * `?_bid=2379&course_id=${props.cid}&subpay_code=${props.subpayCode}`;

``const url = `${location.protocol}//fudao.qq.com/other_pay.html` +``

* 加载中&加载失败

```
render() {
  const props = this.props;

  if (!props.cid && props.courseDetailFail) {
    return <PageFail />;
  } else if (!props.cid) {
    return <FetchingLoading />;
  }

  return <div>加载成功</div>
}
```

* classname

```
let footerBarClass = classnames({
  footerBar: true,
  showOtherPay
});
```

相关链接：[Class Name Manipulation](https://github.com/JedWatson/classnames/blob/master/README.md)

