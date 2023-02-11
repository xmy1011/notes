### hooks 用过吗， 聊聊函数组件和类组件的区别。

类组件是使用ES6的class来定义的组件。 函数组件是接收一个单一的`props`对象，并返回一个 React 元素。

关于 类（class）API 和 钩子（hooks）API ,官方更推荐使用函数，因为函数更简洁，代码量少， 更符合react 函数式的本质。

#### 区别：

函数组件的性能比类组件的性能要高，因为类使用的时候要实例化，函数组件则直接执行函数返回结果即可。

- **1 状态的有无**

  hooks出现之前，函数组件`无状态`，`无this`, `无实例`, `没有生命周期`。所以我们称函数组件为状态组件。

  hooks出现之前， react的函数组件只负责考虑UI的渲染。没有自身的状态和业务逻辑代码， 是一个纯函数。它的输出只由参数props决定，不受其他任何因素的影响。

- **调用方式的不同**

  函数组件重新渲染，将重新调用组件方法返回新的 react 元素。

  类组件重新渲染，将new 一个新的组件实例，然后调用render类方法返回react 元素。

**React Hooks **

```js
useState() // 状态钩子
useContext() // 共享状态钩子
useReducer() // action 钩子
useEffect() // 副作用钩子
```

- **useEffect**

  useEffect 允许我们再函数组件中执行副作用。可以模拟 componentDidMount、componentDidUpdate、

  和 componentWillUnmount 。

- **useState**

  useState 可以使函数组件像类组件一样拥有state，函数组件可以通过useState 让组件重新渲染，更新视图。

- **useReducer**

  useReducer 是可以在无状态组件中运行的类似redux 的 功能 api。

  ```js
  //  这个函数是一个惰性初始化函数，可以用来进行复杂的计算，然后返回最终的 initialState
  const [state, dispatch] = useReducer(reducer, initialState, init); 
  ```


- **useCallback** （redux-demo/7_hooks/CallbackIndex.jsx）

```jsx
const Button = ({onClickButton, count , children}) => {
  return (
    <div>
      <button onClick={onClickButton}>{children}</button>

      <span>{Math.random()}</span>
      <span>{count}</span>
    </div>
  )
}
// 加 React.memo 是当props 不发生变化时，组件就不会重新渲染。
export default React.memo(Button)


import React, {useState, useCallback } from 'react'
import Button from './Button';

export default function CallbackIndex() {


  const [count1, setCount1] = useState(0)
  const [count2, setCount2] = useState(0)
  const [count3, setCount3] = useState(0)

//  只要父组件重新渲染（状态或者props更新） 就会导致声明出一个新的方法，新的方法尽管和旧的方法长得一样，但是依旧是两个不同的对象。
//  React.memo 对比后发现 对象 props 改变了，就重新渲染了
  const handleBtnClick1 = () => {
    setCount1(count1+1)
  }
  //  在count2 依赖项发生改变是 ，去返回一个新的函数
  //  由于我们的 这个方法 只依赖了 count2 变量，而且count2 只在点击Button2后才会更新handleBtnClick2 所以就导致了我们点击 Button1 不重新渲染 Button2的内容
  const handleBtnClick2 = useCallback(() => {
    setCount2(count2+1)
  }, [count2]) 


  // 空数组 意味着这个方法将永远不会被更新
  //  多次点击Button2 查看变化，会发现 Button2 后面的值只会改变一次，是因为上述函数内的count2 永远都是0， 就意味着每次都是 0 + 1,
  //  Button 所接受的 count props, 也只会从 0 到 1 且一直都将是1 ，而且 handleClickBtn2New 也因为没有依赖项不会返回新的方法，导致 Button 组件只会因 count 改变而更新一次
  const handleBtnClick2New = useCallback(() => {
    setCount2(count2+1)
  }, []) 


  return (
    <>
      <Button onClickButton={handleBtnClick1}>Button1</Button>
      <Button onClickButton={handleBtnClick2}>Button2</Button>
      <Button count={count2} onClickButton={handleBtnClick2New}>Button2New</Button>
      <Button onClickButton={() => {
        setCount3(count3+1)
      }}>Button3</Button>
    </>
  )
}
```

简单来说就是返回一个函数，只有在依赖项发生变化的时候才会更新（返回一个新的函数）;

**可以用useCallback 包裹一个传递给子组件的方法，这样可以避免自身状态更新的时候，子组件的重复渲染 。**

useCallback 是要配合**子组件**的 **shouldComponentUpdate** 或者 **React.memo** 一起来使用的，否则就是反向优化。

