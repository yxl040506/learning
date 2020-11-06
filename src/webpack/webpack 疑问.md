webpack是如何处理css文件的

webpack的mainTemplate和chunkTemplate


# Webpack hash
## hash
- 工程级别的。
- 一个工程项目下的所有文件共用一个hash，但也意味着某个文件一改变，所有文件的hash就会全部改变
- 不能有效利用缓存、

## chunkhash
- chunk级别的
- 解析一个入口下依赖的所有文件，构建对应的chunk，生成对应的hash值

## contenthash
- 文件级别的
- 每个文件有自己的hash，互不依赖互不影响

## commonsChunkPlugin
- 用途：打包第三方公共模块

## js文件分类构建

```
output: {
  path: path.resolve(__dirname, './dist'),
  publicPath: '/dist/',
  filename: 'js/[name].[chunkhash].js', 
  chunkFilename: 'js/[id].[chunkhash].js' // chunkFilename 是处理异步模块的
}
```
- 产生3种chunkhash
	- 1、正常js文件的hash
	- 2、异步加载的js文件hash
	- 3、第三方库和公共模块的hash

	
## CSS文件打包
- extractTextPlugin:
	- 将所有入口chunk中引用的css文件，抽离到独立的css文件中；不再内嵌到js bundle
	-  使用contenthash命名
	
	```
		plugins: [
			  new ExtractTextPlugin({
			    filename: 'css/[name].[contenthash].css',
			    allChunks: true
			  }),
		]
	```
	- 不可使用chunkhash
		- 

