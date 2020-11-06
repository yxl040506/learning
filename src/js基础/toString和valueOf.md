# toString和valueOf
## toString
- 转换成字符串
- 精确的类型判断：某种场合，比typeof和instanceof更高效准确
- 重写toString方法

## valueOf
返回对象的原始值

- 在数值运算中，优先调用了valueOf;
- 字符串运算中，优先调用了toString

## 优先级
- 在进行对象转换时，
	- 优先调用对象重写的toString方法
	- 再调用对象重写的valueOf方法
	- 如果都没有重写，那就使用Object的toString方法
- 运算操作符：
	- valueOf的优先级高于toString 

# mianshiti
## a===1&&a===2&&a===3 为 true
- 双等号：会触发隐式类型转换
	- 可用valueOf 或 toString来实现
- 全等：不会隐式转换
	- 使用Object.defineProperty 
	
	```
	Object.defineProperty(window,'a',{
	    set:function(newA){
	    		// 注意一定要写_a，否则会导致死循环
	        this._a = newA;
	  
	    },
	    get:function(){
	        return this._a ++
	    }    
	})	
	```
## 柯里化实现多参数累加
```
	function add () {
		let args = [...arguments];
		let fn = function () {
			return add.apply(null, args.concat([...arguments]))
		}
		fn.toString = () => {
			args.reduce((a, b)=>{return sum + curr})
		}
		return fn
	}
```
https://mp.weixin.qq.com/s/J30Op8n0yiZKwFoGIyqarA