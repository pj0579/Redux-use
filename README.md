react native redux流程梳理
---
![Alt text](https://raw.githubusercontent.com/pj0579/Redux-use/master/A2043282-5BEC-4FEF-9771-3DE81EAF0FCE.png)<br/>
###图片来网络   描述部分来自redux中文网 和自己的理解 写给自己看 <br/>
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

###上述是redux同步应用<br/>
下面介绍异步redux   
异步redux需要分析一下源码才能够说清楚。
异步redux需要redux-thunk，之后再说怎么自己实现异步redux。
redux-thunk提供了thunk，react-redux提供了applyMiddleware
下面看 applyMiddleware源码

  	export default function applyMiddleware(...middlewares) {
    return (createStore) => (reducer, preloadedState, enhancer) => {
    const store = createStore(reducer, preloadedState, enhancer)
    let dispatch = store.dispatch
    let chain = []

    const middlewareAPI = {
      getState: store.getState,
      dispatch: (action) => dispatch(action)
    }
    chain = middlewares.map(middleware => middleware(middlewareAPI))
    dispatch = compose(...chain)(store.dispatch)

    return {
      ...store,
      dispatch
        }
      }
    }
    <br/>
applyMiddleware 返回一个函数，参数是createStore，这个函数也返回了一个函数，参数为reducer, preloadedState, enhancer。刚好对应 applyMiddleware(thunk)(createStore)(reducers);thunk为第一个参数，createStore为第二个参数，reducers为第三个参数。最后返回Store和dispatch，这个dispatch经过改造,可以接受参数为函数的方法，具体实现在redux-thunk源码里面如下：

    function createThunkMiddleware(extraArgument) {
    return ({ dispatch, getState }) => next => action => {
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
       }

      return next(action);
        };
      }
      const thunk = createThunkMiddleware();
其中thunk作为参数传递给...middlewares，此时数组里只有一个元素。<br/>
chain = middlewares.map(middleware => middleware(middlewareAPI))
这里面返回<br/>
          <pre><code>
           next => action => {
           if (typeof action === 'function') {
          return action(dispatch, getState, extraArgument);
          }
           return next(action);
          };
</code></pre>
最后下面这<br/>
 <pre><code>
 dispatch = compose(...chain)(store.dispatch)
</code></pre>
           
它返回<br/>
            <pre><code>
 action => {
           if (typeof action === 'function') {
           return action(dispatch, getState, extraArgument);
          }
           return next(action)
           };
</code></pre>
它可以根据action还是function去dispatch。
      


    

