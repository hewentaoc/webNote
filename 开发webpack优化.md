### 1. 新开一个webpack文件，引入cleanWebpackPlugin

### 2. 引入cahe-loader, thread-loader

### 3. 大部分打包时间花费在：npm install 方法中









## 测试结果

###  (2) 旧版本打包时间: 38s左右

### (3) 新版本打包时间: 17s左右



## 开发优化打包方案

### 1. 去掉npm install，开发环境没必要(拉取新的包)  －　需要商榷

### 2. 对webpack进行优化　

### 3. 写入一个新的脚本



## 线上环境优化方案

1. 是否开启css tree shaking (`purgecss-webpack-plugin`)



## 疑问

### 1. loader和plugin的执行顺序

### 2. webpack如何启动按需加载



