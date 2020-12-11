



[TOC]

# 一．webpack初级知识

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
- ![](./assets/2020-09-25 10-15-51 的屏幕截图.png)


###  (4)  webpack --config  build/webpack.config.js
- 执行指定文件夹下webpack的配置文件

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


webpack提供的cli支持很多的参数，例如```--mode```，但更多的时候，我们会使用更加灵活的配置文件来控制webpack的行为

默认情况下，webpack会读取```webpack.config.js```文件作为配置文件，但也可以通过CLI参数```--config```来指定某个配置文件

配置文件中通过CommonJS模块导出一个对象，对象中的各种属性对应不同的webpack配置

**注意：配置文件中的代码，必须是有效的node代码**

当命令行参数与配置文件中的配置出现冲突时，以命令行参数为准。

**基本配置：**

1. mode：编译模式，字符串，取值为development或production，指定编译结果代码运行的环境，会影响webpack对编译结果代码格式的处理
2. entry：入口，字符串（后续会详细讲解），指定入口文件
3. output：出口，对象（后续会详细讲解），指定编译结果文件

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
- entry的路径默认是相对于webpack执行的目录 == package.json所在的同级

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

# 5. webpack的编译过程

## webpack 编译过程 {ignore}

webpack 的作用是将源代码编译（构建、打包）成最终代码

![](assets/2020-01-09-10-26-15.png)

整个过程大致分为三个步骤

1. 初始化
2. 编译
3. 输出

![](assets/2020-01-09-10-53-28.png)

## 初始化

此阶段，webpack会将**CLI参数**、**配置文件**、**默认配置**进行融合，形成一个最终的配置对象。

对配置的处理过程是依托一个第三方库```yargs```完成的

此阶段相对比较简单，主要是为接下来的编译阶段做必要的准备

目前，可以简单的理解为，初始化阶段主要用于产生一个最终的配置

## 编译

1. **创建chunk**

chunk是webpack在内部构建过程中的一个概念，译为```块```，它表示通过某个入口找到的所有依赖的统称。

根据入口模块（默认为```./src/index.js```）创建一个chunk

![](assets/2020-01-09-11-54-08.png)

每个chunk都有至少两个属性：

- name：默认为main
- id：唯一编号，开发环境和name相同，生产环境是一个数字，从0开始

2. **构建所有依赖模块**

   **dependencies，记录依赖关系**

   {
    ./src/index.js : function(){}
   }
   

![](assets/2020-01-09-12-32-38.png)

> AST在线测试工具：https://astexplorer.net/

简图

![](assets/2020-01-09-12-35-05.png)

3. **产生chunk assets**

在第二步完成后，chunk中会产生一个模块列表，列表中包含了**模块id**和**模块转换后的代码**

接下来，webpack会根据配置为chunk生成一个资源列表，即```chunk assets```，资源列表可以理解为是生成到最终文件的文件名和文件内容

![](assets/2020-01-09-12-39-16.png)

> chunk hash是根据所有chunk assets的内容生成的一个hash字符串
> hash：一种算法，具体有很多分类，特点是将一个任意长度的字符串转换为一个固定长度的字符串，而且可以保证原始内容不变，产生的hash字符串就不变

简图

![](assets/2020-01-09-12-43-52.png)

4. **合并chunk assets**

将多个chunk的assets合并到一起，并产生一个总的hash

![](assets/2020-01-09-12-47-43.png)

## 输出

此步骤非常简单，webpack将利用node中的fs模块（文件处理模块），根据编译产生的总的assets，生成相应的文件。

![](assets/2020-01-09-12-54-34.png)

## 总过程

![](assets/2020-01-09-15-51-07.png)

![](assets/2020-01-09-12-32-38.png)

**涉及术语**

1. module：模块，分割的代码单元，webpack中的模块可以是任何内容的文件，不仅限于JS
2. chunk：webpack内部构建模块的块，一个chunk中包含多个模块，这些模块是从入口模块通过依赖分析得来的
3. bundle：chunk构建好模块后会生成chunk的资源清单，清单中的每一项就是一个bundle，可以认为bundle就是最终生成的文件
4. hash：最终的资源清单所有内容联合生成的hash值
5. chunkhash：chunk生成的资源清单内容联合生成的hash值
6. chunkname：chunk的名称，如果没有配置则使用main
7. id：通常指chunk的唯一编号，如果在开发环境下构建，和chunkname相同；如果是生产环境下构建，则使用一个从0开始的数字进行编号


# ６. loader

###  loader

- **webpack loader： loader本质上是一个函数，它的作用是将某个源码字符串转换成另一个源码字符串返回**

