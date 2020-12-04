# 1. 了解webpack

###  (1)  webpack主要做啥的

- webpack做的事情，仅仅是分析出各种模块的依赖关系，然后形成资源列表，最终打包到指定的文件中。

-  更多的功能需要借助webpack loaders 和 wepack plugins完成



###  (2)  webpack.config.js

- 在node环境中进行文件大打包，读取文件，分析出文件的依赖关系

- 在打包过程中，仅仅是处理文件的依赖关系，不会执行文件

- webpack.config.js是在node环境中进行执行的，仅支持node环境的语法解构

###  (3)   webpack流程图

- https://www.yuque.com/u2102367/lzfa30/kigfgx/edit

# 2. 如何安装webpack

###  (1)  安装
- npm install --save-dev(-D) webpack webpack-cli

### (2) webpack：核心包，包含了webpack构建过程中要用到的所有api

### (3)  webpack-cli：提供一个简单的cli命令，它调用了webpack核心包的api，来完成构建过程

- 默认情况下，src/index.js是默认入口，dist/main.js是默认出口

###  开发模式　--mode=development
###  生产模式　--mode=production

```js
	"build": "webpack --mode=production",
    "dev": "webpack --mode development"
```

# 3. webpack的配置文件

### (1)  得到当前文件的目录

- path: __dirname + '/dist'

### (2) 得到node运行的目录

- 在webpack配置文件中，getPath代表node运行的目录
- let getPath = path.resolve('./');

### source-map

- souce map 可以当运行发生错误的时候，我们更加希望能看到源代码中的错误，而不是转换后代码的错误

　devtool:hidden-source-map

- SourceMapDevToolPlugin插件可以来配置source-map,他的可配置性更高

###  entry
- 在你想要多个依赖文件一起注入，并且将它们的依赖导向(graph)到一个“chunk”时，传入数组的方式就很有用
    entry:{
        test:['./entry1.js','./entry2.js']
    },

- 将多个js文件，合并成一个模块

### output
[id].[chunkhash:5]
__dirname + '/dist'

 output:{
    path:
    filename:[id].[chunkhash:5].js
 }

name：chunkname
hash: 总的资源hash，通常用于解决缓存问题
chunkhash: 使用chunkhash
id: 使用chunkid，不推荐

# 4. webpack的编译结果

### 建议结果的分析
```js


//编译结果分析
//合并模块
//将每个模块的路径，当做唯一的标示，进行合并
(function(modules){
    let cacheModules = {};
    function _require(moduleId){
        if(cacheModules[moduleId]){//缓存模块
            return cacheModules[moduleId]
        }
        let func = modules[moduleId];
        let module = {
            exports:{

            }
        }
        func(module,module.exports,_require);
        let result = module.exports;
        cacheModules[moduleId] = result
        return result;
    }
    _require('./index');//引用入口文件
    _require('./index2');//引用入口文件
})({
    './index':function(module,exports,_require){
        console.log('index')
        let a = _require('./a')
        console.log(a)
    },
    './index2':function(module,exports,_require){
        console.log('index2')
        let a = _require('./a')
        console.log(a)
    },
    './a':function(module,exports,_require){
        console.log('我在a')
        module.exports = '111';
    }
})
```

# 5. loader

###  loader

- webpack loader： loader本质上是一个函数，它的作用是将某个源码字符串转换成另一个源码字符串返回。


- https://juejin.im/post/6844903555673882632


###  loader的本质

- 本质也是一个模块，最后会通过module.exports导出东西

# 6.plugin


# 7.webpack细节配置

### context

- 该配置会影响入口和loaders的解析，入口和loaders的相对路径会以context的配置作为基准路径，这样，你的配置会独立于CWD（current working directory 当前执行路径）

### output

> filename
> path
> library: "abc"  这样一来，打包后的结果中，会将自执行函数的执行结果暴露给abc  
> libraryTarget: "var"  该配置可以更加精细的控制如何暴露入口包的导出结果
- https://www.webpackjs.com/configuration/output/#output-librarytarget

### target 

> 设置打包结果最终要运行的环境
> target:"web" //默认值

### module 加载器

> noParse: /jquery/
- 不解析正则表达式匹配的模块，通常用它来忽略那些大型的单模块库，以提高构建性能

### resolve

> **(1) modules **
- 当解析模块时，如果遇到导入语句，require("test")，webpack会从下面的位置寻找依赖的模块
- modulse:['node_modules','a'] //模块的查找位置

> **(2) extensions**

> - extensions: [".js", ".json"]  //默认值
- 当解析模块时，遇到无具体后缀的导入语句，例如require("test")，会依次测试它的后缀名

> **(3) alias** 
alias: {
  "@": path.resolve(__dirname, 'src'),
  "_": __dirname
}
- 有了alias（别名）后，导入语句中可以加入配置的键名，例如require("@/abc.js")，webpack会将其看作是require(src的绝对路径+"/abc.js")。
- 在大型系统中，源码结构往往比较深和复杂，别名配置可以让我们更加方便的导入依赖

### externals 从最终的bundle中排除掉配置的配置的源码

- 这比较适用于一些第三方库来自于外部CDN的情况，这样一来，即可以在页面中使用CDN，又让bundle的体积变得更小，还不影响源码的编写

###  stats

- stats控制的是构建过程中控制台的输出内容

# 8.webpack常用的插件拓展

### (1) clear-webpack-plugin
