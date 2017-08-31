react native redux流程梳理
---
执行流程讲述：<br/><br/>
1.View触发事件，一般情况下你可以直接在 Store 实例上调用 dispatch （接受一个action 作为参数）。一般情况在 React native 中使用 Redux ，俄们会使用 react-redux 提供一个 Store 实例内部的 dispatch 的 connect 函数 。 Store 就是把它们联系到一起的对象。Store 有以下属性和方法：  <br/>
* 维持应用的 state ；
* 提供 getState() 方法获取 state ；
* 提供 dispatch(action) 方法更新 state ；
* 通过 subscribe(listener) 注册监听器;用于订阅更新
* 通过 unsubscribe() 注销监听器。<br/><br/>
2. 更新时，假设有多个reducer 的情况下，并使用combineReducers dispatch(action) 更新Store 时 combineReducers 返回的 rootreducer 会负责调用多个 reducer ，然后会把多个结果集合并成一个 stater/>  <br/><br/>
3. 3.Store 中的State 发生变化，通过 subscribe(listener) 注册监听器;订检测到更新，调用setState 更新包装组件，再将组件State 传递给真实组件，完成更新。

文中有关的知识：

react-redux 提供的 Provider 包裹Store 传递到子组件。


