# Redux拆分

## 公共Redux目录及组织方式

---

### 目录位置选择

* src
  * assets
  * components
  * pages
  * share
    * course.js
    * pay.js

关于公共的redux应该放置的位置和命名应该结合项目结构考虑，在我们的h5项目中，公共的库在assets中，公共组件在components中，所以建议将公共的redux也放置src目录下，并且命名为share（或者redux，具体名字可以再商讨）。

### action、reducer组织方式

#### 按模块进行划分（推荐）

目录结构如下：

* share
  * course.js
  * pay.js

以course.js为例，course.js中包含于course相关的模块的actionType、actionCreator、defaultState、reducer等，这种方式是按照模块来划分redux，把相关的代码写在一起，模块性更强。

```
/** actionType **/
const GET_COURSE_DETAIL = 'GET_COURSE_DETAIL';

/** actionCreator **/
const getCourseDetail = function(result) {
  dispatch({
    type: GET_COURSE_DETAIL,
    data: result.pageInfo,
  });
};

/** defaultState **/
const defaultState = {};

/** reducer **/
function courseReducer(state = {}, action) {
  switch (action.type) {
    case GET_COURSE_DETAIL:
      return {
        ...state,
        ...action.data,
      };

    default:
      return state;
  }
}

export default courseReducer;
export {
  getCourseDetail,
  defaultState,
};
```

#### 函数功能划分

按照函数公共划分，即按照actionType、actionCreator、reducer将文件分为action_creator、action_\_type、reducer。这种方式因为函数功能类似，所以结构较清晰，但是并不利于后期的维护，因为添加一个action需要在三个文件中做更改。代码略，目录结构如下图：

* share
  * action\_creator.js
  * action\_type.js
  * reducer.js

## 公共Redux的使用

---

公共redux的目录及代码组织方式默认是上面方法一种的方式，如果采用方法二的方式，只需要以另外一种方式取得actionCreatore函数、reducer函数即可。

### actionCreator的使用

在页面的index.jsx中，在通过react-redux库的connect方法，连接action，store和container时，组合公共的actionCreator方法

```
import { Provider, connect } from 'react-redux';
import {
  getCourseDetail,
} from 'share/course'; // 取出需要用的公共的actionCreator方法
import action from './action_creators';
import Container from './Container';

const actions = { // 组合本页面的、公共redux的、公共组件的actionCreator方法
  ...action,
  getCourseDetail,
};

const Root = connect(
  state => state,
  actions
)(Container);
```

### reducer的使用

reducer的应用影响了state的结构，所以这里要慎重一些，在页面的reducer.js中组合reducer

#### combineReducer

```
import { combineReducers } from 'redux';
import courseReucer from 'share/course';
import { dialogReducer } from 'components/dialog';
import { GET_DATA } from './action_types';

function reducer(state = {}, action) {
  switch (action.type) {
    case GET_DATA:
      return { ...state, ...action.data };

    default:
      return state;
  }
}

export default combineReducers({
  course: courseReucer,
  dialog: dialogReducer,
  pageInfo: reducer,
});
```

```
// 页面state，及根container的this.props数据结构
{
  course: { cid: 123 }, // 通过getCourseDetail方法获取的值
  dialog: { title: '确认' }, // Dialog对话框相关的值
  pageInfo: { some: 'somevaule' } // 页面本身redux获取的值
}
```

第一种方式是直接使用redux官方提供的combineReducer。这种方式比较有局限的一点是，每个reducer模块对应的state的数据都要对应一个对象，所以要取值的话，在组件中要使用诸如this.props.pageInfo这样的方式去取每个数据块的值，且相关之间不能做到数据共享。这样做的好处也很明显，就是数据分块明显，利于理解。

#### 自定义组织reducer

```
import courseReucer from 'share/course';
import { dialogReducer } from 'components/dialog';
import { GET_DATA } from './action_types';

function reducer (state = {}, action) {
  const course = courseReucer(state, action);
  state = {
    ...state,
    ...course,
    dialog: dialogReducer(state.dialog, action),
  };

  switch (action.type) {
    case GET_DATA:
      return { ...state, ...action.data };

    default:
      return state;
  }
}

export default reducer;
```

```
// 页面state，及根container的this.props数据结构
{
  cid: 123, // 通过getCourseDetail方法获取的值，在this.props顶层
  dialog: { title: '确认' }, // Dialog对话框相关的值
  some: 'somevaule' // 页面本身redux获取的值，在this.props顶层
}
```

combineReducer只是简单的将reducer进行组合而已，自己实现也是完全OK的。如上图的方式，我们可以选择将公共的redux模块的数据放在this.props中（如courseReducer），也可以选择将其放到this.props的一个对象中（如dialogReucer）。由于我们原有结构不适合使用combineReducer，应为改造成本高，所以建议使用自定义组织reducer模式。

## 公共组件redux

### Dialog组件代码结构

有些公共组件需要用到数据，譬如dialog组件，这种组件建议自带redux模块，此处以dialog组件为例。

```
import React, { Component } from 'react';
import classNames from 'classnames';

import {
  showDialog,
  hideDialog,
} from './action_creator';
import reducer from './reducer';

import './index.scss';

class Dialog extends Component {
  // 业务逻辑省略
}

export default Dialog;

export {
  showDialog,
  hideDialog,
  reducer as dialogReducer,
};
```

### Dialog组件使用

```
// 页面index.jsx
import {
  getCourseDetail,
} from 'share/course';
import {
  showDialog,
  hideDialog,
} from 'components/dialog';
import action from './action_creators';

const actions = {
  ...action,
  getCourseDetail,
  showDialog,
  hideDialog,
};
```

```
// 页面reducer.js
import { dialogReducer } from 'components/dialog';
function reducer (state = {}, action) {
  state = {
    ...state,
    course: courseReucer(state.courseDetail, action),
    dialog: dialogReducer(state.dialog, action),
  };
  // 业务逻辑略
}
```

```
// 页面container.jsx
class Container extends Component{
  showDialog = () => {
    this.props.showDialog({
      title: '报名成功',
      titleClass: 'succ',
      leftBtnName: '确定',
      leftBtnIsPrimary: false,
      leftBtnClick: this.props.hideDialog,
    });
  }

  render() {
    return(
      <div>
        <div onClick={this.showDailog}>点击显示弹框</div>
        <Dialog {...props.dialog} />
      </div>
    )
  }
}
```



