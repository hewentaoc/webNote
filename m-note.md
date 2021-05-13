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

(2)  read NAME
	
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