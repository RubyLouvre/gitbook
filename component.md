# 组件
avalon拥有两大利器，强大的组件化功能以应对**复杂墙**问题，顶级的虚拟DOM机制来解决**性能墙**问题。

组件可谓是指令的集合，但`1＋1>2`

##组件容器
在avalon2中，有4类标签可以定义组件。分别是wbr, xmp, template, 及ms-开头的自定义标签。其他标签如果使用了ms-widget指令会抛错。

说起自定义标签。之前1.5为了兼容IE6－8，是使用旧式的带命名空间的标签（即中间带冒号的那种标签）作为容器，而Web Component则是使用中间带杠的标签，如`<ms-button>`,风格大相径庭。显然后者是主流，是未来！

经过一番研究，发掘出三大原生标签作为组件定义时的容器。
```
xmp, wbr, template
```

`xmp`是闭合标签，与div一样，需要写开标签与闭标签。但它里面的内容全部作为文本存在，因此在它里面写带杠的自定义标签完全没问题。并且有一个好处时，它是能减少真实DOM的生成（内部就只有一个文本节点）。
```html
<xmp ms-widget="@config">
<ms-button ms-widget="@btn1"><ms-button>
<div>
<ms-tab ms-widget="@tab"><ms-tab>
</div>
</xmp>
```

`wbr`与`xmp`一样，是一个很古老的标签。它是一个空标签，或者说是半闭合标签，像br, area, hr, map, col都是空标签。我们知道，自定义标签都是闭合标签，后面部分根本不没有携带更多有用的信息，因此对我们来说，没多大用处。

```html
<wbr ms-widget="@config" />
```

`template`是HTML5添加的标签，它在IE9－11中不认，但也能正确解析得出来。它与xmp, wbr都有一个共同特点，能节省我们定义组件时页面上的节点规模。xmp只有一个文本节点作为孩子，wbr没有孩子，template也没有孩子，并且用content属性将内容转换为文档碎片藏起来。

```html
<template ms-widget="@config" >
<ms-dialog ms-widget="@config"></ms-dialog>
</template>
```

当然如果你不打算兼容IE6－8，可以直接上ms-button这样标签。自定义标签比起上面三大容器标签，只是让你少写了is配置项而已，但多写了一个无用的闭标签。

```html
<ms-dialog ms-widget="@config" ><ms-panel ms-widget="@config2"></ms-panel></ms-dialog>
<!--比对下面的写法-->
<xmp ms-widget="@config" ><wbr ms-widget="@config2"/></xmp>
<wbr ms-widget='{is:"ms-button"}'/>
<template ms-widget='{is:"ms-button"}'></template>
```

如果你想在页面上使用ms-button组件，只能用于以下四种方式

```html
<!--在自定义标签中，ms-widget不是必须的-->
<ms-button></ms-button>
<!--下面三种方式，ms-widget才是存在，其中的is也是必须的-->
<xmp ms-widget='{is:"ms-button"}'></xmp>
<wbr ms-widget='{is:"ms-button"}'/>
<template ms-widget='{is:"ms-button"}'></template>
```

##配置对象

##插槽元素

##组件定义

##生命周期

##注意事项