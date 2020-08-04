# Tapable 
## 使用步骤
- STEP1：new一个hooks，并传入参数

```
	myCar.hooks.calculateRoutes= new AsyncParallelHook(["source", "target", "routesList"])   
```
- STEP2：使用tap等类tap函数添加监听函数

```
// promise: 绑定promise钩子的API
myCar.hooks.calculateRoutes.tapPromise("GoogleMapsPlugin", (source, target,routesList) => {
	// return a promise
	return google.maps.findRoute(source, target).then(route => {
	   routesList.add(route);
	});
});
// tapAsync:绑定异步钩子的API
myCar.hooks.calculateRoutes.tapAsync("BingMapsPlugin", (source, target, routesList, callback) => {
    bing.findRoute(source, target, (err, route) => {
        if(err) return callback(err);
        routesList.add(route);
        // call the callback
        callback();
    });
});

// You can still use sync plugins
// tap: 绑定同步钩子的API
myCar.hooks.calculateRoutes.tap("CachedRoutesPlugin", (source, target, routesList) => {
    const cachedRoute = cache.get(source, target);
    if(cachedRoute)
        routesList.add(cachedRoute);
})
```

同步钩子中, tap 是唯一的绑定方法

异步钩子通常支持多种监听方法，包括异步和同步方式：

```
const {
    SyncHook,
    SyncBailHook,
    SyncWaterfallHook,
    SyncLoopHook,
    AsyncParallelHook, // 异步并行钩子
    AsyncParallelBailHook,
    AsyncSeriesHook,  // 异步串行钩子
    AsyncSeriesBailHook,
    AsyncSeriesWaterfallHook
 } = require("tapable");
```
### 拦截器interception

```
myCar.hooks.calculateRoutes.intercept({
	// 多个钩子执行一次
    call: (source, target, routesList) => {
  
    },
    // 每个钩子执行之前(多个钩子执行多个),就会触发这个函数
    tap: (tap: Tap) => { 
    
    } ,
    // 每添加一个Tap都会触发
    register: (tapInfo) => {
        // tapInfo = { type: "promise", name: "GoogleMapsPlugin", fn: ... }
        console.log(`${tapInfo.name} is doing its job`);
        return tapInfo; // may return a new tapInfo object
    },
    // 这个会为你的每一个循环钩子(LoopHook, 就是类型到Loop的)触发,触发时机未知
    loop:(...args) => void {
   
    }
})
```
