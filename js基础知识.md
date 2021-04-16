## js的底层知识



### 1. 说说函数预编译，执行期上下文，作用域

- (1) 创建全局的执行期上下文（GO）

- (2) 函数定义的时候作用域指向GO

- (3) 在函数执行之前创建执行期上下文，将执行期上下文放在作用域链的最顶端

- (4) 在创建执行期之前进行变量生命提升，函数生命提升，(函数预编译)

- (5) 在函数执行完成的时候，销毁执行期上下文，恢复定义时的执行期上下文

- (6) 如果函数执行完成的时候，内部的函数被保存到外部，就会形成闭包

- (7) 内部函数定义时会拿去外层函数作用域作为自己的作用域

- (8) 所以内部函数被保存到外部仍然可以拿到外部函数作用域中的值

  ![](assets/2021-03-01 15-37-31 的屏幕截图.png)

### 2. 谈谈ES6的Symbol

> **特点: 唯一,可作为对象的属性,有静态属性Symbol.iterator**

```js
1.
   如果Symbol中传递的参数是一个对象
   那么就会调用toString()方法,返回的值放在Symbol中
let os = Symbol({
    name:'hwt',
    toString:function(){
        return 'duyi';
    }
})

2. Symbol具有唯一性 Symbol('a') != Symbol('a') 

3. Symbol作为对象的属性
    let os = Symbol('a');
    let name = 'name';
    let obj = {
         [os] :'hwt',
         [name]:'ww',
    }   
    取到属性值: obj[os]

4.Symbol上的静态属性 --》 Symbol.interator

 arr  Set  Map  " " arguments nodelist 
 每一个数据原型上都有Symbol(Symbol.iterator)属性
 并且属性都 == iterator函数

 console.log(Symbol.iterator,Symbol('Symbol.interator'));
```



## 3. 使用Object.defineProperty(obj,prop,{})进行对象或者数组的代理

  - **代理对象的核心是**需要使用一个新的对象进行之前对象的代理
 	- **代理数组的核心**是重写push等方法，从而进行数组方法监控
 	- 如果对象的层次比较深，需要进行深层次递归 

　### 问题

- Object.defineProperty()只能检测对象,对于数组不能进行监控
- 不能对深层次对象监控
- 后面新加的属性没法进行监控

```js
let obj = {
    name:'hwt',
    age:13,
    test:[1],
    love:{
        sing:'song'
    }
}
//错误的方法
let proxyObj = {}
function proxyMix(obj){
    for (const key in obj) {
        if (Object.hasOwnProperty.call(obj, key)) {
            const element = obj[key];
            if(element instanceof Array){
                Object.defineProperty(proxyObj,key,{
                    set(val){
                        console.log('set')
                        obj[key] = val;
                    },
                    get(){
                        console.log('get');
                        return obj[key];
                    }
                })
                let prototypeObj = {
                    push:function(){},
                    pop(){},
                    shift(){},
                    unshift(){}
                }
                element.__proto__ = prototypeObj;
                proxyArrFunc(prototypeObj,'push');
                proxyArrFunc(prototypeObj,'pop');
            }else{
                proxyObjFunc(key,obj,element);
            }
        }
    }
}
let protoArr = Array.prototype;
function proxyArrFunc(prototypeObj,prop){
    Object.defineProperty(prototypeObj,prop,{
        value:(function(){
            return function(...arg){
                console.log(prop,'数组监控')
                protoArr[prop].apply(this,arg);
                return this.length;
            }
        }())
    })
}
function proxyObjFunc(key,obj,element){
    Object.defineProperty(proxyObj,key,{
        set(val){
            console.log('set')
            obj[key] = val;
        },
        get(){
            console.log('get');
            return obj[key];
        }
    })
    if(typeof element == 'object'){
       proxyMix(element);
    }
}

proxyMix(obj);
```

