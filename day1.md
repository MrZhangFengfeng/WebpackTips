## Webpack使用记录day1

### `Webpack.config.js`的编写
- 在webpack版本 1.*中 output可以直接写路径，在2.*里就要引入path.

      var webpack = require("webpack");
      var path = require("path")
      module.exports = {
          entry : './app/index.js',
          output : {
              path: path.resolve(__dirname,'./dist'),
              filename:'bundle.js'
          }
      }
      

> 入口文件不能直接写`entry : 'app/index.js'`。一定要以`./`开头,不然webpack会默认他是一个模块  然后去`nodemodules`里找。
> 写`./`表示这是我们自己的一个文件

- - - 
### Webpack安装不上？？
- 先全局安装  `npm i webpack -g`
- 再局部项目内安装   `npm i webpack --save -dev`

- - -
### Webpack-dev-server  搭建开发环境
webpack-dev-server 是一个小型的Node Express服务器，拓为通过webpack打包生成的资源文件提供web服务。
它能为我们做：
- 提供web服务
- 自动刷新
