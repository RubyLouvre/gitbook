#组件绑定

avalon2最引以为豪的东西是，终于有一套强大的类Web Component的组件系统。这个组件系统媲美于React的JSX，并且能更好地控制子组件的传参。

avalon自诞生以来，就一直探索如何优雅的定义组件使用组件。从avalon1.4的ms-widget，到avalon1.5的自定义标签。而现在的版本恰好是它们的结合体，并从web component那里借鉴了slot插入点机制及生命周期管理，从react那里抄来了render字符串模板。

现在定义一个组件只需要这样写：
```html
<wbr ms-widget="@obj" />
```   

wbr元素是一个类似于br的HTML元素，所有浏览器都支持。 在avalon2中，有4类标签可以定义组件。分别是wbr, xmp, template, 及ms-开头的自定义标签。

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
在JS中，我们是这样使用它

```html
<!DOCTYPE html>
<html>
    <head>
        <title>ms-validate</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge" /> 
        <script src="../dist/avalon.js"></script>
        <script>
            var vm = avalon.define({
                $id: 'test',
                button: {//注意这里不能以 $开头
                    buttonText: "VM内容"
                }
            })

            avalon.component('ms-button', {
                template: '<button type="button"><span><slot name="buttonText"></slot></span></button>',
                defaults: {
                    buttonText: "默认内容"
                },
                soleSlot: 'buttonText'
            })

        </script>
    </head>

    <body ms-controller="test">
    <!--在自定义标签中，ms-widget不是必须的-->
    <ms-button ></ms-button>
    <!--下面三种方式，ms-widget才是存在，其中的is也是必须的-->
    <xmp ms-widget='{is:"ms-button"}'></xmp>
    <wbr ms-widget='{is:"ms-button"}'/>
    <template ms-widget='{is:"ms-button"}'></template>
</body>
</html>
 ```

但这样我们就不好控制组件的更新。我们改一改。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>ms-validate</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge" /> 
        <script src="../dist/avalon.js"></script>
        <script>
            var vm = avalon.define({
                $id: 'test',
                button: {//注意这里不能以 $开头
                    buttonText: "按钮内容"
                }
            })

            avalon.component('ms-button', {
                template: '<button type="button"><span><slot name="buttonText"></slot></span></button>',
                defaults: {
                    buttonText: "button"
                },
                soleSlot: 'buttonText'
            })

        </script>
    </head>

    <body ms-controller="test">
    <!--在自定义标签中，ms-widget不是必须的-->
    <ms-button ms-widget="@button"></ms-button>
    <!--下面三种方式，ms-widget才是存在，其中的is也是必须的-->
    <xmp ms-widget='[{is:"ms-button"},@button]'></xmp>
    <wbr ms-widget='[{is:"ms-button"},@button]'/>
    <template ms-widget='[{is:"ms-button"},@button]'></template>
</body>
</html>
```

这样我们直接操作 vm中的button对象中对应属性就能更新组件了。这比原来avalon1.*好用一万倍。

此外，avalon2还支持Web Components规范中所说的DOM插入点机制，它是用来配置
一些字符串长度很长的属性。比如说ms-tabs组件，通常有一个数组属性，
而数组的每个元素都是一个很长的文本，用于以应一个面板。这时我们可以在自定义标签的
innerHTML内，添加一些slot元素，并且指定其name就行了。

当我们不使用slot，又不愿意写面板内部放进vm时，你的页面会是这样的：
```html
<ms-tabs ms-widget='{panels:[
"第一个面板的内部dfsdfsdfsdfdsfdsf",
"第二个面板的内部dfsdfsdfsdfdsfdsf"
"第三个面板的内部dfsdfsdfsdfdsfdsf"]  }'
></ms-tabs>
```
使用了slot后

```html
<ms-tabs>
<div slot='panels'>第一个面板的内部dfsdfsdfsdfdsfdsf</div>
<div slot='panels'>第二个面板的内部dfsdfsdfsdfdsfdsf</div>
<div slot='panels'>第三个面板的内部dfsdfsdfsdfdsfdsf</div>
</ms-tabs>
```
而你的组件是这样定义

```
<ms-tabs>
<slot name='panels'></solt>
<slot name='panels'></solt>
<slot name='panels'></solt>
</ms-tabs>
```
上面的div会依次替代slot元素。

此外，如果我们只有一个插槽，不想在页面上slot属性，那么可以在组件里使用soleSlot。

注意avalon.component的第二个参数，是一个对象，它里面只有4个配置项， template是必须的， defaults、 soleSlot, getTemplate是可选的。

组件属性的寻找顺序，会优先找配置对象，然后是innerHTML，然后是defaults中的默认值.我们可以看一下测试

> 有关avalon.component的详细用法,可见这里

```
    <script>    
div.innerHTML = heredoc(function () {
        /*
         <div ms-controller='widget0' >
         <template ms-widget="{is:'ms-button'}">{{@btn}}</template>
         <ms-button>这是标签里面的TEXT</ms-button>
         <ms-button ms-widget='{buttonText:"这是属性中的TEXT"}'></ms-button>
         <ms-button></ms-button>
         </div>
         */
    })
    vm = avalon.define({
        $id: 'widget0',
        btn: '这是VM中的TEXT'
    })
    avalon.scan(div)
    setTimeout(function () {
        var span = div.getElementsByTagName('span')
        expect(span[0].innerHTML).to.equal('这是VM中的TEXT')
        expect(span[1].innerHTML).to.equal('这是标签里面的TEXT')
        expect(span[2].innerHTML).to.equal('这是属性中的TEXT')
        expect(span[3].innerHTML).to.equal('button')
        vm.btn = '改动'
        setTimeout(function () {
            expect(span[0].innerHTML).to.equal('改动')
            done()
        })
    })
    </script>

```