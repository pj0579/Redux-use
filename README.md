react native redux流程梳理
---
![Alt text](https://raw.githubusercontent.com/pj0579/Redux-use/master/A2043282-5BEC-4FEF-9771-3DE81EAF0FCE.png)<br/>
###图片来网络   描述部分来自redux中文网 和自己的理解 写给自己看的 <br/>
执行流程讲述：<br/><br/>
1.View触发事件，一般情况下你可以直接在 `Store` 实例上调用 `dispatch()`接受一个`action`作为参数）。一般情况在 `React native` 中使用 `Redux` ，俄们会使用 `react-redux` 提供一个 `Store` 实例内部的 `dispatch` 的 `connect` 函数 。 `Store` 就是把它们联系到一起的对象。`Store` 有以下属性和方法 <br/><br/>
+ 提供 `getState()` 方法获取 state ；<br/>
+ 提供 `dispatch(action)` 方法更新 state ；<br/>
+ 通过 `subscribe(listener)` 注册监听器;用于订阅更新<br/>
+ 通过 `unsubscribe()`注销监听器。<br/><br/>
2. 更新时，假设有多个`reducer` 的情况下，并使用`combineReducers ` ,`dispatch(action)` 更新`Store` 时 `combineReducers` 返回的 `rootreducer` 会负责调用多个 `reducer` ，然后会把多个结果集合并成一个 `state`<br/>  <br/><br/>
3.`Store` 中的`State` 发生变化，通过 `subscribe(listener)` 注册监听器;订检测到更新，调用`setState` 更新包装组件，再将组件`State 传递给真实组件，完成更新。

文中有关的知识：

`react-redux` 提供的 `Provider` 包裹`Store` 传递到子组件。

###上述是redux同步应用
下面介绍异步redux   
异步redux需要分析一下源码才能够说清楚。
异步redux需要redux-thunk，之后再说怎么自己实现异步redux。
redux-thunk提供了thunk，react-redux提供了applyMiddleware
下面看 applyMiddleware源码
function applyMiddleware() {
  for (var _len = arguments.length, middlewares = Array(_len), _key = 0; _key < _len; _key++) {
    middlewares[_key] = arguments[_key];
  }

  return function (createStore) {
    return function (reducer, preloadedState, enhancer) {
      var store = createStore(reducer, preloadedState, enhancer);
      var _dispatch = store.dispatch;
      var chain = [];

      var middlewareAPI = {
        getState: store.getState,
        dispatch: function dispatch(action) {
          return _dispatch(action);
        }
      };
      chain = middlewares.map(function (middleware) {
        return middleware(middlewareAPI);
      });
      _dispatch = _compose2['default'].apply(undefined, chain)(store.dispatch);

      return _extends({}, store, {
        dispatch: _dispatch
      });
    };
  };
}


