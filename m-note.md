## Tools

```sh
git config user.name
```



**NVM 、NRM 、NPM**

NVM 　切换Node版本

​	nvm install stable

NRM 　自由切换 npm 源（主要用来切换小米源）

​	nrm ls

​    nrm add mi http://

​    nrm use mi	

NPM　 包管理工具



npm run branch 创建分支

npm run commit 提交信息 push

npm run difff 进行代码的code review

npm run land 

**创建分支**

git checkout -b hewentao-add-param origin/master

**关联分支**

git push --set-upstream origin hewentao-add-param

lsof -i tcp:7000   **查找**7000**的端口**

kill -9 28538       **清除**7000**端口的**id

ifconfig               **获取电脑的**ip

db.dataPage.drop() **删除**collections







## shell

1. 常见的脚本命令

(1) echo ‘enter you name:’

	enter you name:

(2)  read

 NAME
	

	读取输入的值为NAME

(3) echo $NAME

     输出输入的值

(4)  $# 代表添加到Shell的参数个数
       if [ $# -ge 1 ] ;then
     如果shell的参数个数 >= 1
    就执行if语句
      -ge  >= 
	$1 代表第一个参数

-eq           //等于

-ne           //不等于

-gt            //大于

-lt            //小于

ge            //大于等于

le            //小于等于



## Note

用css来控制文本展示三行

```css
  display: -webkit-box;
  -webkit-box-orient: vertical;
  text-overflow: ellipsis;
  -webkit-line-clamp: 3;
  max-height: none;
```

无障碍模式适配

(1)

​       焦点可能和tabindex= 0 这个属性有关系

​       使用tabindex这个属性会出现聚焦

  所以需要使用outline:none进行取消边默认的border

1G = 1024 MB

1MB = 1024KB

1KB = 1024Byte

### **两种命令把他们写入到** **package.json** **文件里面**

  1.安装使用--save-dev简写为-D 的插件是被写入到 devDependencies 对象里面。
 2. 使用--save简写为-S 的插件是则被写入到 dependencies 对象里面

 3. devDependencies  里面的插件只用于开发环境，不用于生产环境。

 4. dependencies  则是需要发布到生产环境的。

      

      简单的说-D用于开发环境，-S用于生产环境

## store

页面加载第二帧

```js
requestAnimationFrame(()=>{
	requestAnimationFrame(()=>{
		//执行回调	
	})
})
div.getBoundingClientRect();
```

ssh root@58.87.117.176

## 前端知识收集

1.  如何获取对象的length ?
    
    var obj = {a:1,b:2,c:3};
    var len = Object.keys(obj).length;


2. video、audio

3. vue中watch属性

　watch属性可以检测到对象的变化吗？
　目前来看检测不到或者性能太差

4. 滚动条置顶的方法
　 
     ```js
　 document.body.scrollBy(0,-document.documentElement.scrollTop);
     scrollTo(0,0);
     document.documentElement.scrollTop = 0;
     document.body.scrollTop = 0;
　 document.documentElement.scrollIntoView();
　 console.log(document.documentElement.scrollTop,document.body.scrollTop,window.pageYOffset,7788)
　 ```
　 
5.   window.addEventListener('hashchange',function(){});
当 一个窗口的 hash （URL 中 # 后面的部分）改变时就会触发 hashchange 事件（参见 location.hash）。


6.  在Dom元素重新渲染之后  就会触发$nextTick中的回调函数
 this.$nextTick(() => {

 })

7. transform 和　fixed不能连起来用
8. css动画、js动画