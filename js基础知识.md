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



## 4. 使用Proxy,Reflect进行对象或者数组的代理

### 优点

- 可以解决新增属性无法进行监控的问题
- 可以直接监控数组

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










