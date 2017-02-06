# VueWebpack
Vue+Webpack基础


##项目的创建
###1.新建项目文件夹，并在其中建立package.json

$ mkdir [project name]
$ cd [project name]
$ npm init
###2.在项目目录下新建index.html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue example</title>
</head>
<body>
    <div>{{message}}</div>
    <script src="dist/build.js"></script>
</body>
</html>

###src文件夹，并在该文件夹下建立main.js

import Vue from 'vue'
new Vue({
    el:'body',
    data:{
        message:'test success!'
    }
});
##设置webpack
###1.安装webpack，webpack-dev-server以及相关的loaders
全局安装webpack，webpack-dev-server
$ npm install -g webpack
$ npm install -g webpack-dev-server
为项目安装其他依赖
$ npm i webpack-merge css-loader style-loader file-loader url-loader babel-core babel-loader babel-plugin-transform-runtime babel-preset-es2015 babel-preset-stage-0 babel-runtime vue vue-loader vue-html-loader vue-style-loader vue-hot-reload-api -D
webpack-merge：开发环境和生产环节的webpaak配置文件的配置合并<br/>
css-loader：编译写入css<br/>
style-loader：把编译后的css整合进html<br/>
file-loader：编译写入文件，默认情况下生成文件的文件名是文件名与MD5哈希值的组合<br/>
vue：vue主程序<br/>
vue-laoder：编译写入.vue文件<br/>
vue-html-loader：编译vue的template部分<br/>
vue-style-loader：编译vue的样式部分<br/>
vue-hot-reload-api：webpack对vue实现热替换<br/>
babel-core：ES2015编译核心<br/>
babel-loader：编译写入ES2015文档<br/>
babel-preset-es2015：ES2015语法<br/>
babel-preset-stage-0：开启测试功能<br/>
babel-runtime：babel执行环境<br/>

url-loader

这里介绍下url-loader，这个loader实际上是对file-loader的封装https://github.com/webpack/url-loader
比如CSS文件中有时候会这么写：

.demo{
    background-image: url('a.png');
}
module:{
    loaders:[
        {test:/\.(png|jpg)$/,loader:'url-loader?limit=8192'}
    ]
}
经过以上配置，当a.png小于8K就会自动将图片转换成base64编码，如果不小于，则不会转换。
这里顺便提一句，在module配置的时候，loader的写法：

module:{
    loaders:[
        {test:/\.jade$/,loader:'jade'}
        //这里配置了让webpack识别jade的loader，其他类似，比如.vue
        //用于css文件的loader有两种写法
        {test:/\.css$/,loader:'style!css'}
        {test:/\.css$/,loaders:['style','css']}
    ]
}
###2.配置webpack.config.js
在根目录下建立webpack.config.js，配置如下：

var path = require('path');
module.exports = {
  entry: './src/main.js',
  //定义webpack输出的文件，我们在这里设置了
  让打包后生成的文件放在dist文件夹下的build.js文件中
  output: {
    path: './dist',
    publicPath:'dist/',
    filename: 'build.js'
  },
  module: {
    loaders: [
      //转化ES6语法
      {
        test: /\.js$/,
        loader: 'babel',
        exclude: /node_modules/
      },
      //图片转化，小于8K自动转化为base64的编码
      {
        test: /\.(png|jpg|gif)$/,
        loader:'url-loader?limit=8192'
      }
    ]
  },
  //这里用于安装babel，如果在根目录下的.babelrc配置了，这里就不写了
  babel: {
     presets: ['es2015','stage-0'],
     plugins: ['transform-runtime']
  }
}
特别说明
如果要在.babelrc下配置babel，则需要在根目录下新建该文件，windows环境下，不能新建该txt文件然后改后缀，需要通过dos命令建立：

echo>.babelrc
通过该命令就可以建立babelde配置文件，用编辑器打开，修改里面的内容为：

{
  "presets": ["es2015", "stage-0"],
  "plugins": ["transform-runtime"]
}
完成该配置我们在命令中运行

$ webpack
打开index.html就可以看到浏览器中看到我们刚刚写的文字
至此我们实现了最基本的利用webpack打包vue
