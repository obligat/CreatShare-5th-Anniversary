# JavaScript 中的异步方法

标签（空格分隔）： JavaScript

---

### 1. 回调函数

假定有两个函数```f1()```和```f2()```，后者等待前者的执行结果。如果```f1()```是一个很耗时的任务，可以考虑改写```f1()```，把```f2()```写成```f1()```的回调函数。

```
　function f1(callback){
　　　　setTimeout(function () {
　　　　　　// f1的任务代码
　　　　　　callback();
　　　　}, 1000);
　　}
```
回调函数的优点是简单、容易理解和部署，缺点是不利于代码的阅读和维护，各个部分之间高度耦合（Coupling），流程会很混乱，而且每个任务只能指定一个回调函数。

### 2. 事件监听

另一种思路是采用事件驱动模式。任务的执行不取决于代码的顺序，而取决于某个事件是否发生。还是以f1和f2为例。首先，为f1绑定一个事件（这里采用的jQuery的写法）。
```
f1.on('done', f2);
```
上面这行代码的意思是，当f1发生done事件，就执行f2。然后，对f1进行改写：
```
　　function f1(){
　　　　setTimeout(function () {
　　　　　　// f1的任务代码
　　　　　　f1.trigger('done');
　　　　}, 1000);
　　}
```
f1.trigger('done')表示，执行完成后，立即触发done事件，从而开始执行f2。
这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以"去耦合"（Decoupling），有利于实现模块化。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。


### 3. 发布/订阅
上一节的"事件"，完全可以理解成"信号"。
我们假定，存在一个"信号中心"，某个任务执行完成，就向信号中心"发布"（publish）一个信号，其他任务可以向信号中心"订阅"（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做"发布/订阅模式"（publish-subscribe pattern），又称"观察者模式"（observer pattern）。


### 4. Promises对象
每一个异步任务返回一个Promise对象，该对象有一个then方法，允许指定回调函数。比如，f1的回调函数f2,可以写成：
```
　f1().then(f2);
```
f1要进行如下改写（这里使用的是jQuery的实现）：
```
　　function f1(){
　　　　var dfd = $.Deferred();
　　　　setTimeout(function () {
　　　　　　// f1的任务代码
　　　　　　dfd.resolve();
　　　　}, 500);
　　　　return dfd.promise;
　　}
```
这样写的优点在于，回调函数变成了链式写法，程序的流程可以看得很清楚，如果一个任务已经完成，再添加回调函数，该回调函数会立即执行。所以，你不用担心是否错过了某个事件或信号。

参考至：http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html




