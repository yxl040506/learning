# 名词解释
## side effect
只要是被yield的值，都可以被称作effect
yield 1 
yield fetch('some-url')
## - dispatch的参数仍旧是一个plain object
- 每一个saga都是一个generator函数
- api
	- fork：
		- 返回一个无阻塞的task，不会等待yield返回，会继续往下执行
		- 所有的forked任务都被附	- 当父节点终止自身的instacution操作，它将会等待所有fored task放回。
		- 子任务发生的错误将会冒泡到父级。任何forked任务跑出异常，父级任务将会终止整个执行树。
	- spawn: 
		- 创造独立的任务
	- take：
	- takeEvery
	- call：会阻塞
	- put：指示中间件安排一个dispatch(放入任务队列，不一定马上执行)
	- takeLatest
### redux-saga原理
- take：注册回调（监听）函数
	- yield take()返回的是一个effect，其实是一个plainObject 
- put：被触发的时候，take注册的函数就会被调用
## redux-saga里
- effect的生产者(effect-producer): 业务代码。使用yield语句将effect传给saga-middleware
- effect的消费者(effect-runner)：saga-middleware。根据effect的类型解释该effect，将结果返回给生产者（即我们的业务代码）
- saga函数：
	javascript生成器函数（传入函数sagaMiddleware.run的生成器函数）
- saga实例：
	调用saga函数得到的迭代器对象
- task:
	saga实例运行状态的描述对象

## 生成器和迭代器
```
// 生成器函数：
function* range(start, end) {
  for (let i = start; i < end; i++) {
    yield i
  }
}
```
```
// 迭代器：调用生成器函数得到的返回值
const iterator = range(1, 10)
```
## 如何消费迭代器
- for-of (不可行)
- while true (不可行，同步的，无法等待网络请求的返回)
- 递归

```
const iterator = range(1, 10)
function next() {
  const { done, value } = iterator.next()
  if (done) {
    return
  }
  console.log(value)
  if (value % 2 === 0) {
    setTimeout(next, value * 300)
  } else {
    next()
  }
}

next()
```

### effect 的类型与含义
根据yield的类型，使用next返回对应类型的结果