
升级至webpack4后，一些默认插件由 optimization 配置替代了，如下：

- CommonsChunkPlugin废弃，由 optimization.splitChunks 和 optimization.runtimeChunk 替代，前者拆分代码，后者提取runtime代码。原来的CommonsChunkPlugin产出模块时，会包含重复的代码，并且无法优化异步模块，minchunks的配置也较复杂，splitChunks解决了这个问题；另外，将 optimization.runtimeChunk 设置为true（或{name: “manifest”}），便能将入口模块中的runtime部分提取出来。
- NoEmitOnErrorsPlugin 废弃，由 optimization.noEmitOnErrors 替代，生产环境默认开启。
- NamedModulesPlugin 废弃，由 optimization.namedModules 替代，生产环境默认开启。
- ModuleConcatenationPlugin 废弃，由 optimization.concatenateModules 替代，生产环境默认开启。
- optimize.UglifyJsPlugin 废弃，由 optimization.minimize 替代，生产环境默认开启。
不仅如此，optimization 还提供了如下默认配置：
http://louiszhai.github.io/2019/01/04/webpack4/

```
optimization: {
    minimize: env === 'production' ? true : false, // 开发环境不压缩
    splitChunks: {
        chunks: "async", // 共有三个值可选：initial(初始模块)、async(按需加载模块)和all(全部模块)
        minSize: 30000, // 模块超过30k自动被抽离成公共模块
        minChunks: 1, // 模块被引用>=1次，便分割
        maxAsyncRequests: 5,  // 异步加载chunk的并发请求数量<=5
        maxInitialRequests: 3, // 一个入口并发加载的chunk数量<=3
        name: true, // 默认由模块名+hash命名，名称相同时多个模块将合并为1个，可以设置为function
        automaticNameDelimiter: '~', // 命名分隔符
        cacheGroups: { // 缓存组，会继承和覆盖splitChunks的配置
            default: { // 模块缓存规则，设置为false，默认缓存组将禁用
                minChunks: 2, // 模块被引用>=2次，拆分至vendors公共模块
                priority: -20, // 优先级
                reuseExistingChunk: true, // 默认使用已有的模块
            },
            vendors: {
                test: /[\\/]node_modules[\\/]/, // 表示默认拆分node_modules中的模块
                priority: -10
            }
        }
    }
}
```
splitChunks是拆包优化的重点，如果你的项目中包含 element-ui 等第三方组件（组件较大），建议单独拆包，如下所示。
```
splitChunks: {
    // ...
    cacheGroups: {    
        elementUI: {
            name: "chunk-elementUI", // 单独将 elementUI 拆包
            priority: 15, // 权重需大于其它缓存组
            test: /[\/]node_modules[\/]element-ui[\/]/
        }
    }
}
```

# Webpack输出文件优化
- treeshaking
	- 必须使用es6模块
		- babel配置：让其保留es6模块化语句
		
		```
		{
        'presets':[
            [
                'env',{
                    'module':false
                }
            ]
        	]
    	}
		```  
	- UglifyjsPlugin
		- 需要使用UglifyJsPlugin插件 
		- mode：production默认开启
	- optimization.usedExports 
		- 这样之后，webpack会在打包文件中添加诸如/* unused harmony export */这样的注释，其后UglifyJsPlugin插件会对这些注释作出理解。
		
	```
	// 完整配置
	module.exports = {
        mode: 'none',
        optimization: {
            minimize: true,
            minimizer: [
                new UglifyJsPlugin()
            ],
            usedExports: true,
            sideEffects: true
        }
    }
	```

- 提取公共代码

	splitChunks
	
	```
	splitChunks: {
        chunks: "async",
        minSize: 30000,
        minChunks: 1,
        maxAsyncRequests: 5,
        maxInitialRequests: 3,
        automaticNameDelimiter: '~',
        name: true,
        cacheGroups: {
            vendors: {
                test: /[\\/]node_modules[\\/]/,
                priority: -10
            },
        default: {
                minChunks: 2,
                priority: -20,
                reuseExistingChunk: true
            }
        }
    }
	```
	
- 动态加载：
	- Webpack 支持两种动态代码拆分技术：

		- 符合 ECMAScript proposal 的 import() 语法，推荐使用
	
		- 传统的 require.ensure
	
	- import() 用于动态加载模块，其引用的模块及子模块会被分割打包成一个独立的 chunk。Webpack 还允许以注释的方式传参，进而更好的生成 chunk。

```
const Test = loadable({
        loader: () =>import('./test'),
})

const AppRouter = () => (
    <>
        <Route path="/test" exact component={Test} />
    </>
```