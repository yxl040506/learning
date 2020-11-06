# 类型
## 第一题：void 和 undefined 有什么区别？

- 默认来说，null和undefined是其他所有类型（比如void）的子类。这意味着你可以把null和undefined赋值给其他任意类型，比如number、void，只要你没有开启`--strictNullChecks`
- 类型声明的时候函数返回void，但是实际上可以返回别的非undefined类型
	- 如果要准确控制的话，要声明返回undefined
	
```
function doSomething(callback: () => void) {
    let c = callback() // at this position, callback always returns undefined
    //c is also of type undefiend
  }
  // this function returns a number
  function aNumberCallback(): number {
    return 2;
  }
  
  // works type safety is ensured in doSometing
  doSomething(aNumberCallback) 
```
## 第二题：什么是 never 类型？
- Never：如果一个函数不返回任何东西，Never则可以作为它的返回值类型声明
- Never可以是任意类型的子类型，因此可以被复制给任意类型的变量，不能赋值给自己本身。
- 其他所有类型不能赋值给它。

## 第四题：const 和 readonly的区别

- readonly：
	- 只能在interface、type、 class 中使用，用于修饰属性
	- 只能在以下2种情况下初始化：声明的时候顺便初始化 或者 在constructor里初始化
- const:
	- 声明时必须初始化，其值不能被重新分配
- 用readonly来修饰property，用const修饰变量

## 第六题：类型object和any的区别
- 虽然object和any都可以让你给变量赋任何值
- 但是object不可以让你调用对象中可能存在的函数，会报不存在的错误。但是any可以
- object用来描述非原始类型（非 number, string, boolean, bigint, symbol, null, 或undefined.）

```
let notSure: any = 4;
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let prettySure: Object = 4;
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.
```

## 类型断言
- 仅由编译器使用，它对运行时没有影响。 
- 类似其他语言的类型转换，但是不执行数据的特殊检查或重组

```
// 有两种形式
// 使用<xxx>，但是在jsx语法中不适用
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;

// 使用as
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

## 第一题：type和interface的区别
相交类型 A & B
联合类型 A | B

- type某些情况下可以被继承，不可以去继承别人
	- class不能继承type
	- 某些情况下(object类型的type或交叉类型的object type)，class能impletments type
	- 某些情况下(object类型的type或交叉类型的object type)，interface能extends type
- type可以与其他类型相交，interface不可以




```
An interface can only extend an object type or intersection of object types with statically known members.
```
- interface可以被继承，也可以去继承别的，比如interface或class、type


# 函数入参检测
## 假重载
- 通过输写函数声明，以达到函数入参的检测
- 但是其实还是不能实现多个同名函数，多个实现体，会报错Duplicate function implementation.(2393)

```
function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): { suit: string; card: number; };


function pickCard(x:any): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}
```
# 范型
## 范型函数

```
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}
let myIdentity: GenericIdentityFn<number> = identity;
```
## 范型类
```
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

```
