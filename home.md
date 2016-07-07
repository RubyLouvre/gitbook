# 简介

avalon2是一款基于虚拟DOM与属性劫持的 迷你、 易用、 高性能 的 前端MVVM框架， 适用于各种场景, 兼容各种古老刁钻浏览器, 吸收最新的技术成果, 能迅速堆砌组件与应用。

## [谁在使用avalon](who.md)

## 使用方式

avalon2： https://github.com/RubyLouvre/avalon/tree/2.1.0/dist

CDN: http://www.bootcdn.cn/avalon.js/


> `注意`, 不要使用avalon2.0s,avalon2.0b1,avalon2.0b2,那是很早期的beta版本


```javascript
npm install avalon2
```

avalon2是使用一份源码编译成N个版本:

```
avalon 支持IE6+及古老的W3C浏览器(判定标准是 
       这些浏览器是否支持VBScript, __defineSetter__, __defineGetter__)

avalon.modern 支持IE10+及较新的W3C浏览器(判定标准是 
       这些浏览器是否支持Object.defineProperty, addEventListener)

avalon.next 支持IE12+(edge)及chrome49, firefox49(判定标准是 
       这些浏览器是否支持Proxy, document.registerElement)
```

##早期版本的支持


[1.4的入门教程](http://www.cnblogs.com/rubylouvre/p/3181291.html)
[1.4的仓库地](https://github.com/RubyLouvre/avalon/tree/1.4.7.2/dist)

[1.5的入门教程](http://www.cnblogs.com/rubylouvre/p/4783966.html)
[1.5的仓库地址](https://github.com/RubyLouvre/avalon/tree/1.5.6/dist)


[旧的官网地址](http://avalonjs.github.io/)

avalon论坛 : [http://www.avalon.org.cn/](http://www.avalon.org.cn/)


## avalon2的架构图
![](http://avalonjs.coding.me/share/img/4.jpg)

```html
<!DOCTYPE html>
<html>
    <head>
        <title>first example</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="./dist/avalon.js"></script>
        <script>
            var vm = avalon.define({
                $id: "test",
                name: "司徒正美"
            })
        </script>
    </head>

    <body ms-controller="test">
        <input ms-duplex="@name">
        <p>Hello,{{@name}}!</p>
    </body>
</html>
```

这里面涉及一些知识点

1. vm,使用avalon.define方法生成,必须带$id属性
2. 指令,以ms-开头的属性及双花括号的插值表达式
3. 圈定作用域,使用ms-controller告诉框架,只处理这个范围内的标签
4. 引导符,使用@来告诉框架这些变量是来自vm的

## avalon起源
----------------------

avalon 是一个简单易用迷你的MVVM框架，它最早发布于2012.09.15， 为解决同一业务逻辑存在各种视图呈现而开发出来的。 事实上，这问题其实也可以简单地利用一般的前端模板加jQuery 事件委托 搞定， 但随着业务的膨胀， 代码就充满了各种选择器与事件回调，难以维护。 因此彻底的将业务与逻辑分离，就只能求助于架构。 最初想到的是MVC，尝试过backbone，但代码不降反升，很偶尔的机会，碰上微软的WPF， 优雅的MVVM架构立马吸引住我，我觉得这就是我一直寻找的解决之道。

avalon将所有前端代码彻底分成两部分，视图的处理通过绑定实现（angular有个更炫酷的名词叫指令）， 业务逻辑则集中在一个个叫VM的对象中处理。我们只要操作VM的数据，它就自然而然地神奇地同步到视图。 显然所有神秘都有其内幕，C#是通过一种叫访问器属性的语句实现，那么JS也有对应的东西。 感谢上帝，IE8最早引入这东西（`Object.defineProperty`），可惜有BUG，但带动了其他浏览器实现它， IE9+便能安全使用它。 对于老式IE，我找了好久，实在没有办法，使用VBScript实现了。

Object.defineProperty或VBS的作用是将对象的某一个属性，转换一个setter与getter， 我们只要劫持这两个方法，通过Pub/Sub模式就能偷偷操作视图。为了纪念WPF的指引， 我将此项目以WPF最初的开发代号avalon来命名。 它真的能让前端人员脱离DOM的苦海，来到数据的乐园中！

avalon的所有指令都是以`ms-*`命名,ms是用纪念我之前的一个框架`mass Framework`!