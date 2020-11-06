# javascript数组技巧
## 数组去重

```
var fruits = ['banana', 'apple', 'orange', 'apple']

方法一：Array.from(new Set(fruits))
方法二：[..new Set(fruits)]
```

## 替换数组中的特定值
- splice(startIndex, numToRemove, item1, item2...)

### 数组map的替换方法

```
var friends = [
    { name: "John", age: 22 },
    { name: "Peter", age: 23 },
    { name: "Mark", age: 24 },
    { name: "Maria", age: 22 },
    { name: "Monica", age: 21 },
    { name: "Martha", age: 19 },
]
 

var friendsNames = Array.from(friends, ({name}) => name);
console.log(friendsNames);
// returns ["John", "Peter", "Mark", "Maria", "Monica", "Martha"]
```
### 清空数组
arr.length = 0

### 用初始化的数据填充数组

new Array(10).fill('1')
还可以指定下标范围：

```
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.fill("Runoob", 2, 4);
```

### 合并数组
- concat
- [...arr1, ...arr2, ...arr3]

### 求2个数组的交集
先对一个数组去重，遍历它，判断它里面的元素在不在另一个数组中
[...new Set(arr1)].filter((item)=>{ arr2.includes(item) })

### 从数组中删除假值
arr.filter(Boolean)

### 数组中取随机值
复习一下随机数函数：
num>=1 && num <=10
Math.floor((Math.random()*10)+1);

num >=0 && num <= 9
Math.floor((Math.random()*10));

arr[Math.floor(Math.random()*arr.length)]

### lastIndexOf

### 将数组中所有值相加
arr.reduce((sum, curr)=>{ return sum+ curr })