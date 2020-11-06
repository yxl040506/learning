# webpack打包速度优化手段
共10个
1、缩小文件范围

- module：2个
	- noParse
	- loader：
		- test/include/exclude 
		- babel：cacheDirectory
- resolve：3个
	- modules
	- extensions
	- mainFiled
- plugin
	- DllPlugin 和 HardSourceWebpackPlugin 
2、加快编译速度
- happyPack 开启多线程
3、加快压缩速度

- ParallelUglifyPlugin

		
- 文件监听：1个
	- watchOption：ignore 不监听node_modules
	
## 1、loader配置优化
- 动机：loader对文件的转换很耗时，尽可能少地处理文件
- 优化操作：
	- 控制转换文件的范围 
		- test：匹配文件路径名称
		- include： 只对src目录中的文件采用babel-loader
		- exclude： 
		
		```
		module .exports = {
            module : {
                rules : [{
                //如果项目源码中只有 文件，就不要写成/\jsx?$/，以提升正则表达式的性能
                test: /\.js$/,
                //babel -loader 支持缓存转换出的结果，通过 cacheDirectory 选项开启
                use: ['babel-loader?cacheDirectory'] ,
                //只对项目根目录下 src 目录中的文件采用 babel-loader
                include: path.resolve(__dirname,'src'),
                }],
            }
        }
		```
	- 缓存转换的结果
			
		```
		use: ['babel-loader?cacheDirectory'] 
		```

## 2、resolve.modules配置
- 字段含义：
	- 默认值是['node_modules']，指示第三方模块的存放目录
	- 先去当前目录的node_modules目录下找模块，没有去层层往上寻找
- 动机：层层寻找node_modules很耗时
- 优化提升厉害

```
module.exports = {
    resolve: {
        modules: [path.resolve( __dirname,'node modules')]
    }
}
```

## 3、resolve.extensions
- 动机：没写文件后缀名的import语句，webpack会一个一个尝试后缀名查找

```
module.exports = {
        resolve : {
            //尽可能减少后缀尝试的可能性
            extensions : ['js'],
        }
    }
```

## 4、resolve.mainFileds
- 字段含义：第三方库一般都会在package.json中声明该库的入口文件
```
{
	main: 'dist/index.js'
}
```
- 动机：减少搜寻入口文件的步骤

```
module.exports = {
        resolve: {
        //只采用 main 字段作为入口文件的描述字段，以减少搜索步骤
        mainFields: ['main']
        }
    }
```
## 5、module.noParse
- 动机：对于没有采用模块化的文件，不需要进行递归解析处理，比如jquery

```
module.exports = {
    module: {
        noParse: /jquery/,
    }
};
```

## 6、ParallelUglifyPlugin

## 7、DllPlugin
- 动机：将第三方库只单独打包一次，不重复打包

- 现在有替代方案：

```
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin')

const plugins = [
	new HardSourceWebpackPlugin()
]
```

## 8、文件监听
```
module.export = {
        watchOptions : {
            //不监听的 node_modules 目录下的文件
            ignored : /node_modules/,
        }
}
```

## 9、happyPack
## 10、还有一个冷门的alias