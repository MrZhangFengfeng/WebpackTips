## 使用loader
在webpack中，可以使用`require('./a')`的方式来引入`a.js`。那loader是做什么的呢？

`loader`是webpack中一个重要的概念，它是指用来将一段代码转换成另一段代码的webpack插件。为什么需要将一段代码转换成另一段代码呢？
这是因为webpack实际上只能处理JS文件，如果需要使用一些非JS文件（比如`Coffee Script`），就需要将它转换成`JS`再require进来。当然，这个代
码转换的过程能做的远不止是`Coffee->JS`这么简单。

> 虽然本质上说，loader也是插件，但因为webpack的体系中还有一个专门的名词就叫插件（plugins），为避免混淆，后面不再将loader与插件混淆
> 说，后文中这将是两个相互独立的概念。