- https://juejin.im/post/6844903555673882632

> webpack做的事情，仅仅是分析出各种模块的依赖关系，然后形成资源列表，最终打包生成到指定的文件中。
> 更多的功能需要借助webpack loaders和webpack plugins完成。

webpack loader： loader本质上是一个函数，它的作用是将某个源码字符串转换成另一个源码字符串返回

![](assets/2020-01-13-10-39-24.png)

**loader函数的将在模块解析的过程中被调用，以得到最终的源码**

**全流程：**

![](assets/2020-01-13-09-28-52.png)

**chunk中解析模块的流程：**

![](assets/2020-01-13-09-29-08.png)

**chunk中解析模块的更详细流程：**

![](assets/2020-01-13-09-35-44.png)

**处理loaders流程：**

![](assets/2020-01-13-10-29-54.png)

**loader配置：**

**完整配置**

```js
module.exports = {
    module: { //针对模块的配置，目前版本只有两个配置，rules、noParse
        rules: [ //模块匹配规则，可以存在多个规则
            { //每个规则是一个对象
                test: /\.js$/, //匹配的模块正则
                use: [ //匹配到后应用的规则模块
                    {  //其中一个规则
                        loader: "模块路径", //loader模块的路径，该字符串会被放置到require中
                        options: { //向对应loader传递的额外参数

                        }
                    }
                ]
            }
        ]
    }
}
```

**简化配置**

```js
module.exports = {
    module: { //针对模块的配置，目前版本只有两个配置，rules、noParse
        rules: [ //模块匹配规则，可以存在多个规则
            { //每个规则是一个对象
                test: /\.js$/, //匹配的模块正则
                use: ["模块路径1", "模块路径2"]//loader模块的路径，该字符串会被放置到require中
            }
        ]
    }
}
```

###  loader的本质

- 本质也是一个模块，最后会通过module.exports导出东西

# 7.plugin

**loader的功能定位是转换代码，而一些其他的操作难以使用loader完成，比如：**

- 当webpack生成文件时，顺便多生成一个说明描述文件
- 当webpack编译启动时，控制台输出一句话表示webpack启动了
- 当xxxx时，xxxx

这种类似的功能需要把功能嵌入到**webpack的编译流程中**，而这种事情的实现是依托于plugin的


plugin的**本质**是一个带有apply方法的对象

```js
var plugin = {
    apply: function(compiler){
        
    }
}
```

通常，习惯上，我们会将该对象写成构造函数的模式

```js
class MyPlugin{
    apply(compiler){

    }
}

var plugin = new MyPlugin();
```

要将插件应用到webpack，需要把插件对象配置到webpack的plugins数组中，如下：

```js
module.exports = {
    plugins:[
        new MyPlugin()
    ]
}
```

apply函数会在初始化阶段，创建好Compiler对象后运行。

compiler对象是在初始化阶段构建的，整个webpack打包期间只有一个compiler对象，后续完成打包工作的是compiler对象内部创建的compilation

apply方法会在**创建好compiler对象后调用**，并向方法传入一个compiler对象

![](assets/2020-01-15-12-49-26.png)

compiler对象提供了大量的钩子函数（hooks，可以理解为事件），plugin的开发者可以注册这些钩子函数，参与webpack编译和生成。

你可以在apply方法中使用下面的代码注册钩子函数:

```js
class MyPlugin{
    apply(compiler){
        compiler.hooks.事件名称.事件类型(name, function(compilation){
            //事件处理函数
        })
    }
}
```

**事件名称**

即要监听的事件名，即钩子名，所有的钩子：https://www.webpackjs.com/api/compiler-hooks

**事件类型**

这一部分使用的是 Tapable API，这个小型的库是一个专门用于钩子函数监听的库。

它提供了一些事件类型：

- tap：注册一个同步的钩子函数，函数运行完毕则表示事件处理结束
- tapAsync：注册一个基于回调的异步的钩子函数，函数通过调用一个回调表示事件处理结束
- tapPromise：注册一个基于Promise的异步的钩子函数，函数通过返回的Promise进入已决状态表示事件处理结束

**处理函数**

处理函数有一个事件参数```compilation```


# 8.webpack细节配置

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

# 9.webpack如何区分环境

- **(1) webpack --config webpack.config.js**
- **(2)** **通过配置文件传递参数来进行区分**
```js
    "scripts": {
        "dev": "webpack",
        "prod": "webpack --env.prod"
    }
```
有些时候，我们需要针对生产环境和开发环境分别书写webpack配置

为了更好的适应这种要求，webpack允许配置不仅可以是一个对象，还可以是一个**函数**

