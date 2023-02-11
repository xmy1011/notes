### 类型检测 & 快速区分

**1 JS 有几种基础数据类型？几种新增？**

> JS 有 8 种基础数据类型。： undefined null string number boolean  object | Symbol  bigInt
>
> Symbol :  创建之后 就是独一无二也不可变的 ，主要是用来做什么？ 用来解决全局变量冲突， 内部变量覆盖
>
> bigInt:   用来解决大数字类型的。表示任意精度的正数 安全存储和操作大数， 即便超出了number的安全整数范围。

**2 基础数据类型通常会如何进行分类？使用起来有什么区别，使用过程中如何区分它们的？**

> 可以分为原始数据类型 + 引用数据类型
>
> 原始数据类型有：undefined null string number number 
>
> 引用数据类型： 对象 函数 数组
>
> 区别：
>
> - 存储位置不同
>
>   - 原始数据类型存在栈内存中， 栈内存中保存的变量就是值。因为栈是一种先进后出的数据结构，栈区由编译器分配释放，常用来存储临时变量。
>
>   - 引用数据类型存在堆内存中， 栈内存的变量保存的是对象在堆内存的地址引用。
>
>     堆内存通常是由开发者进行分配释放的，长期存储机制， 直到应用结束。
>
> - 效果不同：
>
>   - 原始数据类型是直接赋值后，我们对赋值后的值进行修改，不存在引用关系、
>   - 而引用数据赋值后，再去修改属性，会发现原有的属性也被改变。存在引用关系。
>
>  原始数据放到栈中，空间小，大小固定，操作频繁。
>
> 引用类型数据量大， 大小不固定吗，赋值给的是地址。
>
> **通过栈和堆去区分原始数据类型，和引用类型。**

**3  如何进行类型的区分判断？几种对类型做判断区分的方式？**

> 1 typeof  无法区分对象和数组
>
> ===> 需要注意的特例：
>
> ```js
> typeof null // object
> typeof NaN // number
> ```

```js
typeof 2 // number
typeof true // boolean

typeof {} // object
typeof [] // object
typeof ()=>{} // function
```

> 2 instanceof 弥补了typeof 的缺陷

```js
// 返回是或否
2 instanceof Number // true
[] instanceof Array // true
{} instanceof Object // true
```

> 你能说一下或者手写 instanceof 的原理和实现吗
>
> 通过翻`户口本`， 顺着原型链往上找。
>
> ```js
> function myInstance(left, right){
>     // 获取对象的原型
>     let _proto = Object.getPrototypeOf(left);
>     // 构造函数的 prototype
>     let _prototype = right.prototype;
>     
>     while(true){
>         if(!_proto) return false;
>         if(_proto === _prototype) return true;
>         _proto = Object.getPrototypeOf(_proto)
>     }
>    
> }
> ```
>
> 3  通过constructor 
>
> ```js
> (2).constructor === Number // true
> ([]).constructor === Array // true
> ```
>
> ==> 隐患
>
> constructor 代表的是构造函数指向的类型，是可以被修改的
>
> ```js
> function Fn() {
>     
> }
> Fn.prototype = new Array();
> var f = new Fn()
> ```
>
>  4 Object. prototype. toString. call()
>
> ```js
> let a = Object.prototype.toString;
> a.call(2) // [object Number]
> a.call([]) // [object Array]
> ```

> 为啥要用call ？同样是检测Object调用toString, Object. toString()的结果与  Object.prototype.toString 结果不一样？
>
> ： 保证 toString 是 Object  上的原型方法， 根据原型链知识，优先调用 本对象属性。
>
>  ===> 当对象上有某个属性和Object 的属性重名时， 使用顺序是怎样的，如果说优先使用Object 上的属性，如何做？

### 类型转换

**1 isNaN 和 Number.isNaN 的区别？**

> isNaN 包含了一个隐式转换。 
>
> ​	isNaN  => 接收参数 => 尝试 将参数转换成数值型 => 如果不能被转换，返回 true
>
> Number.isNaN => 接收参数 => 判断是否为数值类型 => 判断是否为 NaN 

**2 既然说到了类型转换，有没有其他的类型转换场景？**

> 转成字符串： 
>
> ```js
> console.log(String(null)); // 'null'
> console.log(String(false)); // 'false'
> console.log(String(124)); // '124'
> console.log(String({ a: 1 })); // '[object Object]'
> ```
>
> 转成数字
>
> ```JS
> console.log(Number(true)); //1
> console.log(Number('123')); // 123
> console.log(Number('abc')); // NaN
> console.log(Number(null)); // 0
> console.log(Number(undefined)); // NaN
> console.log(Number({ a: 1 })); // NaN
> ```
>
> 转成 Boolean
>
> ```js
>  undefined | null |  '' | 0 | false |NaN ===> false
> ```

3 **原始数据类型如何具有属性操作的？**

> 前置知识： js 的包装类型
>
> ​	基础类型本来是没有属性方法的，但是在调用属性和方法时，JS 会在后台隐式的将基本类型转换成对	象。
>
> ```js
> let a = 'abc';
> a.length; // 3
> 
> let b = Object(a);
> // 去包装
> let c = b.valueOf() // 'abc'
> ```
>
> => 说说下面的代码执行结果：
>
> ```js
> let a = new Boolean(false);  // => Boolean {} 任何的东西只要经过包装都是对象
> if (!a) {
>   console.log('hi')
> }
> ```

### 数组操作的相关问题

