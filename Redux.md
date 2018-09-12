# Redux
### 三大原则
1. 单一数据源
	- 一个应用永远只有一个唯一的数据源
2. 状态是只读的
	- 没有直接修改，而是返回一份全新的状态
	- 确保视图和网络请求都不能直接修改 state，它们只能表达想要修改的意图。所有的修改都被集中化处理，且严格按照一个接一个的顺序执行
3. 状态修改均由纯函数完成
	- 纯函数：接受一定的输入，必定会得到一定的输出

### Action
- store 中数据的唯一来源
- 只是描述了有事情发生了这一事实，并没有指明如何更新 state
- **尽量减少在 action 中传递的数据**

**Action 创建函数**： 生成 action 的方法
```
function addTodo(text) {
  return {
    type: 'ADD_TODO',
    text
  }
}
```

### Reducer
- 接收旧的 state 和 action，返回新的 state
- 不要在 reducer 里做：
	- 修改传入参数；
	- 执行有副作用的操作，如 API 请求和路由跳转；
	- 调用非纯函数，如 `Date.now()` 或 `Math.random()`
	
**只要传入参数相同，返回计算得到的下一个 state 就一定相同。没有特殊情况、没有副作用，没有 API 请求、没有变量修改，单纯执行计算**

##### 注:
- 不要修改 state：可以使用对象展开运算符(...)
- 每个 reducer 只负责管理全局 state 中它负责的一部分。每个 reducer 的 state 参数都不同，分别对应它管理的那部分 state 数据。
- combineReuducers() 所做的只是生成一个函数 ，这个函数来调用你的一系列 reducer，每个 reducer 根据它们的 key 来筛选出 state 中的一部分数据并处理，然后这个生成的函数再讲所有 reducer 的结果合并成一个大的对象。

**reducer 是纯函数，仅仅用于计算下一个 state，是完全可预测的：多次传入相同的输入必须产生相同的输出**


> 发两个 action

1. api 是单个的，不推荐在 api 层进行处理（发完一个 api 接着发另一个 api）
2. 在 reducer 层可以接收到请求的结果，但是不能在该层调用另一个 api
3. 可以在发 action 的时候使用 promise 的 then 触发另一个 action
4. 或者在 createAction 的时候，第二个参数给两个 action，那么它会在第一个 action 完成后调用另一个 action


### Store
1. 职责
	- 维持应用的 state
	- 提供 getState() 方法获取 state
	- 提供 dispatch(action) 方法更新 state
	- 通过 subscribe(listener) 注册监听器
	- 通过 subscribe(listener) 返回的函数注销监听器

	**createStore() 第二个参数可选，用于设置 state 初始状态**
	
- mapStateToProps 指定如何把当前 redux store state 映射到展示组件的 props 中
- mapDispatchToProps 接收 dispatch() 方法并返回期望注入到展示组件的 props 中的回调方法

- _**若担心 mapStateToProps 创建新对象太过频繁，可使用 reselect**_


### 异步 Action
> 一般情况下，每个 API 请求都需要 dispatch 至少三种 Action：

- 通知 reducer 请求的 Action
- 通知 reducer 请求成功的 Action
- 通知 reducer 请求失败的 Action
- **将同步 Action 创建函数和网络请求结合起来——redux-thunk**

- 当 Action 创建函数返回函数时，这个函数会被 redux thunk middleware 执行（这个函数不需要保持纯净；可带有副作用，包括执行异步 API 请求）

> 服务器端渲染须知：
> 异步 action 创建函数对于做服务端渲染非常方便。你可以创建一个 store，dispatch 一个异步 Action 创建函数，这个 Action 创建函数又 dispatch 另一个异步 Action 创建函数来为应用的一整块请求数据，同时在 Promise 完成后结束时才 render 界面。然后在 render 前，Store 里就已经存在了需要用的 state。
> 

	

	