[TOC]

# 一．Lesson1

## 1. 初识React

### 什么是React？

React是由**Facebook**研发的、用于**解决UI复杂度**的开源**JavaScript库**，目前由React联合社区维护。

> 它不是框架，只是为了解决UI复杂度而诞生的一个库

### React的特点

- 轻量：React的开发版所有源码（包含注释）仅3000多行
- 原生：所有的React的代码都是用原生JS书写而成的，不依赖其他任何库
- 易扩展：React对代码的封装程度较低，也没有过多的使用魔法，所以React中的很多功能都可以扩展。
- 不依赖宿主环境：React只依赖原生JS语言，不依赖任何其他东西，包括运行环境。因此，它可以被轻松的移植到浏览器、桌面应用、移动端。
- 渐近式：React并非框架，对整个工程没有强制约束力。这对与那些已存在的工程，可以逐步的将其改造为React，而不需要全盘重写。
- 单向数据流：所有的数据自顶而下的流动
- 用JS代码声明界面
- 组件化

### 对比Vue

|   对比项   |  Vue  | React |
| :--------: | :---: | :---: |
| 全球使用量 |       |   ✔   |
| 国内使用量 |   ✔   |       |
|    性能    |   ✔   |   ✔   |
|   易上手   |   ✔   |       |
|   灵活度   |       |   ✔   |
|  大型企业  |       |   ✔   |
| 中小型企业 |   ✔   |       |
|    生态    |       |   ✔   |

### 学习路径

整体原则：熟悉API --> 深入理解原理

1. React
   1. 基础：掌握React的基本使用方法，有能力制作各种组件，并理解其基本运作原理
   2. 进阶：掌握React中的一些黑科技，提高代码质量
2. React-Router：相当于vue-router
3. Redux：相当于Vuex
   1. Redux本身
   2. 各种中间件
4. 第三方脚手架：umi
5. UI库：Ant Design，相当于Vue的Element-UI 或 IView
6. 源码部分
   1. React源码分析
   2. Redux源码分析

### react的库

- **react库是react的核心库，用来创建react虚拟对象**
- **react-dom库用以配合核心库进行网页端开发**
- **babel用以将JSX语法解析为JS**


直接在页面上使用React，引用下面的JS

```html
​```crossorigin 用于跨域的时候提示错误
<!--
    引入React的核心库 用来创建React虚拟对象
    不依赖宿主环境(网页),只依赖原生JS语言
-->
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>

<!-- 用以将配合核心库进行网页端开发 -->
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>

