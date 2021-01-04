> Promise是异步编程的另一种选择，它的工作方式类似于在其他语言中延迟并在将来执行作业。一个Promise指定一些稍后要执行的代码，并且也明确标示了作业的代码是否执行成功。

#### 异步编程的背景
JS引擎建立在单线程事件循环的概念上。单线程意味着在同一时刻只能执行一段代码。代码会被放置在作业队列（job queue）中，每当一段代码准备被执行，它就被添加到作业队列 。当JS引擎结束当前代码的执行后，事件循环（event loop）就会执行队列中的下一个作业。  
回调函数模式是异步编程的最常用的一种选择，但是当回调函数嵌套过多时，会陷入回调地狱（callback hell），代码会可读性会变差且调试困难。

#### Promise的生命周期
Promise初始为挂起态（pending）时，表示异步操作尚未结束，此时的Promise是未决的（unsettled），异步操作结束的Promise是已决的（settled），已决的Promise有完成（fullfilled）及拒绝（rejected）两种状态。
```
graph LR
A[挂起态 pending]-->B[已完成 fulfilled]
A-->C[已拒绝 rejected]
```
#### 使用`then()`方法监测Promise状态的改变
`then()`方法在所有Promise上都存在，接收两个参数（非必传的）。第一个参数为Promise状态变为`已完成（fullfilled）`时要调用的函数，第二个参数是Promise状态改变为`已拒绝（rejected）`时要调用的函数。  
示例代码：
```js
let promise = readFile("example.txt");

//同时监听完成和拒绝
promise.then(function(contents) {
    // 完成
    //contents中包含异步操作成功相关的数据
    console.log(contents);
}, function(err) {
    // 拒绝
    //err中包含异步操作被拒绝相关的数据
    console.error(err.message);
});

//只监听完成
promise.then(function(contents) {
    console.log(contents);
});

//只监听拒绝
promise.then(null, function(err) {
    console.error(err.message);
});

//等价于使用catch()
promise.catch(function(err){
    console.error(err.message);
})
```

#### 创建未决的Promise
使用Promise构造器来创建，接收一个函数作为参数，该函数传递`resolve`及`reject`两个参数。  
示例代码：
```js
// Node.js 范例
let fs = require("fs");
function readFile(filename) {
    // 创建promise并作为函数的返回值
    return new Promise(function(resolve, reject) {
        // 触发异步操作
        fs.readFile(filename, { encoding: "utf8" }, function(err, contents) {
            // 检查错误
            if (err) {
                // promise状态会改变为rejected
                reject(err);
                return;
            } 
            // 读取成功，promise状态会变为fullfilled
            resolve(contents);
        });
    });
} 
//调用readFile，得到返回的Promise对象
let promise = readFile("example.txt");
// 监听promise对象的状态
promise.then(function(contents) {
    // 完成
    console.log(contents);
    }, function(err) {
    // 拒绝
    console.error(err.message);
});
```
##### Promise的作业顺序问题
```js
let promise = new Promise(function(resolve, reject){
    
});􏱝􏰼􏱋􏱝􏱖􏰅􏱙􏱖􏱝􏱞􏰾􏱛􏷈􏱌􏷉􏱛􏰿􏱘
```