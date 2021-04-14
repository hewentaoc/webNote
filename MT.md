**MT**

#### １. import导入和require导入的区别

- import导入的是值的引用**(原文件发生变化，引用的文件也会发生变化)**

- require导入的值的拷贝**(一旦输出一个值，模块内部的变化就影响不到这个值)**

- AMD依赖前置

  > ```js
  > define(['./package/lib.js'], function(lib) {
  >       function say(){
  >            lib.log('this is fn');
  >        }
  >        return {
  >            say:say
  >        }; 
  > ```
  >
  > 
#### 2. import()引入进行动态加载,可以实现按需加载

- 动态的 import() 提供一个基于 Promise 的 API
- 动态的import() 可以在脚本的任何地方使用
- import() 接受字符串文字，你可以根据你的需要构造说明符

  

### 6. key值必须是唯一的原因

​    注:必须给每一个标签一个唯一的key值(key值必须是唯一的)

```html
<div v-for="(item,index) in items" :key="index"></div>
```

 注:key值不会渲染到页面上

​      **如果key值存在,里面的文本改变就不会对标签进行复用**

​      所以尽量不使用index作为key值

​    注:如果仅仅保持标签的位置改变,使用唯一key值,**就会复用标签,从而提高性能;**



​    注:key值另外的功能

​       不写key值,Vue内部会默认进行元素的复用

​       在input输入框中的值都会被保存下来

​       此时我们不希望input标签进行复用,就可以设置唯一key值

​       Vue就会对设置key值的元素不进行元素的复用

　### 7. template编译成render的话需要Vue做哪些优化

- 抽象语法树优化：编译器会先检查缓存中是否有已经编译好的结果，如果有结果则直接从缓存中读取。这样做防止每次同样的模板都要进行重复的编译工作。

●首先，通过compile编译器把template编译成AST语法树（abstract syntax tree，即源代码的抽象语法结构的树状表现形式），compile是creatCompiler（createCompiler是用以创建编译器的）的返回值，compile负责合并option

● 然后，AST会经过generate（将AST语法树转化成render function字符串的过程）得到render函数，render的返回值是VNode，VNode是Vue的虚拟DOM节点，里面包含（标签名、子节点、文本等）

### 8. 曝光，图片的预加载



### 9.  localStorage存储空间不足

- 会受到同源策略的约束

**方案**

- 每个子业务划分在不同子域名下，维护自己业务私有的数据
- 移动端可以与客户端通过js bridge 提供一个仿 localStorage 进行数据的维护
- file协议存储，都可以得到
- 跨页面传数据：考虑单页应用、优先采用 url 传数据
- 最后的兜底方案：清掉别人的存储

### 10.  https , http/2.0

　**(1)  状态码**

- 301永久重定向

- 302临时重定向

　**(2) http1.1和http2.0**　　

- 

   **(3) 强制缓存和协商缓存**

   **(4) https**

![](assets/https.png)

  https://cloud.tencent.com/developer/article/1662122

  https://segmentfault.com/a/1190000022350860

- **说说WebSocket和HTTP的区别**
- **三次握手，四次挥手**
- **如何区分一个数据报是本次连接产生的还是上一次由于网络拥塞延迟到达的**
- **http的下一代要解决什么问题**

### 11. js异步和宏任务

### 12. vue中scoped的实现原理

- **状态传递**
- **路由懒加载原理**
- **单页面路由原理**

PostCSS给一个组件中的所有dom添加了一个独一无二的动态属性，然后，给CSS选择器额外添加一个对应的属性选择器来选择该组件中dom，这种做法使得样式只作用于含有该属性的dom——组件内部dom

### 13 .浏览器原生图片懒加载的方法 "loading" in HTMLImageElement.prototype

  　-  **<img src='' loading='lazy'>  开启懒加载**

### 14. 不同长度文字得到一样的宽度怎么用flex实现

### 15 .CDN原理

### 16. eventloop 是什么

### 17. XSS，CSRF攻击以及预防手段

### 18.  !documentType 是什么意思

- 严格模式与混杂模式-如何触发这两种模式，区分它们有何意义

### 19. （移动端长列表优化）

### 20 .BigInt

- BigInt
- js垃圾回收机制
- script标签的defer和async的区别？多个标签有 同一属性时顺序是怎样的，能保证有序吗

### 21. react fiber架构的理解

### 22. 时间复杂度

### 23.node主要用来解决什么问题

### 24. docker

​	

### 25.项目监控

​	**oneTrack打点上报**　

　

### 26. css动画

### 27.  cmd/amd/commonjs的差异

### 28. 对于XSS 和CSRF 如何防范

浏览器缓存和服务端的缓存控制