<!-- 将JSX语法解析为JS JS的扩展语法，需要使用babel进行转义 -->
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
```

### React.createElement

创建一个React元素，称作虚拟DOM，本质上是一个对象

1. 参数1：元素类型，如果是字符串，一个普通的HTML元素
2. 参数2：元素的属性，一个对象
3. 后续参数：元素的子节点
    let h1 = React.createElement('h1',{
        class:'dom'
    },'ssss')
    
### JSX

JS的扩展语法，需要使用babel进行转义。

## 2. React脚手架

### 1.　使用yarn进行安装 react-learn工程
> (1) yarn create react-app react-learn

### 2.  React脚手架中中index.js怎么被index.html文件引用

> HtmlWebpackPlugin
> 如果你有多个 webpack 入口点， 他们都会在生成的HTML文件中的 script 标签内。
>  https://webpack.docschina.org/plugins/html-webpack-plugin/

### 3. 凡是使用JSX的文件，必须导入React

### 4. 开发环境搭建

> (1) VSCode配置
 - emmet配置：
 -  "javascript": "javascriptreact" //让js支持jsx的语法

> (2) VSCode插件安装
 -  ESLint：代码风格检查
 -  ES7 React/Redux/GraphQL/React-Native snippets：快速代码编写

> (3) Chrome插件安装
  	> react Developer Tools

### 使用脚手架
- 官方: create-react-app
- 第三方: next.js、umi.js



# 二．Lesson2

## 1. JSX

### 什么是JSX

- Facebook起草的JS扩展语法
- 本质是一个JS对象，会被babel编译，最终会被转换为React.createElement
- 每个JSX表达式，有且仅有一个根节点
  1. 添加一个空的根节点,不会反应到真实的DOM中,所以不会影响页面结构
  < >
    <div></div>
    <div></div>
  </>
  
  - React.Fragment ===  < ></>
- 每个JSX元素必须结束（XML规范）
  例如 img、input等标签必须要结束(</img></input>)
  或者 <img /> 这种写法也可以

### 在JSX中嵌入表达式
   <div > 
        {a} * {b}
   </div>

原理:
   React.createElement('div',{},`${a}*${b}`)

- 在JSX中使用注释
- 将表达式作为内容的一部分
  - null、undefined、false不会显示
  - 普通对象，不可以作为子元素 例如obj = {a:1,b:2}就不能作为子元素
  - 可以放置React元素对象
  - 可以放置数组在子元素中,会把数组中每个元素作为子元素加进来 {arr}
- 将表达式作为元素属性 className={name} src={url} style={ { marginLeft:"50px" } }
- 属性使用小驼峰命名法
- 防止注入攻击
  - 自动编码
  - dangerouslySetInnerHTML

### 元素的不可变性 (React元素中的所有属性都不可修改)

- 虽然JSX元素是一个对象，但是该对象中的所有属性不可更改
- 如果确实需要更改元素的属性，需要重新创建JSX元素

## 2. 组件和组件属性

组件：包含内容、样式和功能的UI单元

### 创建一个组件 
--
 function MyComponent() {
     return (
         <h1>我是一个组件</h1>
     )
 }

-- 组件使用
   - 执行函数{MyComponent()}  (很少用)
   - 当成元素来使用 <Mycomponent></MyComponent>

-- 使用组件生成的仍然是一个React元素,变化的只是type值

-- 快速生成组件的快捷键

**特别注意：组件的名称首字母必须大写**



1. 函数组件
   

返回一个React元素

2. 类组件

必须继承React.Component

必须提供render函数，用于渲染组件
render方法必须返回React元素

-- class Demo extend React.Component {
    render() {
        return (
            <div></div>
        )
    }
}


###  组件的属性

1. 对于函数组件，属性会作为一个对象的属性，传递给函数的参数
2. 对于类组件，属性会作为一个对象的属性，传递给构造函数的参数

注意：组件的属性，应该使用小驼峰命名法

**组件无法改变自身的属性**。

之前学习的React元素，本质上，就是一个组件（内置组件）
- 首字母小写的时候就是一个内置组件(<div></div>)
- 首字母大写的时候就是自定义的函数或者类组件
- 组件的所有属性都不可修改

React中的哲学：数据属于谁，谁才有权力改动

**React中的数据，自顶而下流动**


## 3. 组件状态

组件状态：组件可以自行维护的数据

组件状态仅在类组件中有效

状态（state），本质上是类组件的一个属性，是一个对象

**状态初始化**
  1. - 构造函数中进行状态的初始化

    constructor (props) {
        super(props);
        // 初始化状态
        this.state = {
    
        }
    }
  2. - 在构造函数之外状态初始化 (Es7中提案,在构造函数之外也可以进行私有属性的初始化)
      state = {

   }

**状态的变化**

不能直接改变状态：因为React无法监控到状态发生了变化

必须使用this.setState({})改变状态 //重新设置状态后会触发组件的重新渲染

会将this.setState({

})对象和this.state对象中的值进行混合

- 就是如果新{}有this.state中相同的属性就覆盖之前的值
- 没有就添加新的属性

**一旦调用了this.setState，会导致当前组件重新渲染**

**组件中的数据**

1. props：该数据是由组件的使用者传递的数据，所有权不属于组件自身，因此组件无法改变该数组
2. state：该数据是由组件自身创建的，所有权属于组件自身，因此组件有权改变该数据


## 4. 事件

在React中，组件的事件，本质上就是一个属性

按照之前React对组件的约定，由于事件本质上是一个属性，因此也需要使用小驼峰命名法

**如果没有特殊处理，在事件处理函数中，this指向undefined**

1. 使用bind函数，绑定this
2. 使用箭头函数

- 普通Dom元素绑定事件
    let btn = <button onClick={()=>{
        console.log('点击我')
    }}>点击我</button>

- 组件绑定事件,组件的事件,本质上就是一个属性

```js
    // 如果不使用箭头函数,事件处理函数的this默认值为undefined
    handleOver= () => {
        console.log(222,this)
        this.setState({
            isOver:true,
        })
    }
    demo(){ //这种写法当成属性传递给子组件
            //在子组件中调用他,此时他的this指向为该组件的React元素对象
        console.log('111',this)
    }
