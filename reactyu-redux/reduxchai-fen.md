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

1. 鸭子式（推荐）

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
function courseDetailReducer(state = {}, action) {
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

export default courseDetailReducer;
export {
  getCourseDetail,
  defaultState,
};
```

    2. 函数功能划分

按照函数公共划分，即按照actionType、actionCreator、reducer将文件分为action_creator、action_\_type、reducer。这种方式因为函数功能类似，所以结构较清晰，但是并不利于后期的维护，因为添加一个action需要在三个文件中做更改。代码略，目录结构如下图：

* share
  * action\_creator.js
  * action\_type.js
  * reducer.js



## 公共Redux的使用



