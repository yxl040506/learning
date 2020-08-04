## redux状态管理
### 组件分层
- UI组件：获取props进行UI渲染、不使用this.state
- 容器组件：负责管理数据和业务逻辑、有内部状态
- 使用redux将得到一个使用`容器组件`包裹的`UI组件`

### store 
- 定义： 数据存储中心，同时连接着Actions和Views（React Components）。
	- STEP1：Store需要负责接收Views传来的Action
		- 每一个Store实例都拥有dispatch方法，Views只需要通过调用该方法，并传入action对象作为形参，Store自然就可以收到Action 
	- STEP2：根据Action.type和Action.payload对Store里的数据进行修改
		- 数据修改逻辑写在Reducer（一个纯函数）里，Store实例在创建的时候，就会被传递这样一个reducer作为形参，这样Store就可以通过Reducer的返回值更新内部数据了。 
	- STEP3：store通知views数据改变，view调用setState
		- 如何实现的：store提供了一个订阅方法subscribe。View使用该方法注册一个listener（内含setState操作）。之后每次dispatch(action)时，该回调都会被触发，从而实现重新渲染

### action
- 本质：javascript普通对象，描述了一次变更的计划
- 组成：action type和 需要变更的数据
- action创建函数
	- 定义：返回action的函数
	- 使用：一般会把action创建函数的结果传给dispatch()方法，即可发起一次dispatch过程
	
### reducer
- 定义：一个接收 state 和 action，并返回新的 state 的函数
- 通过定义多个reducer对数据进行拆解访问或者修改，最终再通过combineReducers函数将零散的数据拼装回去
- 只要state有发生改变，reducer返回的都是一个全新的state。shouldComponentUpdate可以通过判断对象引用。
- 不可变性：Redux通过一个个reducer实现了不可变数据（immutability）。

### dispatch方法
- STEP1：调用根reducer（根reducer就是初始化的时候传给createStore的）
- STEP2：调用监听函数（调用store.subscribe函数时传入的listener）

### 总结
- Store的数据修改，本质上是通过Reducer来完成的。
- Store只提供get方法（即getState），不提供set方法，所以数据的修改一定是通过dispatch(action)来完成，即：action -> reducers -> store
- Store除了存储数据之外，还有着消息发布/订阅（pub/sub）的功能，也正是因为这个功能，它才能够同时连接着Actions和Views。
- dispatch方法 对应着 pub
- subscribe方法 对应着 subs

## redux中间件
- 定义：中间件就是对dispatch的重写或者扩展，增强dispatch的功能（dispatch发送计划的过程中，顺便做点别的事）

### redux-thunk
- dispatch的参数是一个函数

### redux-saga


## react-redux
### connect
- 用于从 UI 组件生成容器组件

```
// 这只是一个没有与外界交互的足迹，既没有获取props，也没有修改数据
import { connect } from 'react-redux';
const VisibleTodoList = connect()(TodoList);
```
```
// connect 源码
trySubscribe() {
    if (shouldSubscribe && !this.unsubscribe) {
	  // 跟store关联，消息订阅
      this.unsubscribe = this.store.subscribe(this.handleChange.bind(this))
      this.handleChange()
    }
}

handleChange() {
    if (!this.unsubscribe) {
      return
    }

    const prevStoreState = this.state.storeState
    const storeState = this.store.getState()

    if (!pure || prevStoreState !== storeState) {
      this.hasStoreStateChanged = true
      this.setState({ storeState }) // 组件重新渲染
    }
}
```
### Provider
- 原理：通过context的方式将唯一的数据源store传递给任意想访问的子孙组件
从根组件，将store里的数据层层传递给子组件
```
const store = createStore(
  todoApp,
  persistedState
);
```
### mapStateToProps、mapDispatchToProps
- mapStateToProps：
	- 作用：将容器组件的state作为props传递给ui组件。也可以理解为订阅Store，容器组件的state更新的时候，同步给UI组件。
	
	```
	// 第一个参数总是state对象，还可以使用第二个参数，代表容器组件的props对象。
	const mapStateToProps = (state, props) => {
	  return {
	    active: props.filter === state.visibilityFilter
	  }
	}
	```
- mapDispatchToProps：
	- 返回一个函数，把这个函数作为props的一部分传递给子组件
	- 该函数可以通过dispatch方法调用一个action
	```
		// Action Creator
		const increaseAction = { type: 'increase', number }
	```
	```
	const mapDispatchToProps = {
	  	onIncreaseClick: (number) => dispatch(increaseAction, number)
	}
	```
	- 该函数也可以直接返回一个
	```
	const mapDispatchToProps = {
	  	onIncreaseClick: (number) => {
	  		type: 'increase',
	  		number
	  	}
	}
	```