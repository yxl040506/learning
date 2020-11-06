# 注意区分action和actionCreator
- action：一个js对象
- actionCreator：一个函数，用于接受一些参数，来定制化生产action对象

```
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

我们通过这样调用：dispatch(addTodo(index))，来完成一次dispatch
## createAction
export const getCollectList = createAction(GET_COLLECT_LIST)

```
 const actionCreator = (...args) => {
 	// 如果没有定义
    const payload = finalPayloadCreator(...args);
    const action = { type };

    if (payload instanceof Error) {
      action.error = true;
    }

    if (payload !== undefined) {
      action.payload = payload;
    }

    if (hasMeta) {
      action.meta = metaCreator(...args);
    }

    return action;
  };
  

  
  createAction (type, payloadCreator, metaCreator) { 
 		return actionCreator
  }
```

- 得到的是：action创建函数
  - actionCreator的返回值简化之后：
  
```
  {
  		type: xxx,
  		payload: xxx,
  		meta: xxx
  }
```
## dispatch 一个 action
```
const mapDispatchToProps = dispatch => ({
    collectActions: bindActionCreators(CollectActions, dispatch)
  })
```
bindActionCreators(CollectActions, dispatch)

-得到的actionCreator其实是包裹了dispatch的

调用collectActions.collect(data)的同时，会创建一个action对象，并触发一次dispatch

## connect方法
React Redux 库的 connect() 方法来生成，这个方法做了性能优化来避免很多不必要的重复渲染。（这样你就不必为了性能而手动实现 React 性能优化建议 中的 shouldComponentUpdate 方法。）

# middleWare