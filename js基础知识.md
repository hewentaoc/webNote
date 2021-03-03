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

> 特点: 唯一,可作为对象的属性,有静态属性**Symbol.iterator**

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







