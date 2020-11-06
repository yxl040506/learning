# Webpack打包后代码执行机制和优化
### 单入口、单输出文件
- webpack打包后的代码其实就是一个立即执行函数
	- 传入模块，使用webpack_require进行调用个 
- 文件加载后立即执行业务逻辑

### 多入口、多输出文件
- 场景：
	- 抽取公共模块打包，避免重复加载：splitChunks
	- 配置external，第三方库单独加载 
- 问题：
	- 需要等待入口文件及非入口的chunk文件加载完，才开始执行
- 在抽取公共模块的情况下
	- 主入口文件中：
		- 声明了依赖的所有chunk
		- chunk文件加载后，标记加载完成了，并检查是否所有chunk都加载完了(主入口文件会提供给chunk相关的回调函数)
		- 文件加载后，检查相关文件是否都加载完成。如是，则开始执行主逻辑
	- 依赖的chunk文件中
		- 正常情况下，entry先于chunk加载完，则调用entry提供更难过的回调方法，标记完成
		- 当chunk文件先于入口文件加载完，则先缓存记录，等entry加载完后，读取缓存并标记完成
		
		```
		// # entry.js

		// 声明依赖列表
		deferredModules.push(["./src/entryA.js","commons"]);
		
		// 缓存已完成的加载
		var installedChunks = {
		    "entryA": 0
		};
		
		function webpackJsonpCallback(data) {
		    // 加载后标记完成
		    installedChunks[chunkId] = 0;
		}
		
		// 检查是否都加载完成，如是，则开始执行业务逻辑
		function checkDeferredModules() {
		    // 判断 installedChunks 是否完整
		    // ...
		    if(fulfilled) {
		        // 所有都加载，开始执行
		        result = __webpack_require__(__webpack_require__.s = deferredModule[0]);
		    }
		}
		
		// 提供给 chunk 的全局回调方法
		var jsonpArray = window["webpackJsonp"] = window["webpackJsonp"] || [];
		jsonpArray.push = webpackJsonpCallback;
		
		
		// # chunk.js

		(window["webpackJsonp"] = window["webpackJsonp"] || []).push([["commons"],{
		     "./src/moduleA.js":  (function(module, __webpack_exports__, __webpack_require__) {
		           
		     })
		}]);
	```
- 在external的情况下
	- webpack的处理：默认external的模块在业务代码执行前，已存在环境中，不做任何判断了
	- 后果：external 文件未加载完成或加载失败时，使用对应模块将会导致执行出错
	```
	"react":  (function(module, exports) {
     eval("(function() { module.exports = window[\"React\"]; }());");
	})
	``` 

### 如何做到等待external文件？
- 封装entry逻辑，不立即执行过
- 获取依赖的 external Modules，分析 external 对应变量
- external模块不存在时，监听等待文件加载完成过后再判断执行
- external模块都存在后，再执行entry逻辑

	