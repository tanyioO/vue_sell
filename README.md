# vue_sell

> 使用vue.js开发的一个类似饿了么的webapp。这是根据黄奕老师的课程开发的项目，
> 上传源码只是为了保留这段时间以来学习的成果，同时熟悉git的基本操作，如有侵权我会立即删除。

### 项目技术架构

* vue
* ES6
* vue-cli               （脚手架工具）
* vue-resource          （后台数据交互）
* vue-router		    （前端路由实现单页应用）
* better-scroll		    （列表滚动插件）
* html的localStorage    （浏览器端键值对存储）
* webpack               （构建打包工具）
* stylus                （css预处理）

### 项目图片演示

* 商品详情页（点击单个商品进入其详情）

![Image text](https://raw.githubusercontent.com/graphicsd/vue_sell/master/img-forder/1.png )
![Image text](https://raw.githubusercontent.com/graphicsd/vue_sell/master/img-forder/2.png )

* 弹出购物车、公告以及优惠信息

![Image text](https://raw.githubusercontent.com/graphicsd/vue_sell/master/img-forder/3.png )
![Image text](https://raw.githubusercontent.com/graphicsd/vue_sell/master/img-forder/4.png )

* 商家评价、商家基本信息

![Image text](https://raw.githubusercontent.com/graphicsd/vue_sell/master/img-forder/5.png )
![Image text](https://raw.githubusercontent.com/graphicsd/vue_sell/master/img-forder/6.png )

### 总结

> 由于在学习课程以及《vue.js权威指南》时大多接受的是较低版本的知识，开发的过程中不可避免地遇到过很多问题，包括代码问题和配置问题。例如vue-router（路由商品、评价、商家三个板块） 2.0从1.0的改变就让我在开发初期卡了壳，后来通过在官网的学习解决了。

> 还有就是在完成购物车小球动画的过程时，最初的实现是在商品组件（goods）中引入添加-删除组件（cart-control），监听cart-control组件点击后的$emit事件然后再通过$refs方法调用购物车组件（shop-cart）的方法执行小球飞入购物车动画。但是如果cart-control组件是在处于另一个组件的包裹时，如单个商品详情页和点击购物车弹出的购物车清单中，通过cart-control组件$emit传递的自定义时间无法被goods组件监听到，不能触发动画。我查看了vue官网以及很多案例关于自定义事件的信息都没能解决，最后是通过一个自己想出来的比较笨的方法，即让自定义事件通过冒泡的方式先传递到上层组件然后再传递到外层组件的解决了这个问题。

> 总的来说通过这个项目的学习，让我对于vue的实战有了比较深刻的了解，也加深很多比如flex弹性布局，css sticky footer布局，两层盒模型包装实现内外层动画，better-scroll列表滚动原理等常用编程技巧的理解，受益匪浅。
### 安装设置

###### 通过`npm`安装项目第三方依赖模块(需已安装[Node.js](https://nodejs.org/))
``` 
npm install
```
###### 浏览器预览(http://localhost:8080)
``` 
npm run start
```
> tips：按F12打开控制台选择移动设备预览效果最佳。

