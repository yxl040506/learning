# Webpack loader
## loader的返回值
- 转换后的源码
- sourceMap

## 执行顺序：
- 从【数组最末】往【数组最开始】

## 如何处理css的
- @import和url语句导入的依赖需要被模块系统处理
	- 方法一：使用require
		- .css文件使用
	- 方法二：使用this.resolve 
		- .less文件使用
		- 因为less文件必须经过编译

# Webpack plugin
所有的hook：https://webpack.js.org/api/compiler-hooks/#hooks

```
// compiler.hooks.compilation能够在回调中返回compilation
class HelloCompilationPlugin {
  apply(compiler) {
    // Tap into compilation hook which gives compilation as argument to the callback function
    compiler.hooks.compilation.tap('HelloCompilationPlugin', compilation => {
      // Now we can tap into various hooks available through compilation
      compilation.hooks.optimize.tap('HelloCompilationPlugin', () => {
        console.log('Assets are being optimized.');
      });
    });
  }
}

module.exports = HelloCompilationPlugin
```
## Compiler Hooks
- entryOption：webpack中的entry配置被处理完
- afterPlugins：内部plugins挂载完
- beforeRun：
- run
- normalModuleFactory：NormalModuleFactory被创建
- initialize：compiler 对象被初始化之后
- beforeCompile：
- compile：在beforeCompile之后立马调用，在一个新的compilation被创建
- 以下钩子，都能获取到compilation（在回调函数中）
	- thisCompilation：当初始化compilation对象之后，发送compilation 事件之前
	- make：完成本次编译之前
	- afterCompile：完成和封印本次编译之前
	- shouldEmit：发送静态文件之前，需要返回一个bool值，是否要emit
	- emit：发送给assets到目标路径之前
	- afterEmit
- assetEmitted：能拿到file和info