```

## 5. 深入认识setState

- setState，它对状态的改变，**可能**是异步的

> **如果改变状态的代码处于某个HTML元素的事件中，则其是异步的，否则是同步**

- 如果遇到某个事件中，需要同步调用多次，需要使用函数的方式得到最新状态

- setState第一个参数还可以是函数
  this.setState( cur=>{ //**cur** 表示当前的状态
                        //该函数的返回结果,会混合(覆盖)掉之前的状态
                        //该函数也是异步执行
        return {
            n: cur.n + 1,
        }
  })

> 这种函数式写法,cur的值是值的信任的
> cur就是上一个函数状态改变后传给下一个函数的参数
> 等到前面状态发生改变之后才会运行该函数
  this.setState(cur=>{
      n: cur.n + 1,
  })

> 下面这种写法,无法解决状态多次改变问题
 this.setState({
    n: this.state.n + 1
},()=> { //在状态发生变化之后就会触发该回调函数
         //该回调函数运行在render函数之后
})

**最佳实践：**

1. 把所有的setState当作是异步的
2. 永远不要信任setState调用之后的状态
3. 如果要使用改变之后的状态，需要使用回调函数（setState的第二个参数）
4. 如果新的状态要根据之前的状态进行运算，使用函数的方式改变状态（setState的第一个函数）


React会对异步的setState进行优化，将多次setState进行合并（将多次状态改变完成后，再统一对state进行改变，然后触发render）

## 6. 生命周期

生命周期：组件从诞生到销毁会经历一系列的过程，该过程就叫做生命周期。React在组件的生命周期中提供了一系列的钩子函数（类似于事件），可以让开发者在函数中注入代码，这些代码会在适当的时候运行。

**生命周期仅存在于类组件中，函数组件每次调用都是重新运行函数，旧的组件即刻被销毁**

- 研究的是每个组件实例对象的生命周期

## 旧版生命周期

React < 16.0.0![assets/oldlife.png](/home/hwt/桌面/Note/assets/oldlife.png)



1. constructor //每个组件的实例对象只会创建一次
   1. 同一个组件对象只会创建一次
   2. 不能在第一次挂载到页面之前，调用setState，为了避免问题，构造函数中严禁使用setState
2. componentWillMount
   1. 正常情况下，和构造函数一样，它只会运行一次
   2. 可以使用setState，但是为了避免bug，不允许使用，因为在某些特殊情况下，该函数可能被调用多次
3. **render**
   1. 返回一个虚拟DOM，会被挂载到虚拟DOM树中，最终渲染到页面的真实DOM中
   2. render可能不只运行一次，只要需要重新渲染，就会重新运行
   3. 严禁使用setState，因为可能会导致无限递归渲染
4. **componentDidMount**
   1. 只会执行一次
   2. 可以使用setState
   3. 通常情况下，会将网络请求、启动计时器等一开始需要的操作，书写到该函数中
5. 组件进入活跃状态
6. componentWillReceiveProps
   1. 即将接收新的属性值
   2. 参数为新的属性对象
   3. 该函数可能会导致一些bug，所以不推荐使用
7. **shouldComponentUpdate**
   1. 指示React是否要重新渲染该组件，通过返回true和false来指定
   2. 默认情况下，会直接返回true
8. componentWillUpdate
   1. 组件即将被重新渲染
9. componentDidUpdate
   1.  往往在该函数中使用dom操作，改变元素
10. **componentWillUnmount**
    1.  通常在该函数中销毁一些组件依赖的资源，比如计时器

## 新版生命周期

React >= 16.0.0

React官方认为，某个数据的来源必须是单一的

![assets/newlife.png](/home/hwt/桌面/Note/assets/newlife.png)

1. getDerivedStateFromProps
   1. 通过参数可以获取新的属性和状态
   2. 该函数是静态的
   3. 该函数的返回值会覆盖掉组件状态
   4. 该函数几乎是没有什么用
2. getSnapshotBeforeUpdate
   1. 真实的DOM构建完成，但还未实际渲染到页面中。
   2. 在该函数中，通常用于实现一些附加的dom操作
   3. 该函数的返回值，会作为componentDidUpdate的第三个参数