```js
module.exports = env => {
    return {
        //配置内容
    }
}
```

在开始构建时，webpack如果发现配置是一个函数，会调用该函数，将函数返回的对象作为配置内容，因此，开发者可以根据不同的环境返回不同的对象

在调用webpack函数时，webpack会向函数传入一个参数env，该参数的值来自于webpack命令中给env指定的值，例如

```shell
npx webpack --env abc # env: "abc"

npx webpack --env.abc # env: {abc:true}
npx webpack --env.abc=1  # env： {abc:1}
npx webpack --env.abc=1 --env.bcd=2 # env: {abc:1, bcd:2}
```

这样一来，我们就可以在命令中指定环境，在代码中进行判断，根据环境返回不同的配置结果。





# 二．webpack拓展

# 1.webpack常用的插件拓展

### (1) clear-webpack-plugin

> **清除文件夹目录**

- **开发中遇到的问题－插件不进行文件夹的清空**

  > 解决方式: 在output配置打包后的文件所在的目录即可解决

### **(2) html-webpack-plugin**

> **自动生成页面**

 HtmlWebpackPlugin 在此可以用于自动重新生成一个index.html或依据模板生成
  帮你把所有生产的js文件引入到html中，最终生成到output目录
  ```js
    new HtmlWebpackPlugin({
      chunks:'all'
    })
  ```
  chunks:'all' 默认值会把所有的js都引进来

- copy-webpack-plugin 复制静态资源

```js
    new CopyWebpackPlugin({
        patterns: [{
            from: __dirname + '/dist',
            to:__dirname+ '/demo'
        }]
    })
```

### **(３)　copy-webpack-plugin**
> 复制静态文件到指定目录
```js
        new CopyWebpackPlugin({
            patterns: [{
                from: './public',
                to: './' //相对于输出的文件夹
            }]
        }),
```

### 　**(４)　webpack-dev-server**

> 开发服务器

在**开发阶段**，目前遇到的问题是打包、运行、调试过程过于繁琐，回顾一下我们的操作流程：

1. 编写代码
2. 控制台运行命令完成打包
3. 打开页面查看效果
4. 继续编写代码，回到步骤2

并且，我们往往希望把最终生成的代码和页面部署到服务器上，来模拟真实环境

为了解决这些问题，webpack官方制作了一个单独的库：**webpack-dev-server**

它**既不是plugin也不是loader**

先来看看它怎么用

1. 安装
2. 执行```webpack-dev-server```命令

```webpack-dev-server```命令几乎支持所有的webpack命令参数，如```--config```、```-env```等等，你可以把它当作webpack命令使用

这个命令是专门为开发阶段服务的，真正部署的时候还是得使用webpack命令

当我们执行```webpack-dev-server```命令后，它做了以下操作：

1. 内部执行webpack命令，传递命令参数
2. 开启watch
3. 注册hooks：类似于plugin，webpack-dev-server会向webpack中注册一些钩子函数，主要功能如下：
   1. 将资源列表（aseets）保存起来
   2. 禁止webpack输出文件
4. 用express开启一个服务器，监听某个端口，当请求到达后，根据请求的路径，给予相应的资源内容

**配置**

针对webpack-dev-server的配置，参考：https://www.webpackjs.com/configuration/dev-server/

常见配置有：

- port：配置监听端口
- proxy：配置代理，常用于跨域访问
- stats：配置控制台输出内容

```js
   devServer:{ 　　
        port:9090, //端口号
        open:true, //是否打开浏览器
        proxy:{　　//配置服务器代理
            '/api':{
                target:'http://open.duyiedu.com',
                changeOrigin: true //更改请求头中的host和origin
            }
        }
    }
```
### 　**(5)　普通文件处理**

- **file-loader url-loader**

> file-loader:生成依赖的文件到输出目录,然后将模块文件设置为：导处一个路径

```js
//file-loader
function loader(source){
	// source：文件内容（图片内容 buffer）
	// 1. 生成一个具有相同文件内容的文件到输出目录　（将需要处理的图片打包的目标文件夹中）
	// 2. 返回一段代码   export default "文件名"　（处理源码字符串后返回新生成图片的路径）
}
```

> url-loader:将依赖的文件转换为：导出一个base64格式的字符串
```js
//url-loader
function loader(source){
	// source：文件内容（图片内容 buffer）
	// 1. 根据buffer生成一个base64编码
	// 2. 返回一段代码   export default "base64编码"
}
```

### 　**(6)　解决路径问题**

在使用file-loader或url-loader时，可能会遇到一个非常有趣的问题

比如，通过webpack打包的目录结构如下：

