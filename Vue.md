## 1. 疑问点

- **Vue中的数据通信原理**

 ## 2. vue脚手架

vue create vue-app

## 3. Vue.use

***Vue.use()***使用自定义组件的时候

  需要自定义***install***属性的函数或者方法

才能在全局使用组件中的方法

Vue.use() 方法至少传入一个参数，该参数类型必须是 Object 或 Function，如果是 Object 那么这个 Object 需要定义一个 install 方法，如果是 Function 那么这个函数就被当做 install 方法。在 Vue.use() 执行时 install 会默认执行，当 install 执行时第一个参数就是 Vue，其他参数是 Vue.use() 执行时传入的其他参数。

**vue模板解析是用正则匹配的吗**

## 4. 美团源码分析

https://tech.meituan.com/2017/04/27/vuex-code-analysis.html

## 5. Vue源码

http://caibaojian.com/vue-design/art/

```js
//只可读，不可写
Object.defineProperty(res,'name',{
    get(){
        console.log('get')
        return obj.name;
    }
})
```



$listeners,$attrs,slot

/home/hwt/桌面/Learn/Demo/Vue/vue-demo























------------


# 分析源码的意义

- **深入**理解技术原理
- 学习各种优质解决方案
- 冲击高薪

# vue2响应系统

vue2响应系统由两个核心部件组成：

- 数据响应部件：该部件的作用是将一个对象的所有属性转换为`getter`和`setter`，当读取属性或设置属性时，可以发出通知
- 依赖收集部件：该部件的作用是在一个函数的执行过程中，记录该函数所依赖的顶层函数，将来一旦发出一个通知，将重新执行该顶层函数

## 数据响应式实现

![image-20200924165657102](http://mdrs.yuanjin.tech/img/20200924165657.png)

## 依赖收集实现

![image-20200924172834305](http://mdrs.yuanjin.tech/img/20200924172834.png)

# vue3响应系统

vue3中，将响应系统彻底的分离了出去成为了一个单独的库`@vue/reactivity`

在响应式系统中，为了更好的支持`Tree Shaking`，因此将所有的功能都进行了函数化，并且全部使用`TypeSript`重写

## 数据响应实现

<img src="http://mdrs.yuanjin.tech/img/20200924213343.png" alt="image-20200924213343682" style="zoom:50%;" />

## 依赖收集

<img src="http://mdrs.yuanjin.tech/img/20200924215617.png" alt="image-20200924215617947" style="zoom:50%;" />

# 题

问题：简述`vue2`和`vue3`分别是如何实现响应式的？`vue3`在响应式上的提升在哪里？

回答：

```
vue2的响应式是使用Object.defineProperty完成的，它会对原始对象有侵入。
在创建响应式阶段，会递归遍历原始对象的所有属性，当对象属性较多、较深时，对效率的影响颇为严重。不仅如此，由于遍历属性仅在最开始完成，因此在这儿之后无法响应属性的新增和删除。
在收集依赖时，vue2采取的是构造函数的办法，构造函数是一个整体，不利于tree shaking。

vue3的响应式是使用Proxy完成的，它不会侵入原始对象，而是返回一个代理对象，通过操作代理对象完成响应式。
由于使用了代理对象，因此并不需要遍历原始对象的属性，只需在读取属性时动态的决定要不要继续返回一个代理，这种按需加载的模式可以完全无视对象属性的数量和深度，达到更高的执行效率。
由于ES6的Proxy可以代理更加底层的操作，因此对属性的新增、删除都可以完美响应。
在收集依赖时，vue3采取的是普通函数的做法，利用高效率的WeakMap实现依赖记录，这利于tree shaking，从而降低打包体积。
```