-  （useCallback返回的是缓存函数，也就是旧函数，不代表新函数（第一个参数）不会被创建。既然会创建，如果不配合React.memo，确实是反向优化）

- （没经过优化的组件父组件更新参数不变，子组件也会更新，可以使用memo、shouldComponentUpdate、Pure Component ...）

- **useMemo**

useMemo 与 useCallback 很像， 根据上述 useCallback 已经可以想到 useMemo 也**能针对传入子组件的值**进行缓存优化。

可以把一些**昂贵的计算逻辑**放到 useMemo 中，只有当依赖值发生改变的时候才去更新。

https://juejin.cn/post/7165338403465068552#heading-8

```js
import React, {useState, useEffect, useMemo} from 'react'


function useTime() {
  const [time, setTime] = useState(new Date().getSeconds());

  useEffect(() => {
    const intervalId = window.setInterval(() => {
      setTime(new Date().getSeconds())
    }, 1000);

    return () => {
      window.clearInterval(intervalId)
    }
  }, []);


  return time;

}


function isPrime(n){
  const max = Math.ceil(Math.sqrt(n));


  if(n === 2) return true;

  for(let counter = 2 ; counter<=max ; counter ++) {
    if(n% counter === 0) return false
  }
  return true
}


const UseMemoIndex1 = () => {

  const [selectedNum, setSelectedNum] = useState(100);

  const time = useTime();

  const allPrimes = useMemo(() => {
    const result = [];
    for(let counter = 2; counter < selectedNum ; counter++){
      if(isPrime(counter)) {
        result.push(counter)
      }
    }
    return result;
  }, [selectedNum]);

  return (
    <div>

      <p className="clock">
        {time}
      </p>
      <form>
        <label htmlFor="num">Your number:</label>
        <input
          type="number"
          value={selectedNum}
          onChange={(event) => {
            // 为了防止电脑烧起来，我们限制一下传入的值最大为 100k
            let num = Math.min(100_000, Number(event.target.value));
            
            setSelectedNum(num);
          }}
        />
      </form>
      <p>
        There are {allPrimes.length} prime(s) between 1 and {selectedNum}:
        <span>
          {allPrimes.join(', ')}
        </span>
      </p>

    </div>
  )
}

export default UseMemoIndex1;
```

我们可以将 useMemo 的返回值定义为返回一个函数这样就可以变通的实现了 useCallback

**结语**

useCallback 与 useMemo 一个缓存的是函数，一个缓存的是函数值。 useCallback 是来优化子组件的，防止子组件的重复渲染。 useMemo 可以优化当前组件也可有优化子组件，优化当前组件主要是通过 memorize 来将一些复杂的计算逻辑进行缓存，如果只是进行一些简单的计算也没必要使用。

**useRef 和 useImperativeHandler 的一个使用实例**

`useRef`会生成一个包含current属性的对象，它能够在react渲染全生命周期内保持地址不变，

- useRef 返回一个可变的 ref 对象，其 `current` 属性被初始化为传入的参数（initValue）；

  ```js
  const ref = useRef(initValue);
  ```

  **useRef  返回的ref对象，在组件的整个生命周期内保持不变。也就是说在每次重新渲染组件时，返回的ref对象都是同一个，（使用 React.createRef, 每次重新渲染都会创建 ref）**

  **作用二：**获取dom元素 比较常用

  我们也许会用到ref，ref主要用于传递给子组件，并拿到对应子组件的实例

  通过 useRef 创建一个变量，进行保存。 (domRef)

  在jsx中 通过 ref={domRef} 给对应元素节点添加属性。

  在页面挂载后，通过 domRef.current 就可以获取对应节点的真实dom元素了。



  上面是通过`forwardRef`把`Child`函数包起来，然后传入第二个参数`ref`最后挂载`ref={ref}` 这样就可以拿到对应的DOM了

  ```jsx
  function CustomTextInput(props) {
    // 这里必须声明 textInput，这样 ref 才可以引用它
    const textInput = useRef(null);
  
    console.log(textInput);
    function handleClick() {
      textInput.current.focus();
    }
    return (
      <div>
        <Child ref={textInput} />  //**依然使用ref传递**
        <input type="button" value="Focus the text input" onClick={handleClick} />
      </div>
    );
  }
  const Child = forwardRef((props, ref) => {  //** 看我 **
    return <input type="text" ref={ref} />;//** 看我挂到对应的dom上 **
  });
  ```



  ```jsx
  import React, { useEffect, useRef } from 'react';
  
  export default function Demo() {
    const domRef = useRef<HTMLInputElement>(null);
  
    useEffect(() => {
      domRef.current?.focus();
      console.log(domRef,'domRef')
    });
  
    return (
      <div>
        <input ref={domRef} type="text" />
        <button>增加</button>
      </div>
    );
  }
  ```


