## 1. 说说



### 1. Vue构造函数的初始化

### 2. Vue全局静态属性和方法的初始化

v-diff

key

### 3.Vue的核心方法实现

- Vue.mixin

```js
Vue.options = {
	components: {
		KeepAlive
		Transition,
    	TransitionGroup
	},
	directives:{
	    model,
        show
	},
	filters: Object.create(null),
	_base: Vue
}
```

```js
//Provider，父组件提供数据
provide: {
    data: 'test provide'
},
//inject,子组件拿数据      
inject: ['data']
```



### 4. 子组件的创建可能是通过Vue.extends方法

### 5. parentVal` 应该是 `Vue.options.created 主要是options中的东西

### 6. 子组件的创建

Vue.mixin的实现

生命周期的执行

### 7. $options会存储Vue.mix()全局和局部混入的created方法

**此时的created是一个数组**

`mergeOptions` 函数是如何对父子选项进行合并处理的，也知道了它的作用

Vue.config.optionMergeStrategies 选项合并中的策略对象

```js
// 子组件
var ChildComponent = {
  created () {
    console.log(this.$options.parent)
  }
}

var vm = new Vue({
    el: '#app',
    components: {
      // 注册组件
      ChildComponent
    },
    data: {
        test: 1
    }
})
```

上面的代码中，我们的子组件 `ChildComponent` 说白了就是一个 `json` 对象，或者叫做组件选项对象，在父组件的 `components` 选项中把这个子组件选项对象注册了进去，实际上在 `Vue` 内部，会首先以子组件选项对象作为参数通过 `Vue.extend` 函数创建一个子类出来，然后再通过实例化子类来创建子组件，而 `createComponentInstanceForVnode` 函数的作用，在这里大家就可以简单理解为**实例化子组件**，只不过这个过程是在虚拟DOM的 `patch` 算法中进行的，我们后边会详细去讲

![](https://cn.vuejs.org/images/lifecycle.png)

created
beforeMounted
render
rmounted

 **Object.getOwnPropertyDescriptor(obj, 'name')**

1. *{value: "hwt", writable: true, enumerable: true, configurable: true}*

2. ```js
   {
       configurable: true,
       enumerable: true,
       value: "hwt",
       writable: true  
   }
   ```

3. 

4. dep.depend()

5. dep.notify()

6. **实际上完整版 `Vue` 的 `$mount` 函数要做的核心事情就是编译模板(`template`)字符串为渲染函数，并将渲染函数赋值给 `vm.$options.render` 选项**

**触发set事件后，会得到dep.notify**

**得到watch实例，重新出发啊this.get()  => this.getters => 触发render函数**





我们知道任务队列并非只有一个队列，在 `node` 中更为复杂，但总的来说我们可以将其分为 `microtask` 和 `(macro)task`，并且这两个队列的行为还要依据不同浏览器的具体实现去讨论，这里我们只讨论被广泛认同和接受的队列执行行为。当调用栈空闲后每次事件循环只会从 `(macro)task` 中读取一个任务并执行，而在同一次事件循环内会将 `microtask` 队列中所有的任务全部执行完毕，且要先于 `(macro)task`。另外 `(macro)task` 中两个不同的任务之间可能穿插着UI的重渲染，那么我们只需要在 `microtask` 中把所有在UI重渲染之前需要更新的数据全部更新，这样只需要一次重渲染就能得到最新的DOM了。恰好 `Vue` 是一个数据驱动的框架，如果能在UI重渲染之前更新所有数据状态，这对性能的提升是一个很大的帮助，所有要优先选用 `microtask` 去更新数据状态而不是 `(macro)task`，这就是为什么不使用 `setTimeout` 的原因，因为 `setTimeout` 会将回调放到 `(macro)task` 队列中而不是 `microtask` 队列，所以理论上最优的选择是使用 `Promise`，当浏览器不支持 `Promise` 时再降级为 `setTimeout`。如下是 `next-tick.js` 文件中的一段代码：

**setImmediate**



相信大家应该了解过 `Web Workers`，实际上 `Web Workers` 的内部实现就是用到了 `MessageChannel`，一个 `MessageChannel` 实例对象拥有两个属性 `port1` 和 `port2`，我们只需要让其中一个 `port` 监听 `onmessage` 事件，然后使用另外一个 `port` 的 **`postMessage`** 向前一个 `port` 发送消息即可，这样前一个 `port` 的 `onmessage` 回调就会被注册为 `(macro)task`，由于它也不需要做任何检测工作，所以性能也要优于 `setTimeout`。总之 `macroTimerFunc` 函数的作用就是将 `flushCallbacks` 注册为 `(macro)task`

Dep.target ？ watcher

，正是因为 watcher 对表达式的求值，触发了数据属性的 get 拦截器函数，从而收集到了依赖，当数据变化时能够触发响应。在上面的代码中 Watcher 观察者实例将对 updateComponent 函数求值，我们知道 updateComponent 函数的执行会间接触发渲染函数(vm.$options.render)的执行，而渲染函数的执行则会触发数据属性的 get 拦截器函数，从而将依赖(观察者)收集，当数据变化时将重新执行 updateComponent 函数，这就完成了重新渲染。同时我们把上面代码中实例化的观察者对象称为 渲染函数的观察者。

```js
const vm = new Vue({
    el: '#app',
    data() {
        return {
            name: 'xiaoming',
        }
    },
    computed: {
        text() {
            return `Hello, ${this.name}!`
        }
    },
    methods: {
        changeName() {
            this.name = 'onlyil'
        },
    },
}
```

一元标签

非一元标签

编译器

抽象语法树

```js
const html = '<div v-for="obj of list" class="box"></div>'
element = {
  type: 1,
  tag: 'div',
  attrsList: [
    {
      name: 'v-for',
      value: 'obj of list'
    },
    {
      name: 'class',
      value: 'box'
    }
  ],
  attrsMap: {
    'v-for': 'obj of list',
    'class': 'box'
  },
  parent,
  children: []
}
```

```js
<div v-if="a"></div>
<p v-else-if="b"></p>
<span v-else></span>
{
  type: 1,
  tag: 'div',
  ifConditions: [
    {
      exp: 'b',
      block: { type: 1, tag: 'p' /* 省略其他属性 */ }
    },
    {
      exp: undefined,
      block: { type: 1, tag: 'span' /* 省略其他属性 */ }
    }
  ]
  // 省略其他属性...
}
```

源码讲解: https://github.com/lihongxun945/myblog/issues/29

### 问题

## **在进行render函数渲染之前的准备工作，解析模板字符串为抽象语法树，render函数用来将抽象语法书进行渲染成虚拟Dom节点

`with(this){return ${code}}`

"with(this){return _c("div",{attrs:{"id":"app"}},[_v("\n "+_s(text)+"\n ")])}"

new Function(code) == 相当于eval

```js

export function generate (ast) {
  const code = genElement(ast);
  return new Function (`with (this) { return ${code}}`);

```