**1 数组的基本操作方法？ 如何使用？**

> 转换方法：
>
>  	toString() toLocalString() join()
>
> 尾操作：
>
> ​	pop() push()
>
> 首操作
>
> ​	shift()  unshift()
>
> 排序
>
> ​	reverse()  sort()
>
> 连接的方法
>
> ​	concat()不影响原数组
>
> 截取
>
> ​	slice()
>
> 插入
>
>  	splice()
>
> 索引
>
> ​	indexOf()
>
> 迭代方法： every() some() filter() map() forEach()
>
> 归并： reduce()

### 2 变量提升 和 作用域

**1 谈谈对变量提升及作用域的理解**

> 变量提升： 将我们变量的定义提升到首部。
>
> ​	无论在任何位置声明的函数、变量、都被提升到模块、函数的顶部。
>
> JS的实现原理： 
>
> ​	分为 解析 | 执行
>
> 解析阶段：
>
> ​	1 检查语法 
>
> ​		把代码中即将执行的变量和函数声明往上调整到全局顶部，并且赋值为 undefined。
>
> ​	2 预编译
>
> ​		全局上下文： 变量定义 函数声明
>
> ​		函数上下文： 变量定义 函数声明 this arguments 
>
> ​	再去执行阶段，按照代码顺序运行。
>
> 变量提升的意义： 
>
> ​	提高性能，解析引用提升了性能，不需要到执行时重新解析。
>
> ​	更加灵活，补充定义这样一种玩法。
>
> ​	let const  没有变量提升

### 闭包

**1 什么是闭包？ 闭包的作用**

> 在一个函数中访问另一个函数作用域中变量的方法。
>
> 闭包的作用：
>
> ​	函数外部可以访问到函数内部的变量。
>
> ​		跨作用域， 创建私有变量
>
> ​		已经运行结束的逻辑，依然会残留在闭包中，这个变量无法回收。

**2 闭包经典题目结果 和 改造方式**

> ```js
> for(var i = 0 ;i<9 ; i++) {
>     setTimeout(() => {
>         console.log(i) // 每隔1s 打印出一堆 9 
>     }, i* 1000)
> }
> 
> // 利用闭包
> for(var i = 1 ; i< 9 ; i++) {
>     (function(j){
>         setTimeout(() =>{
>             console.log(9)
>         }, j*1000)
>     })(i)
> }
> 
> for (let i = 0; i < 9; i++) {
>   setTimeout(() => {
>     console.log(i)
>   }, i * 1000)
> }
> ```

### ES6 

**1  const 对象的属性可以修改吗？ new 一个箭头函数会发生什么呢？**

> const 只能保证指针是固定不变的，指向的数据结构属性，是无法控制是否变化的。
>
> **New 执行全过程**
>
> 1 首先创建该构造函数的实例对象
>
> 2 设置该实例对象的原型为 该构造函数的 prototype对象
>
> 箭头函数，没有Prototype 也没有独立的this 更没有arguments

**2 JS ES  内置对象有哪些？**

> 1 值属性类： Infinity NaN undefined null 
>
> 2 函数属性 ： eval() parseInt()
>
> 3 对象： Object Function Boolean Symbol, Error
>
> 4 数字： Number Math Date
>
> 5 正则： String RegExp
>
> 6 集合 Map Set  WeakMap
>
> 7 映射 proxy

### 原型 &  原型链

**1 简单说说 原型 原型链理解**

> 构造函数：
>
> ​	JS中是用来构造新建一个对象的，只有有构造函数才能新创建一个对象。
>
> ​	构造函数内部有个属性prototype， 是一个对象，包含了实例共享的属性和方法。
>
> ​	**使用构造函数创建对象后，被创建对象的内部会存在一个指针，（__proto__），指向构造函数的prototype属性对应的 对象**

**2 继承**

### 异步编程

**1 聊聊遇到的哪些异步执行方式**

> ​	 回调函数 => cb 回调地狱
>
> ​	promise  => 链式调用  => 语义不明确
>
> ​	generator => 考虑如何控制执行 co库
>
> ​	async await =>  不改变同步书写习惯的前提下，异步处理

**2 聊聊对 Promise 的理解**

> 一个对象 一个容器 => 触发操作
>
> 三个状态： pending | resolved | rejected 
>
> 两个过程： pending => resolved pending => rejected
>
> Promsie缺点：
>
> ​	无法取消
>
> ​	pending  状态， 无细分状态

###  内存和浏览器执行相关

**1 对垃圾回收的理解**

> 垃圾回收的概念： 
>
> ​	把不用的变量内存进行释放
>
> ​	JS 具有自动垃圾回收机制，找到不再使用的变量，释放其内存空间
>
> JS 存在两种变量：
>
> ​	局部变量（VO 在函数执行完毕后被回收） + 全局变量

**2 现代浏览器如何处理垃圾回收？**

> 标记清除
>
> ​	内存中所有变量加上标记，当前环境态。定期的进行标记变量的回收。
>
> ​	进入环境
>
> ​	执行环境
>
> ​	离开环境
>
> 引用计数
>
> ​	跟踪变量引用的次数。当一个上下文被清空后就不再引用了，那就计数减少1个。
>
> ​	减到0 时，自动清除

**3 减少垃圾的方案**

> 数组优化： 清空数组时，赋值一个[]  ===> length = 0;
>
> object 优化： 对象尽复用，减少深拷贝
>
> 函数优化：循环中的函数表达式，尽量统一放在外面。

