```js
/**
* 1. Object.defineProperty - 1
*/

let obj = {
    name:'hwt',
    age:13,
    test:[1,2,3],
    love:{
        sing:'song'
    }
}

function proxyMix(obj){
    let proxyObj = {}
    if(obj instanceof Array){
       proxyObj = [];
       for(let i = 0 ; i < obj.length ; i++){
           proxyObj[i] = proxyObjFunc(obj[i]);
       }
       proxyObj = proxyArrFunc(obj)
    }else if(typeof obj == 'object'){
        proxyObj = proxyObjFunc(obj);
    }
    return proxyObj;
}
function proxyObjFunc(obj){
    let proxyObj = {}
    for (const key in obj) {
        Object.defineProperty(proxyObj,key,{
            set(val){
                console.log('set')
                obj[key] = val;
            },
            get(){
                console.log('get');
                return obj[key];
            }
        })
        if(typeof obj[key] == 'object'){
            proxyObj[key] = proxyMix(obj[key]);
        }
    }
    return proxyObj;
}
let protoArr = Array.prototype;
function proxyArrFunc(arr){
    let prototypeObj = {
        push:function(){},
        pop(){},
        shift(){},
        unshift(){}
    }
    Object.defineProperty(prototypeObj,'push',{
        value:(function(){
            return function(...arg){
                console.log('push','数组监控')
                protoArr['push'].apply(this,arg);
                return this.length;
            }
        }())
    })
    arr.__proto__ = prototypeObj;
    return arr;
}

let proxyObj = proxyMix(obj);
```

```js

/**
  2. Object.defineProperty - 2
*/
function observer(obj) {
    if(typeof obj != 'object'){
        return;
    }
    Object.keys(obj).forEach((item)=>{
        let value = obj[item];
        observer(obj[item])//递归监听属性
        Object.defineProperty(obj,item,{
            get(){
                console.log('get '+ item + ':',value)
                return value;
            },
            set(val){
                observer(val);//对赋值为对象的值进行重新监听
                console.log('set '+ item + ':',value)
                value = val;
            }
        })
    })
}
observer(obj)
```

```js

/**
  3. Object.defineProperty - 3
*/
function observer(obj){
    if(typeof obj != 'object'){
        return obj;
    }
    
    let result = new Proxy(obj,{
        set(target,key,value){
            console.log('set',key)
            observer(value)
            Reflect.set(target,key,value);
        },
        get(target,key){
          console.log('get',key)
          let value = observer(Reflect.get(target,key));
          return value;
        }
    })
    return result;
}

```



为什么proxy可以get的时候，再进行深度遍历，而Object.defineProperty不可以?　

> **proxy有传递的参数,可以直接得到此时的target,key,从而得到value**
>
> ***而Object.defineProperty*****中的get事件不具备实施得到value的功能**
>
> **proxy是代理到别的对象上,不会出现死循环**
>
> **Object.defineProperty如果代理到自己的身上会出现死循环**

## 4. 使用Proxy,Reflect进行对象或者数组的代理

### 优点

- 可以解决新增属性无法进行监控的问题
- 可以直接监控数组

### 缺点

- 具有兼容性问题
- 监控对象深层次属性值变化的时候,会出现问题(**不能监听对象的深层次变化**)

 

**注:**

- 使用proxy 进行值的监控的时候,必须用返回的代理对象proxyDate调用属性,才能进行属性值的监控

```js
let proxyMixObj = new Proxy(obj,{
    set(target,key,value,proxyObj){
        console.log('proxy set')
        Reflect.set(target,key,value)
    },
    get(target,key,proxyObj){
        console.log('proxy get')
        return Reflect.get(target,key)
    }
})
```



## 5. 迭代器和可迭代协议

> 解决副作用的 redux 中间件
> redux-thunk：需要改动action，可接收action是一个函数
> redux-promise：需要改动action，可接收action是一个promise对象，或action的payload是一个promise对象
> 以上两个中间件，会导致action或action创建函数不再纯净。
> redux-saga将解决这样的问题，它不仅可以保持action、action创建函数、reducer的纯净，而且可以用模块化的方式解决副作用，并且功能非常强大。
> redux-saga是建立在ES6的生成器基础上的，要熟练的使用saga，必须理解生成器。
> 要理解生成器，必须先理解迭代器和可迭代协议。

```js
//迭代器
let iterator = {
    a:1,
    next(){
        return {
            value:this.a++,
            done: this.a > 4
        }
    }
}
//迭代器创建函数
function createIterator(arr){
    let i = 0;
    return {
        next(){
            return {
                value:arr[i++],
                done: i > arr.length
            }
        }
    }
}
//Symbol.iterator 可迭代协议
let arrs = [3,44,99,2,1]
let iter = arrs[Symbol.iterator]();
for(let i = 0 ; i < arrs.length ;i++){
    console.log(iter.next())
}
```





## 迭代

类似于遍历

遍历：有多个数据组成的集合数据结构（map、set、array等其他类数组），需要从该结构中依次取出数据进行某种处理。

迭代：按照某种逻辑，依次取出下一个数据进行处理。

## 迭代器 iterator (是个对象)

JS语言规定，如果一个对象具有next方法，并且next方法满足一定的约束，则该对象是一个迭代器（iterator）。

