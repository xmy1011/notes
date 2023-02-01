# Promise

promise A+ 规范

### 理解：

```js
// Promsie 的常用用法
function fetchSomeThing(url) {
    return new Promise((resolve,reject) => {
        setTimeout(() =>{
            resolve({data:'123'})
        },2000)
    })
}
```

1 Promise 是个构造函数

2 构造函数接收一个函数作为参数，这个函数的参数是两个函数

3 Promise 返回的对象，包含一个 then 函数，这个函数接收两个参数，也都是函数。

【**rules**】

promise 的status : 

​	pending ---> [resolve]---- >fulfilled

​	pending --->[reject]-----> rejected

状态一经更改不可再变。

2 then 函数

1 参数 `onfulfilled`, `onrejected`必须是函数类型，如果不是应该被忽略。

2 `onfulfilled`  `onrejected` 的特性

 	在promise 变成 `fulfilled`  / `rejected`  状态的时候，应该调用 `onFulfilled` / `onRejected`;

​	在 promise 变成 `fulfilled `  / `rejected` 状态之前，不应该调用  `onFulfilled` / `onRejected`;。

3 then  函数应该在 构造函数 Promise 的原型上，可供实例调用。

4  then 函数 ，只会在 resolve 之后调用；

  then 函数的第一个参数，是函数 onFulfilled，onFulfilled 参数为 resolve（this.value） 的值 this.value。

  then 函数的第二个参数，是函数 onRejected，onRejected参数为 reject (this.reason)的值 this.reason。

5 由 4 可知，若存在异步，比如（微任务中）setTimeout 在一定时间后触发 resolve({data: ' 1234 '})  ,但是带有.then()方法；

则then 方法执行之时， this.status 仍然为 `pending` 状态，则此时执行then方法会有问题，没有办法执行 

`onFulfilled`方法；

##### 发布订阅模式 

**因此 在执行到 then 方法时，若 此时的 status 仍为 pending ，需要收集 `onFulfilled` `onRejected`方法，分别在 resolve(this.value) 和 reject (this.reason)  之后再依次执行**。用两个数组，分别存放 两种方法。

resolve 或者 reject之后 ，从两个数组中取出方法，循环执行。**解决了 多次调用的问题。注意 ： 不是链式调用**



**6 then 方法的链式调用问题**

可知 ：then  方法，返回一个 Promise ,并且该 Promise resolve 的值作为 下一个 then 方法的 onFulfilled的参数。

```js
new Promise((resolve,reject) =>{
    ...
    resolve({data: '第一个promise'})
    ...
}).then((res) =>{
   ... 
   return result1;
}).then((res1) =>{
    ...
    return result2;
}).then((res2) =>{
    ...
    console.log(res2)
})
```



实现：

```js
class PromiseIm {
  //  默认返回 this 对象
  constructor(execute) {
    this.status = 'pending';
    this.value = undefined;
    this.reason = undefined;
    // 用来异步更改status 后 执行then方法里的俩函数 
    // 解决多次调用（注意不是链式调用）
    this.onFulfilledArray = [];
    this.onRejectedArray = [];
    //  箭头函数没有自己的this值，它是取上级作用域的this值作为自己的this值来使用
    //  当有then方法时，resolve 之后 执行 then 方法里的 onFulfilled
    let resolve = (value) => {
      queueMicrotask(() => {
        if (this.status === 'pending') {
          this.status = 'fulfilled';
          this.value = value;
          this.onFulfilledArray.forEach(item => item())
        }
      })

    }
    //  当有then方法时，reject 之后 执行 then 方法里的 onRejected 
    let reject = (err) => {
      queueMicrotask(() => {
        if (this.status === 'pending') {
          this.status = 'rejected';
          this.reason = err;
          this.onRejectedArray.forEach(item => item())
        }
      })

    }

    execute(resolve, reject)
  }

}

//  在原型上的方法，仅实例对象可调用，类不可。会去原型上查找该方法
//  then 方法应该返回一个 promise （链式调用）
PromiseIm.prototype.then = function (onFulfilled, onRejected) {
  //  俩参数必须是函数 否则应当忽略
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : data => data;
  onRejected = typeof onRejected === 'function' ? onRejected : reason => reason;
  //  调用 then 的时候，Promise 的实例 P 的status 已经从 pending 状态不可更改的变为了 fulfilled / rejected
  if (this.status === 'fulfilled') {
    return new PromiseIm((resolve, reject) => {
      try {
        const res = onFulfilled(this.value)
        resolve(res);
      } catch (error) {
        reject(error);
      }
    })
  };
  if (this.status === 'rejected') {
    return new PromiseIm((resolve, reject) => {
      try {
        const res = onRejected(this.reason)
        resolve(res);
      } catch (error) {
        reject(error);
      }
    })

  };
  /**
   * 2023年2月1日14:39:21 
   * 如果在构造函数 Promise内部执行异步操作，在一定时间后才会resolve(value) OR reject(reason)
   * 但是 如果又在实例后面执行 then 方法的话，此时 this.status 还未更改状态，因此会有问题.
   * **/
  /**
   * 构造函数 的 参数函数执行后 还没执行 resolve / reject 的时候，若 then方法执行： 
   * **/
  if (this.status === 'pending') {
    return new PromiseIm((resolve, reject) => {
      this.onFulfilledArray.push(() => {
        try {
          const res = onFulfilled(this.value);
          resolve(res)
        } catch (error) {
          reject(error)
        }
      })
      this.onRejectedArray.push(() => {
        try {
          const res = onRejected(this.reason);
          resolve(res)
        } catch (error) {
          reject(error)
        }
      })
    })
  }
}

const p = new PromiseIm((res, rej) => {
  setTimeout(() => {
    res({ data: 123 });// 执行 Promise类里的 resolve 方法 resolve函数时promise自己写的，但是这个函数需要外部给传进去value
  }, 2000)
  /**
   * value数据 从外部通过 resolve函数的参数 传给Promsie 的实例p， 在Promise内部就是this.value = value;
   * 如果同一个实例 再  then 的话 ，在 then 的内部是能 通过 this.value拿到最初 在外部调用 resolve 传进去的值
   * 总结： promise的 resolve 方法是为了获取外部的value值。用自身的方法去执行。
   * promise 的 then 方法，是为了获取外部传进去的函数， 把自己获取到的value或者reason当做参数去执行外部传进来的函数
   * **/
})
//  then 里面的函数，是外边传进去的，但是外面传进去的这个函数，可以拿到内部的resolve时的value 
//  then 是个微任务
p.then((res) => {
  console.log('res', res);
  console.log('rerere');
})
p.then((res) => {
  console.log('res111', res);
  console.log('rerere111');
})

const p2 = new PromiseIm((res, rej) => {
  rej('123') 
}).then(res1 => {
  return res1 + '456';
},(reason) => {
  return reason+ '789'
}).then(res2 => {
  console.log(res2);
})

// 第 132 行：
/**
 *  then  的 返回值 是 Promise resolve 的值m，m是前一个 then 里面 onFulfilled 返回的值，也就是132 的return值。
 *  但是  132行return 的值，可以是 Promise  function string number .... 需要进一步完善
 * 
 * **/ 
```

```js
123789
res { data: 123 }
rerere
res111 { data: 123 }
rerere111
```

2023年2月1日22:18:43

**对 链式调用 没有完全明白，在 then方法里 ，返回的 promise**

明天去换前车窗玻璃 我爸妈说7点出发，我要睡觉了 ，希望起得来。 







































































