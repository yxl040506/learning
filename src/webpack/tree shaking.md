# tree shaking
## 基本使用
- mode=production情况下自动开启
- 仅支持import写法
	- 只支持 import   ES2015 import 这种静态引入
	- 不支持require CommonJS 这种动态引入
- sideEffect
	- package.json的sideEffect变量 
	- 默认是true的
	- 针对没有具体导入变量，但是有副作用的代码模块，比如以下两种常见情况
	
		```
		//a.js
		console.log('side Effect from a.js')
		
		// b.js
		import './a.js'
		
		``` 
	
		```
		import './style.css'
		```
	- package.json的配置
	
		```
		{
			  "name": "ling-live-client",
			  "sideEffects": [
			    "*.less",
			    "src/utils/sideEffect.ts"
			  ]
		}
		```
		
## 其他实践
- 组件库
- css DCE
- 按需加载的异步模块op