next方法的约束：该方法必须返回一个对象，该对象至少具有两个属性：

- value：any类型，下一个数据的值，如果done属性为true，通常，会将value设置为undefined
- done：bool类型，是否已经迭代完成

通过迭代器的next方法，可以依次取出数据，并可以根据返回的done属性，判定是否迭代结束。

### 迭代器创建函数 iterator creator

它是指一个函数，调用该函数后，返回一个迭代器，则该函数称之为迭代器创建函数，可以简称位迭代器函数。


## 可迭代协议

ES6中出现了for-of循环，该循环就是用于迭代某个对象的，因此，for-of循环要求对象必须是可迭代的（对象必须满足可迭代协议）

可迭代协议是用于约束一个对象的，如果一个对象满足下面的规范，则该对象满足可迭代协议，也称之为该对象是可以被迭代的。

可迭代协议的约束如下：

1. 对象必须有一个知名符号属性（Symbol.iterator）
2. 该属性必须是一个无参的迭代器创建函数

## for-of循环的原理

调用对象的[Symbol.iterator]方法，得到一个迭代器。不断调用next方法，只有返回的done为false，则将返回的value传递给变量，然后进入循环体执行一次。

## 6. 生成器 generator

```js
 //生成器创建函数

 function* createGenerator(){

 }
 let generator = createGenerator(); //迭代器和可迭代对象
// generator.next == generator[Symbol.iterator]().next

//传递yield的返回值
function* createGenerator2() {
   let val = yield asyncFunc();
   console.log(1111,val);
}
let generator2 = createGenerator2();

function getData(generator){
    let next = function(val){
        let {value,done} = generator.next(val);
        if(done){
            return
        }else{
           if(value instanceof Promise){
                value.then((data)=>{
                  next(data)
                })
           }else{
               next(value)
           }
        }
    }
    next();
}
getData(generator2)
```



## generator

生成器：由构造函数Generator创建的对象，该对象既是一个迭代器，同时，又是一个可迭代对象（满足可迭代协议的对象）

```js
//伪代码

var generator = new Generator();
generator.next();//它具有next方法
var iterator = generator[Symbol.iterator];//它也是一个可迭代对象
for(const item of generator){
    //由于它是一个可迭代对象，因此也可以使用for of循环
}
```

**注意：Generator构造函数，不提供给开发者使用，仅作为JS引擎内部使用**

## generator function 

生成器函数（生成器创建函数）：该函数用于创建一个生成器。

ES6新增了一个特殊的函数，叫做生成器函数，只要在函数名与function关键字之间加上一个*号，则该函数会自动返回一个生成器

生成器函数的特点：

1. 调用生成器函数，会返回一个生成器，而不是执行函数体（因为，生成器函数的函数体执行，收到生成器控制）
2. 每当调用了生成器的next方法，生成器的函数体会从上一次yield的位置（或开始位置）运行到下一个yield
   1. yield关键字只能在生成器内部使用，不可以在普通函数内部使用
   2. 它表示暂停，并返回一个当前迭代的数据
   3. 如果没有下一个yield，到了函数结束，则生成器的next方法得到的结果中的done为true
3. yield关键字后面的表达式返回的数据，会作为当前迭代的数据
4. 生成器函数的返回值，会作为迭代结束时的value
   1. 但是，如果在结束过后，仍然反复调用next，则value为undefined
5. 生成器调用next的时候，可以传递参数，该参数会作为生成器函数体上一次yield表达式的值。
   1. 生成器第一次调用next函数时，传递参数没有任何意义
6. 生成器带有一个throw方法，该方法与next的效果相同，唯一的区别在于：
   1. next方法传递的参数会被返回成一个正常值
   2. throw方法传递的参数是一个错误对象，会导致生成器函数内部发生一个错误。
7. 生成器带有一个return方法，该方法会直接结束生成器函数
8. 若需要在生成器内部调用其他生成器，注意：如果直接调用，得到的是一个生成器，如果加入*号调用，则进入其生成器内部执行。如果是```yield* 函数()```调用生成器函数，则该函数的返回结果，为该表达式的结果





### 进制的转换

```js
//将16进制转换为10进制，将别的进制转换为10进制
var num=parseInt("a",16);  //num=10;
//将10进制转换为对应的进制，10进制的数转换为8进制
demo.toString(8);
```



## Event Loop

为了利用多核CPU的计算能力，HTML5提出Web Worker标准，允许JavaScript脚本创建多个线程，但是子线程完全受主线程控制，且不得操作DOM。所以，这个新标准并没有改变JavaScript单线程的本质。

