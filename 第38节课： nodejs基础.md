### Node 怎么学

熟悉/熟练/精通 node

可以从这几个角度入手：

——模块 koa / express ORM: typeORM sequelize

更多的是一种后端的**编程思路**

前端的编程： 声明式编程

后端： 命令式编程

```java
// java
class Test {
    public static void main(String[] args){
        // run 
    }
}
```

#### node 学了干嘛

不设边界。

#### node 简介

**node是不是单线程的？**

一定要搞清楚一个 宿主环境 

什么是宿主环境？

```
// index.js
console.log('hello word')
/**
直接用node去执行这个js
node index.js
**/
// 把这个js引入到一个html界面里  浏览器console也会打印出来
```

**两个过程**

1 console.log('hello word') 这个字符串 需要被解析

​	1 需要被解析 console是个对象 log是个函数 hello word 是个字符串作为参数

​	2 需要被执行 hello world 这句话被打出来

1 console.log(document.getElementByTagName('head'))[0] 这个字符串 需要被解析

​	1 需要被解析 console是个对象 log是个函数 document.getElementByTagName('head')[0] 是个字符串作为参数

​	2 需要被执行 hello world 这句话被打出来

解析这件事 V8 在做

是否能执行 是宿主环境决定

eg：

setTimeout(() =>{

})