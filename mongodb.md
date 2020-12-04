## 开发遇到的问题

### 1. 在mongodb中怎么插入一条表中不存在的数据?

> (1) **我常用的方案，先查询后插入**
>
> (2) **可以给数据创建一个唯一的索引**
>
> (3) **可以用新数据替换之前的数据**

 ###  2. 在mongo中怎么定期删除15天之前的数据？

> (1) 根据mongo中创建索引的方式对新插入的数据进行管理
>
> **具体的实现方式：**
>
> ```js
> /**
>   方法一　
> **/
> //首先创建一个索引，通过createdAt来进行标定，设置90秒之后自动删除
> db.log_events.createIndex( { "createdAt": 1 }, { expireAfterSeconds: 90 } )
> 
> //插入新的数据　"createdAt": new Date()必须传入
> 　db.log_events.insert( { "createdAt": new Date(), "logEvent": 2, "logMessage": "Success!" } )
> 
> 
> /**
> 　方法二　
> **/
> //增加一个expireTime字段（用于指定过期时间），expireAfterSeconds属性值设置为0，这个expireTime的时间就需要在插入时指定上
> db.log_events.createIndex( { "expireTime": 1 }, { expireAfterSeconds: 0 } )
> 
> //插入数据
> insertOne('log_test',{name:"测试222",expireTime:date}
> 
> 
> 
> ```
> 
>