> 笔记是用markdown写的，需要用支持markdown预览的软件打开，比如typora：https://www.typora.io/

### 线程和异步

### 进程

当一个应用程序运行时，需要使用内存和CPU资源，这些资源需要向操作系统申请。

操作系统以进程的方式来分配这些资源，一个进程就代表着一块独立于其他进程的内存空间。

一个应用程序要运行，必须至少有一个进程启动。

进程的最大特点是**独立**，一个进程不能随意的访问其他进程的资源。这就保证了多个程序在操作系统上运行互不干扰。

<img src="http://mdrs.yuanjin.tech/img/20200925114500.png" alt="image-20200925114500712" style="zoom:50%;" />

### 线程

任何一个进程在启动的时候，操作系统都会给其分配一个线程，应用程序的入口函数在主线程中运行。

在应用程序的运行过程中，可能有多个任务需要**同时**执行，于是可以向操作系统申请分配更多的线程来执行不同的任务。

比如，浏览器启动后，会开启多个线程来处理不同的事情。

<img src="http://mdrs.yuanjin.tech/img/20200925150811.png" alt="image-20200925150811708" style="zoom:50%;" />

和进程不一样的是，线程之间的资源不是隔离的，它们可以共享数据，并且线程可以被调度。

比如浏览器中的执行线程和GUI线程就是被调度为互斥的，当GUI线程执行渲染时，执行线程会被阻塞，反之亦然。所以在下面的代码中你是看不到元素内容被改变的：

```html
<h1 id="title">Monica</h1>
<button onclick="test()">click me</button>
<script>
  function test() {
    title.innerHTML = "莫妮卡";
    while (true) {}
  }
</script>
```

**我们所说的「JS中是单线程」的语言，是指在宿主环境中，执行JS代码的线程只有一个**

### 异步

单线程的主要优势是不需要考虑线程调度，降低了程序的复杂性

但在单线程中如果要处理需要等待的任务时，就必须要考虑阻塞的问题。

考虑下面的伪代码：

```js
var dom = document.getElementById("name"); // 获取某个dom元素
var name = syncConnect("http://server/getname"); // 以同步的方式向服务器获取名字
dom.innerHTML = name;
otherTask(); // 其他无关任务
```

<img src="http://mdrs.yuanjin.tech/img/20200925154832.png" alt="image-20200925154832790" style="zoom:50%;" />

因此，JS引入异步来处理该问题

```js
var dom = document.getElementById("name"); // 获取某个dom元素
asyncConnect("http://server/getname", function callback(result){ //以异步的方式向服务器获取名字
  dom.innerHTML = result;
}); 
otherTask(); // 其他无关任务
```

<img src="http://mdrs.yuanjin.tech/img/20200925155145.png" alt="image-20200925155145567" style="zoom:50%;" />

### 执行栈



### 事件循环


### 问题

1. 怎样理解JS的异步？

   ```
   JS是一个单线程的语言，意味着宿主仅为其分配了一个执行线程。
   而在实际的开发中，JS有时需要执行一些耗时的操作，比如等待一个DOM事件发生、等待网络通信完成、等待计时结束等等。如果在执行线程上去等待，就浪费线程的宝贵执行时间，阻塞后续操作。更可怕的是，由于浏览器的GUI线程和JS执行线程是互斥的，这就导致浏览器界面会因为JS的等待处于卡死状态。
   因此，JS通过异步来解决这个问题，当需要等待的时候，通知宿主的其他线程去做处理，执行线程则继续后续执行。当其他线程完成处理后，会发出通知，此时执行线程转而去执行事先定义好的回调函数即可。
   异步的方式充分了解放了执行线程，让执行线程可以毫无阻塞的运行，也就避免了浏览器宿主因为等待操作完成出现的卡死现象。
   ```

2. 下面的哪个函数执行会导致报错？如果报错，会报什么错误？为什么会出现这种情况？

   ```js
   function A(){
     A();
   }
   
   function B(){
     var n = 0;
     while(n >= 0){
       n++;
     }
   }
   ```

3. 下面的代码输出结果是什么？

   ```js
   setTimeout(function func1() {
     console.log(1)
     a();
   }, 0)
   
   function a() {
     setTimeout(function func2() {
       console.log(2)
     }, 0)
     console.log(3)
   }
   
   a();
   
   console.log(4)
   ```

### JavaScript是多线程吗

浏览器是多线程，浏览器中有个执行线程用于执行JS