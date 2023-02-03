#### 状态为什么要管理

#### 状态如何管理

#### 一个管理状态的库，应该具备哪些能力



## 什么是状态

web 是一个状态机。

我们不关心流程，我们关心的是界面处于哪个状态。

状态管理， 在 web 生命周期变化的时候，数据model所呈现出来的view。

​	——生命周期

 	——状态变化

​	——状态model 怎么样来触发 view 变化

​	——全局状态管理

—生命周期： 从打开浏览器，输入地址，访问页面， 一直到下一次界面刷新的时候。

—闭包可实现。

### 我们在设计一个数据的时候，要考虑哪些事情？

软件的本质， 其实就是**数据管理**。（比如游戏：升级，从一个数据状态到另一个数据状态，数据变化，页面上的展示变化）

我们在设计一个状态的时候，实际上设计一个数据；。设计这个数据（状态）它存在的生命周期和作用范围。

数据的生命周期：

—数据存在于数据库DB内，用户若还在，数据就还在。

—数据存在于local / session 。（比如在一局游戏里面买的装备，这个装备数据就只存在于这个会话里面 ，再开一局不能用上一局里的装备）这个会话还在，状态就还在。

—project runtime

—路由级别 的  page

—组件级别的 component 这个组件销毁了 数据也就没有了

###  和 redux 生命周期一致的数据 ，还有哪些？

```js
const  deps ={};
function create() {
    deps.value = 'hello';
}

export default create();
// 把这个函数导出，放到别处执行。那么 deps.value === 'hello ',变量deps.value一直不会消失 
// 闭包。最外层相当于一个 function
```

闭包回收： https://blog.csdn.net/qq_25506089/article/details/119248749



```js
 dispatch({type: Symbol()})

// createStore 的时候，用一个不匹配任何 type 的 action，来触发 state = reducer(state, action)

// 因为 action.type 不匹配，每个子 reducer 都会进到 default 项，返回自己初始化的 state，这样就获得了初始化的 state 树了
```

可参考的文章及github： https://blog.csdn.net/qq_36376434/article/details/101443753**



**高阶函数  和 函数柯里化 和普通函数 可参考文章：https://blog.csdn.net/weixin_43677405/article/details/114799637**

**函数柯里化**：通过函数调用继续**返回函数**的方式，实现**多次接收参数最后统一处理**的函数编码形式。

**高阶函数**：如果一个函数符合下面2个规范中的 **任何一个** ，那该函数就是高阶函数。

​	A函数，**接收的参数是一个函数**，那么A就可以称之为高阶函数。
​	若A函数，调用的**返回值依然是一个函数**，那么A就可以称之为高阶函数。
**常见的高阶函数有**：

Promise：  new Promise(()=>{ })
setTimeout:new setTimeout(()=>{ })
数组里一些常见的函数，如arr.map()等等

**中间件及具体中间件的实现**

http://grapevinelin.cn/2021/10/02/shou-xie-yi-ge-ji-jian-redux-he-zhong-jian-jian/