```yaml
dist
    |—— img
        |—— a.png  #file-loader生成的文件
    |—— scripts
        |—— main.js  #export default "img/a.png"
    |—— html
        |—— index.html #<script src="../scripts/main.js" ></script>
```

这种问题发生的根本原因：模块中的路径来自于某个loader或plugin，当产生路径时，loader或plugin只有相对于dist目录的路径，并不知道该路径将在哪个资源中使用，从而无法确定最终正确的路径

面对这种情况，需要依靠webpack的配置**publicPath**解决

### 　**(7)　内置插件**

所有的webpack内置插件都作为webpack的静态属性存在的，使用下面的方式即可创建一个插件对象

```js
const webpack = require("webpack")
new webpack.插件名(options)
```

> **DefinePlugiv**

全局常量定义插件，使用该插件通常定义一些常量值，例如：

```js
new webpack.DefinePlugin({
    PI: `Math.PI`, // PI = Math.PI
    VERSION: `"1.0.0"`, // VERSION = "1.0.0"
    DOMAIN: JSON.stringify("duyi.com")
})
```

这样一来，在源码中，我们可以直接使用插件中提供的常量，当webpack编译完成后，会自动替换为常量的值

> **BannerPlugin**

它可以为每个chunk生成的文件头部添加一行注释，一般用于添加作者、公司、版权等信息

```js
new webpack.BannerPlugin({
  banner: `
  hash:[hash]
  chunkhash:[chunkhash]
  name:[name]
  author:yuanjin
  corporation:duyi
  `
})
```

>  **ProvidePlugin**

自动加载模块，而不必到处 import 或 require 

```js
new webpack.ProvidePlugin({
  $: 'jquery',
  _: 'lodash'
})
```

然后在我们任意源码中：

```js
$('#item'); // <= 起作用
_.drop([1, 2, 3], 2); // <= 起作用
```



# 三．css工程化

## 1. css的问题

> ### 类名冲突的问题
> ### 重复样式
> ### css文件细分问题

## 2. webpack拆分css

要拆分css，就必须把css当成像js那样的模块；

**要把css当成模块，就必须有一个构建工具（webpack），它具备合并代码的能力**

而webpack本身只能读取css文件的内容、将其当作JS代码进行分析，因此，会导致错误

**于是，就必须有一个loader，能够将css代码转换为js代码**

## css-loader

**css-loader的作用，就是将css代码转换为js代码**

它的处理原理极其简单：将css代码作为字符串导出

例如：

```css
.red{
    color:"#f40";
}
```

经过css-loader转换后变成js代码：

```js
module.exports = `.red{
    color:"#f40";
}`
```

> 上面的js代码是经过我简化后的，不代表真实的css-loader的转换后代码，css-loader转换后的代码会有些复杂，同时会导出更多的信息，但核心思想不变

再例如：

```css
.red{
    color:"#f40";
    background:url("./bg.png")
}
```

经过css-loader转换后变成js代码：

```js
var import1 = require("./bg.png");
module.exports = `.red{
    color:"#f40";
    background:url("${import1}")
}`;
```

这样一来，经过webpack的后续处理，会把依赖```./bg.png```添加到模块列表，然后再将代码转换为

```js
var import1 = __webpack_require__("./src/bg.png");
module.exports = `.red{
    color:"#f40";
    background:url("${import1}")
}`;
```

再例如：

```css
@import "./reset.css";
.red{
    color:"#f40";
    background:url("./bg.png")
}
```

会转换为：

```js
var import1 = require("./reset.css");
var import2 = require("./bg.png");
module.exports = `${import1}
.red{
    color:"#f40";
    background:url("${import2}")
}`;
```

总结，css-loader干了什么：

1. **将css文件的内容作为字符串导出**
2. **将css中的其他依赖作为require导入，以便webpack分析依赖**

## style-loader

由于css-loader仅提供了将css转换为字符串导出的能力，剩余的事情要交给其他loader或plugin来处理

style-loader可以将css-loader转换后的代码进一步处理，将css-loader导出的字符串加入到页面的style元素中

例如：

```css
.red{
    color:"#f40";
}
```

经过css-loader转换后变成js代码：

```js
module.exports = `.red{
    color:"#f40";
}`
```

经过style-loader转换后变成：

```js
module.exports = `.red{
    color:"#f40";
}`
var style = module.exports;
var styleElem = document.createElement("style");
styleElem.innerHTML = style;
document.head.appendChild(styleElem);
module.exports = {}
```

> 以上代码均为简化后的代码，并不代表真实的代码
> style-loader有能力避免同一个样式的重复导入

​	

​	




