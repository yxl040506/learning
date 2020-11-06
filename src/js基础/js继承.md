# js继承
## 一、原型继承
- 本质：重写子类型的原型对象，通过创建一个父类型的实例并赋值给子类型的
关键代码

```
	Subtype.prototype = new SuperType()
	Subtype.prototype.constructor = SubType
```

- 扩展阅读
	- 1、Subtype.prototype 由[[prototype]]指针提供内部访问。
		- Firefox、Safari和Chrome在每个对象上都支持一个属性__proto__。 
	- 2、for...in
		- 可以读到自身和父类的属性
	- 3、因为原型链继承了Object，因此有默认方法：hasOwnProperty()、propertyIsEnumerable()、toLocaleString()、toString()和valueOf()
	- 4、确定原型和实例的关系
		- instanceof 操作符
		- isPrototypeOf
			- Subtype.prototype.isPrototypeOf(instance)) 
			- 只要是instance原型链上出现过的原型，都会返回true
	- 5、子类要在继承后定义新方法
		- 先把new出来的对象赋值给prototype，再给prototype添加参数
	- 6、不能使用对象字面量创建原型
		- 必须用new， 不可以这样Subtype.prototype = {xxx}
		- 会切断原型链
-缺点：父类中引用类型的属性，会在子类之间互相覆盖、影响

		```
			function SuperType () {
				this.colors = ['blue', 'red', 'green']
			}
			function SubType () {
			
			}
			SubType.prototype = new SuperType()
			SubType.prototype.constructor = SubType
			
			let sub1 = new SubType()
			let sub2 = new SubType()
			sub1.colors.push('yellow')
			那么sub2中的colors也会多一个yellow
		```  
	
## 二、构造函数继承
- 核心：使用构造函数的call方法
	```
		function Subtype () {
			SuperType.call(this)
		}
	``` 
- 缺点：父类的函数无法复用
	- 父类prototype上面的函数无法继承，只能写成this.xxxx = function() {}


## 三、组合继承
- 核心：使用构造函数call的函数继承属性，使用创建父类实例的方法继承prototype上的函数
- 缺点：需要调用2次父类构造函数

```
function SuperType(name){
  this.name = name;
  this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function(){
   alert(this.name);
};
function SubType(name, age){
//继承属性
  SuperType.call(this, name);
  this.age = age;
}
//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    alert(this.age);
};
```
## 四、寄生类继承
## 五、寄生组合式继承
- 核心：使用Object.create方式构造prototype的实例，避免了构造函数的二次调用；使用call方法调用父类构造函数

```
function SuperType(name){
  this.name = name;
  this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function(){
   alert(this.name);
};
function getPrototypeObj(SuperType) {
	// 需要考虑Object.create是否存在
	var prototype
	if(typeof Object.create === 'function') {
		prototype = Object.create(SuperType.prototype))
	} else {
		prototype = objectCreate(SuperType.prototype)
	}
	return prototype
}
function objectCreate(o){
    function W(){
    }
    W.prototype=o;
  	// 会多一层prototype
    return new W;
}
function SubType(name, age){
//继承属性
  SuperType.call(this, name);
  this.age = age;
}
//继承方法
SubType.prototype = getPrototypeObj(SuperType);
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    alert(this.age);
};
```

- 一些判断

prototype本来就是只有一个constructor属性的对象，添加了方法sayName

```
SuperType.prototype // {sayName: ƒ, constructor: ƒ, __proto__: Object.prototype}

SubType.prototype.__proto__ === SuperType.prototype // true

SubType.__proto__ === Function.prototype // true
```
## class继承
- 核心：extends关键字
	- class SubType extends SuperType
	- 在constructor方法中调用super方法，否则会报错，且子类得不到this对象
	
- ES5：
	- 实质是先创造子类的实例对象this(new)，然后再将父类的方法添加到this上面（Parent.call(this)） 
- ES6：
	- 实质是先将父类实例对象的属性和方法，加到this上面（所以必须先调用super方法），然后再用子类的构造函数修改this。 