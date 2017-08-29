## 使用loader
在webpack中，可以使用`require('./a')`的方式来引入`a.js`。那loader是做什么的呢？

`loader`是webpack中一个重要的概念，它是指用来将一段代码转换成另一段代码的webpack插件。为什么需要将一段代码转换成另一段代码呢？
这是因为webpack实际上只能处理JS文件，如果需要使用一些非JS文件（比如`Coffee Script`），就需要将它转换成`JS`再require进来。当然，这个代
码转换的过程能做的远不止是`Coffee->JS`这么简单。

> 虽然本质上说，loader也是插件，但因为webpack的体系中还有一个专门的名词就叫插件（plugins），为避免混淆，后面不再将loader与插件混淆
> 说，后文中这将是两个相互独立的概念。

- - -
### 用途

webpack处理的主要对象是JS文件，而我们知道JS文件是可以做很多事情的，比如编译`Less/SASS/CoffeeScript`，比如在页面中插入一段`HTML`，比
如修改替换文本文件等等。既然如此，我们能不能将这些能力集中到`webpack`上来呢，比如我在`require('a.coffee')`的时候，webpack自动帮我把它编译成JS文件，再当成JS文件引入进来？或者更极端一点，require('a.css')的时候，直接将CSS变成一段JS，用这段JS将样式插入DOM中呢？
**这些，正是loader要做的事情。**

- - -
### 用法

为了言之有物，以下皆以coffee-loader为例来说明。
`coffee-loader`的作用就是在引用`.coffee`文件时，自动转换成JS文件，这样可以省去额外的将`.coffee`变成`.js`的过程。

首先我们准备一个`example1.1.js`：

    var hello = require('coffee!./example1.2.coffee');
    hello.sayHello();

值得注意的是这里在require参数最前面加了`coffee!`，这个表示使用`coffee-loader`来处理文件内容。详细的机制稍后说明。
接下来，准备`example1.2.coffee`：

    class Hello
        constructor: (@name) ->

        sayHello: () ->
            alert "hello #{@name}"

    module.exports = new Hello 'world'
    
接下来，我们需要安装`coffee-loader`。webpack的每一个loader命名都是`xxx-loader`，在安装的时候需要将对应的loader装到项目目录下：

    npm install coffee-loader --save
然后编译：

    webpack example1.1.js bundle.js
    
打HTML，即可看到我们写的代码生效了。

`example1.2.coffee`编译后的代码也可以拿出来看一下：

    function(module, exports) {

        var Hello;

        Hello = (function() {
          function Hello(name) {
            this.name = name;
          }

          Hello.prototype.sayHello = function() {
            return alert("hello " + this.name);
          };

          return Hello;

        })();
        module.exports = new Hello('world');
    }
    
- - -
### 进阶

知道了loader的作用之后，可以再来多看一看loader的用法了。
首先，除了npm安装模块的时候以外，在任何场景下，loader名字都是可以简写的，`coffee-loader`和`coffee`是等价的，这意味着`require('coffee!./a.coffee')`和`require('coffee-loader!./a.coffee')`是等价的。

- - -

### 串联

`loader`是可以串联使用的，也就是说，一个文件可以先经过`A-loader`再经过`B-loader`最后再经过`C-loader`处理。而在经过所有的loader处理之前，webpack会先取到文件内容交给第一个loader。以我们后面经常会涉及的一个例子说明：

    require('style!css!./style.css');
    
这个例子的意思是将style.css文件内容先经过`css-loader`处理（路径处理、import处理等），然后经过`style-loader`处理（包装成JS文件，运行的时候直接将样式插入DOM中。）

- - -
### loader使用方法

loader的使用有三种方法，分别是：

- 在require中显式指定，即上面看到的用法
- 在配置项（webpack.config.js）中指定
- 在命令行中指定

第一种我们已经看过了，不再说明。

第二种，在配置项中指定是最灵活的方式，它的指定方式是这样：

    module: {
        // loaders是一个数组，每个元素都用来指定loader
        loaders: [{
            test: /\.jade$/,    //test值为正则表达式，当文件路径匹配时启用
            loader: 'jade',    //指定使用什么loader，可以用字符串，也可以用数组
            exclude: /regexp/, //可以使用exclude来排除一部分文件

            //可以使用query来指定参数，也可以在loader中用和require一样的用法指定参数，如`jade?p1=1`
            query: {
                p1:'1'
            }
        },
        {
            test: /\.css$/,
            loader: 'style!css'    //loader可以和require用法一样串联
        },
        {
            test: /\.css$/,
            loaders: ['style', 'css']    //也可以用数组指定loader
        }]
    }
    
在命令行中指定参数的用法用得较少，可以这样写：

    webpack --module-bind jade --module-bind 'css=style!css'
    
使用`--module-bind`指定loader，如果后缀和loader一样，直接写就好了，比如`jade`表示`.jade`文件用`jade-loader`处理，如果不一样，则需要显示指定，如`css=style!css`表示分别使用`css-loader`和`style-loader`处理`.css`文件。

- - -
### 总结

`loader`本质上做的是一个`anything` **to** `JS`的转换