```jsx
import React, {useRef, useImperativeHandle } from 'react'

const UseImperativeHandler = ({rref}) => {
  
  useImperativeHandle(rref,() => {
    return {
      log: () => {console.log('我是log');}
    }
  })

  return (
    <div>UseImperativeHandler</div>
  )
}

const Parent = () => {

  const rref = useRef()

  return (
    <>
      <button onClick={() => {
        rref.current.log()
      }}>调用子组件方法</button>
      <UseImperativeHandler rref={rref} />
    </>
  )
}

export default Parent

```

### React 组件通信方式常见的几种情况

- 父组件向子组件通信 props
- 子组件向父组件通信
- 跨级组件通信
- 非嵌套关系的组件通信

1） 父组件向子组件通信 ----props

2)   子组件向父组件

props+ 回调的方式。父组件可以在props中给子组件传递一个需要参数的函数。子组件调用该函数时就可以给父组件传参了。

子组件也可以通过 ref 和useImperativeHandler 的方法，主动向父组件暴露函数，让父组件执行。官方不推荐，因为不安全。

3） 跨级组件通信

即父组件向子组件的子组件通信，向更深层子组件通信。

- 使用props逐层传递。但是如果父组件结构较深，且有的中间层组件不需要该props 的话 则不适合。

- 使用 context ； context 相当于一个大容器，我们可以把要通信的内容，放到这个容器里面，这样不管嵌套多深，都可以随意取用。 

  Context 的设计目的， 是为了共享那些 对于一个组件树而言是 “全局” 的数据。

  为了以防在父组件中更改 context数据时造成中间没有用到Context数据的子组件的重复渲染，建议中间子组件用Memo包裹。

  用法：

  ```jsx
  1 定义一个Context 需要导出，在父组件及需要用到它中数据的深层子组件中引入。
  const InfoContext = CreateContext();
  2 父组件中的也用法
  function Parent(){
      const [name, setName] = useState('lily')
      const ValueToChild = {
          name: name
      }
      
      return (
      	<>
              <InfoContext.Provider value={ValueToChild}>
  				<Child />
              </InfoContext.Provider>
          	<button onClick={() => {setName(name + '1' )}}>更改name</button>
          <>
      )
      
  }
  3 子组件中的写法
   const getContextInfo = useContext(InfoContext);
  ```

4） 非嵌套关系的组件通信

- 可以使用 自定义事件通信，发布订阅模式。
- 可以通过redux 等进行全局状态管理
- 如果是兄弟组件通信，可以借用共同的父节点，实现通信。

### react虚拟dom  与 dom Diff 

**什么是虚拟dom**

虚拟dom 是一个能表示dom树的对象，通常含有 `标签名`，`标签上的属性`、`时间监听`、`子元素们`， 以及其他的属性。

```js
const vNode = {
    key: null, 
    props:{
        className: '', 
        onClick: () =>{},
        children: [
            {type: span, props:{}， }
        ]
    },
    type: "div", 
    ref: null
}
```

**有什么优点**

优点是能减少不必要的 DOM 操作。

> 1 虚拟 DOM 可以将多次操作合并为一次操作。比如 添加 1000 个节点，却是一个接一个的操作的（减少DOM操作的次数）
>
> 2 虚拟DOM 借助 DOM Diff 可以把多余的操作省略掉，比如你添加1000 个节点，其实只有10个是新增的（减少 DOM 操作的范围）。

能跨平台渲染。

> 虚拟 DOM 不仅可以变成  DOIM，还可以变成小程序，ios ,安卓 ，因为 虚拟dom本质上是一个js对象

**有什么缺点**

需要额外的创建函数，需要babel对jsx语法进行转义，需要额外的构建过程。

**真实DOM 和虚拟 dom 谁更慢**

js**插入dom**用时是很快的，但是浏览器在**渲染页面**时会让页面不可交互。

在数据规模比较合理的范围内，用虚拟dom是比较好的，因为他可以优化多余的操作。

但是当规模大到十万节点的更新时，会比较慢。



**DOM Diff 虚拟dom的对比算法**

我们的dom树在不断变动



























































