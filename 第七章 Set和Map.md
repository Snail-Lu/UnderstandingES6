#### 1. Set
**Set**是不包含重复值的有序列表，常用来检查某个值是否存在。  
##### 1.1 Set的方法
- add(): 添加值
- has(): 测试值是否存在
- delete(): 删除单个值
- clear(): 清除所有值

示例代码：  
```js
let set = new Set();

set.add(5);
set.add('5');
console.log(set.size);  //2 
console.log(set.has(5)); //true

set.delete(5);
console.log(set.has(5)); //false
console.log(set.size);  //1

set.clear();
console.log(set.size); //0
```

##### 1.2 Set上的forEach方法
与数组上的forEach方法类似，接收一个回调函数，回调函数接收三个参数：
1. Set中下个位置的值
2. Set中下个位置的值
3. 目标Set自身

示例代码： 
```js
let set = new Set([1,2,3]);
set.forEach((value,key,ownerSet)=>{
    console.log(value+","+key);
    console.log(ownerSet === set);
});

// 输出结果
1,1
true
2,2 
true
3,3 
true
```
##### 1.3 将Set转换为数组
```js
function eliminateDuplicates(items){
    return [...new Set(items)];
}

let numbers = [1,2,3,3,3,3,4];

let noDuplicates = eliminateDuplicates(numbers);
console.log(noDuplicates);  //[1,2,3,4]
```

#### 2. Weak Set
*Weak Set*类型只允许存储对象弱引用，不能存储基本类型的值。对象的弱引用在它成为该对象的唯一引用时，不会阻止垃圾回收。
##### 2.1 Weak Set的方法
- add()
- has()
- delete()  

示例代码：
```js
let set = new Set(), key = {};

set.add(key);
console.log(set.has(key)); //true

// 移除对象的强引用，set中的引用依然还在
key = null;
console.log(set.size); //1


let weakSet = new WeakSet();
weakSet.add(key);
console.log(weakSet.has(key)); //true

// 移除对于该对象的最后一个强引用，Weak Set中的引用会自动解除
key = null;
```
##### 2.2 与Set的关键差异
- Weak Set实例调用add()，传入非对象参数会抛出错误；
- 不可迭代
- 没有size属性和clear()方法

#### 3. Map  
**Map**类型是键值对的有序列表，常用作缓存，存储数据以便以后快速检索。Map的键和值都可以是任意类型。
##### 3.1 Map的方法
- set(key,value): 给Map添加项 
- get(key): 提取键值对应的值
- has(key)：判断键是否存在于Map中
- delete(key)：移除Map中的键以及对应的值
- clear()： 移除Map中所有的键与值

示例代码：  
```js
let map = new Map();
map.set("name", "Jack");
map.set("age", 25);
console.log(map.size); //2
console.log(map.has("name"); //true
console.log(map.get("name")); //"Jack"

map.delete("name");
console.log(map.size);  //1
console.log(map.has("name")); //false
console.log(map.get("name")); //undefined

map.clear();
console.log(map.size); //0
```
##### 3.2 Map上的forEach方法
接收一个能接收三个参数的回调函数：  
1. Map中下个位置的值
2. 该值所对应的键
3. 目标Map本身

示例代码：
```js
let map = new Map([["name","Nicholas"],["age",20]]);
map.forEach(function(value,key,ownerMap){
    console.log(key + "," + value);
    console.log(ownerMap === map);
});

// 输出结果
name,Nicholas
true
age,20
true
```

#### 4. Weak Map
Map的弱引用方式（键才是弱引用，值不是），所有的键都必须是非空的对象，值可以是任意类型。
##### 4.1 Weak Map的方法
- set(): 添加数据
- get(): 提取数据 
- has(): 判断指定的键是否存在于Map中
- delete(): 移除一个特定的键值对

示例代码：
```js
let map = new weakMap(),
    element = document.querySelector(".element");
    
map.set(element,"Original");

console.log(map.has(element));  //true
console.log(map.get(element));  //"Original"

element.parentNode.removeChild(element);
element = null;          //weakMap中对应的数据也会被移除    
```

##### 4.2 与Map的差异
- Weak Map只接受非空对象作为键值
- 不可迭代和枚举
- 没有size属性和clear()方法