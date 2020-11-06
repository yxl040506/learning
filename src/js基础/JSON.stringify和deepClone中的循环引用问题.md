# JSON.stringify和deepClone中的循环引用问题
## deepClone是可以解决循环引用问题的
- 解决办法分为2部
	- STEP1：设置一个map作为缓存
		- 一定要是new Map()，并且一定要使用set、get这两个api
		
		```
			// 这种方法是不对的
			var cache = {} 或 var cache = new Map()
			var objToClone1 = {a: 2}
			var objToClone2 = {a: 1}
			cache[objToClone1] = xxxx
			cache[objToClone1] = xxxx
			
			会导致后面的覆盖前面的
			因为实际上cache中存储的key是[object Object]
		```
		
		```
			// 这样是对的，map可以以一个对象的地址作为key，不会相互覆盖
			var cache = new Map()
			cache.set(objToClone1, xxx)
			cache.set(objToClone2, xxx)
		```
	- STEP2：要在递归处理子对象之前，先把克隆出来的对象缓存在map中
		- map其实存的是对象的引用，子对象处理完之后，自动会填充进拷贝对象
		- 不能等待子对象递归处理完才存进map！因为这样，子对象克隆时，判断cache.get(src)还是空的，还是会爆栈
		
	```
		var result = Array.isArray(src) ? [] : {}
		if(isObject(src)) {
		    if(!map.get(src)) { 
		    	  // 先创建拷贝的结果，并在递归处理之前先把引用保存进缓存
		        map.set(src, result)
		        // 递归处理子元素的时候， 把map传递下去
		        forEach(src, (key, clonedValue)=>{
		            result[key] = clonedValue
		        }, map)
		    }
		    return map.get(src)
		} else {
		    return src
		}
		    
		function forEach(src, cb, map) {
		    if(Array.isArray(src)) {
		        src.forEach((item, index)=>{
		            cb(index, cloneDeep(item, map))
		        })
		    } else {
		        Object.keys(src).forEach((key)=>{
		            cb(key, cloneDeep(src[key], map))
		        })
		    }
		}
	```
	
## JSON.stringify处理不了循环引用问题
- json字符串化，输出的是字符串，字符串是不可变的。没办法通过子对象拷贝完，动态变更这串字符

```

function jsonstringify (obj, cache = new Map()) {
    var str = ''
    if(cache.get(obj) !== undefined) {
        return cache.get(obj)
    }
    if(isObject(obj)) {
        cache.set(obj, '[Circular]')
        str = '{'
        const keys = Object.keys(obj)
        keys.forEach((key, index)=>{
            str += `${key}:`
            str += jsonstringify(obj[key], cache)
            if(index < keys.length - 1) {
                str += ','
            }
        })
        str += '}'
        cache.set(obj, str)
        return str
    } else if(Array.isArray(obj)) {
        str = '['
        obj.forEach((item, index)=>{
            str += jsonstringify(item, cache)
            if(index < obj.length -1) {
                str += ','
            }
        })
        str += ']'
        cache.set(obj, str)
        return str
    } else {
       return obj
    }
  
}
function isObject (obj) {
    return toString.call(obj) === '[object Object]'
}
var target = {
    field1: 1,
    field2: undefined,
    field3: {
        child: 'child'
    },
    field4: [2, 4, 8, {objInArr: 1}],
    f: { f: { f: { f: { f: { f: { f: { f: { f: { f: { f: { f: {} } } } } } } } } } } },
};

target.target = target;
var result = jsonstringify(target)
// var result = JSON.stringify(obj1)
console.log('result', result)
```