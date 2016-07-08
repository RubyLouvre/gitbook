# API

##静态成员

### scan
用于描述HTML,将包括ms-controller/ms-imporant的元素的outerHTML取出来,
变成对应的vm的render方法, 最终将里面的ms-*或双花括号变成vm中的属性与方法

`注意`: avalon2不会像avalon1那样将ms-*属性去掉了

有两个参数
1. 元素节点

```javascript
avalon.scan(document.body)
vm.$watch('onReady', function(){
    //页面上每个ms-controller, ms-important元素
    //在其区域内的所有ms-*指令被扫描后会执行
})
```
>onReady回调,在2.1.0新加入,只会调用一次!

```html
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <script src="../dist/avalon.js"></script>
        <script>
            function heredoc(fn) {
                return fn.toString().replace(/^[^\/]+\/\*!?\s?/, '').
                        replace(/\*\/[^\/]+$/, '').trim().replace(/>\s*</g, '><')
            }
            var v123 = heredoc(function () {
                /*
                 <div ms-controller="test2">
        <p ms-click="@alert">123</p>
                 <wbr  ms-widget="{is:'ms-span'}"/>
                 </div>
                 */
            })
            var v456 = heredoc(function () {
                /*
                 <div ms-controller="test3">
         <p ms-click="@alert">456</p>
                 <wbr  ms-widget="{is:'ms-span'}"/>
                 </div>
                 */
            })
        </script>
        <script>

            var vm = avalon.define({
                $id: 'test',
                tpl: "",
                switch1: function () {
                    setTimeout(function () {
                       vm.tpl = v123
                    })

                },
                switch2: function () {
                    setTimeout(function () {
                        vm.test.tpl = v456
                    })

                }
            });
            vm.$watch('onReady', function(){
                avalon.log('vm1 onReady')
            })
            var vm2 = avalon.define({
                $id: 'test2',
                ddd: 'aaaa',
                alert: function(){
                    avalon.log('????')
                }
            });
            vm2.$watch('onReady',function(){
                avalon.log('vm2 onReady')
            })
            var vm3 = avalon.define({
                $id: 'test3',
                ddd: 'bbbb',
                alert: function(){
                    avalon.log('!!!!')
                }
            });
            vm3.$watch('onReady',function(){
                avalon.log('vm3 onReady')
            })
            var vm4 = avalon.define({
                $id: 'test4',
                fff: 'rrrr',
                alert: function(){
                    avalon.log('!!!!')
                }
            });
            vm4.$watch('onReady',function(){
                avalon.log('vm4 onReady')
            })
            avalon.component('ms-span', {
                template: "<span ms-click='@click'>{{@ddd}}</span>",
                defaults: {
                    ddd:'3333',
                    click: function(){
                        avalon.log('inner...')
                    }
                }
            });

        </script>
    </head>
    <body ms-controller="test">
        <div ms-html="@tpl"></div>
        <button ms-click="@switch1">aaaa</button>
        <button ms-click="@switch2">bbbb</button>
        <div ms-important="test4">
            {{@fff}}
        </div>

    </body>
</html>
```

### define
创建一个vm对象,必须指定$id,详见[这里](http://avalonjs.coding.me/cn/doc.html#conpect_vmodel)
```javascript
avalon.define({
  $id: 'aaa',
  bbb: 1
})
```


### noop

空函数

### rword

切割字符串为一个个小块，以空格或逗号分开它们，结合replace实现字符串的forEach
```javascript
"aaa,bbb,ccc".replace(avalon.rword, function(a){
  console.log(a)//依次打出 aaa, bbb, ccc
  return a
})
```

### log

类似于console.log,但更安全,因为IE6没有console.log,而IE7下必须打开调试界面才有console.log

可以传入多个参数

```javascript
avalon.log('aaa','bbb')
```

### warn
```javascript
avalon.warn('aaa','bbb')
```

类似于console.warn, 不存在时内部调用avalon.log

### error

抛出一个异常对象

1. 字符串,错误消息
2. 可选, Error对象的构造器(如果是纯字符串,在某些控制台下会乱码,因此必须包成一个对象)

```javascript
avalon.error('aaa')
```

### oneObject

将一个以空格或逗号隔开的字符串或数组,转换成一个键值都为1的对象
1.  以空格或逗号隔开的字符串或数组, "aaa,bbb,ccc",['aaa','bbb','ccc']
2.  生成的对象的键值都是什么值,默认1

```javascript
avalon.oneObject("aaa,bbb,ccc")//{aaa:1,bbb:1,ccc:1}
```

### isWindow

判定是否为一个window对象
```javascript
avalon.isWindow('ddd')//false
```

### isFunction

判定是否为一个函数
```javascript
avalon.isFunction(window.alert)//true
```

### isObject

是否为一个对象, 返回布尔

```
avalon.isObject({a:1,b:2})//true
avalon.isObject(window.alert) //true
avalon.isObject('aaa') //false
```

### type

取得目标的类型 
```javascript
avalon.type('str') //'string'
avalon.type(123) //'number'
avalon.type(/\w+/) //'regexp'
avalon.type(avalon.noop) //'function'
```

## isPlainObject

判定是否为一个纯净的JS对象, 不能为window, 任何类(包括**自定义类**)的实例,元素节点,文本节点
>用于内部的深克隆, VM的赋值, each方法
```javascript
avalon.isPlainObject({}) //true
avalon.isPlainObject(new Object) //true
avalon.isPlainObject(Object.create(null)) //true
var A = function(){}
avalon.isPlainObject(new A) //false
```

### each

用于遍历对象或类数组,数组
```javascript
avalon.each(arr, function(index, el){
  
})
```

### mix

用于合并多个对象或深克隆,类似于jQuery.extend
>注意,不要加VM批量赋值时用它

```javascript
//合并多个对象,返回第一个参数
target = avalon.mix(target, obj1, obj2, obj3)
//深拷贝模式, 要求第一个参数为true, 返回第二个参数
target = avalon.mix(true, target, obj1, obj2)
```


### vmodels

放置所有用户定义的vm及组件指令产生的组件vm

```javascript
avalon.define({$id:'aaa'})
console.log(avalon.vmodels) // {aaa: vm}
```

### filters

放置所有过滤器,也可以在上面添加你的自定义过滤器

```javascript
avalon.filters.haha = function(str){
  return str + 'haha'
}

```

### components

放置所有用avalon.component方法添加的组件配置对象

### validators

放置所有验证规则,详见[这里](http://avalonjs.coding.me/cn/duplex-validator.html)


### parsers

放置所有数据格式转换器

### slice

用于转换类数组对象为纯数组对象

1. 目示对象
2. 开始索引, 默认为0
3. 结束索引, 默认为总长

```javascript
avalon.slice(document.body.childNodes)
```

### directive

定义一个指令, 请翻看源码,看css, attr, html是怎么玩的
1. 指令名
2. 配置对象

```javascript
avalon.directive('html', {
    parse: function (copy, src, binding) {
       /*
       copy: 每次VM的属性时,avalon就会调用vm.$render方法重新生成一个虚拟DOM树
       ,这个copy就是新虚拟DOM树的一个子孙节点

       src: 第一次调用vm.$render方法生成的虚拟DOM树的某个节点,它将永驻于内存中,
       除非其对应的真实节点被移除.以后不断用src与新生成的copy进行比较,
       然后应用 update方法,最后用copy的属性更新src与真实DOM.

       binding 配置对象,包括name,expr,type,param等配置项

       return 用于生成vtree的字符串 */
    },
    diff: function (copy, src, name) {
       /*
       copy 刚刚生成的虚拟DOM树的某个子孙节点
       src 最初的虚拟DOM树的节点
       name 要比较的指令名
       */
    },
    update: function (node, vnode, parent) {
        /*
       node 当前的真实节点
       vnode  此真实节点在虚拟树的相应位置对应的虚拟节点
       parent node.parentNode
       */
    }
})


```

### effect
用于定义一个动画效果,详见[这里](directives/ms-effect.md)

### component
用于定义一个组件,详见[这里](http://avalonjs.coding.me/cn/doc.html#directive_com)

### range

用于生成整数数组
1. start 开始值, 
2. end 结束值, 可以为负数
3. step 每隔多少个整数

```javacript
avalon.range(10)
=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
avalon.range(1, 11)
=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
avalon.range(0, 30, 5)
=> [0, 5, 10, 15, 20, 25]
avalon.range(0, -10, -1)
=> [0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
avalon.range(0)
```

### hyphen

转换为连字符线风格

```javascript
avalon.hyphen('aaaAaa') //aaa-aaa
```

### camelize

转换为驼峰风格
```javascript
avalon.hyphen('aaa-Bbb') //aaaBBB
```

### bind
添加事件
1. 元素节点,window, document
2. 事件名
3. 回调
4. 是否捕获,可选

```javascript
avalon.bind(window, 'load',loadFn)

```

### unbind
移除事件
参数与bind方法相同

### parseHTML 
转换一段HTML字符串为一个文档对象
```javascript
avalon.parseHTML('<b>222</b><b>333</b>')
```

### innerHTML
类似于`element.innerHTML = newHTML` ,但兼容性更好
1. node 元素节点
2. 要替换的HTML字符串
```javascript
var elem = document.getElementById('aaa')
avalon.innerHTML(elem, '<b>222</b><b>333</b>')
```

### clearHTML
用于清空元素的内部
```javascript
avalon.clearHTML(elem)
```

### contains
判定A节点是否包含B节点
1. A 元素节点
2. B 元素节点

```javascript
avalon.contains(document.body, document.querySelector('a'))
```

### Array

#### Array.merge

合并两个数组 
```javascript
avalon.Array.merge(arr1,arr2)
```

#### Array.ensure

只有当前数组不存在此元素时只添加它
```javascript
avalon.Array.ensure([1,2,3],3)//[1,2,3]

avalon.Array.ensure([1,2,3],8)//[1,2,3,8]

```

#### Array.removeAt

移除数组中指定位置的元素，返回布尔表示成功与否
```javascript
avalon.Array.removeAt([1,2,3],1)//[1,3]
```

#### Array.remove

移除数组中**第一个**匹配传参的那个元素，返回布尔表示成功与否
```javascript
avalon.Array.remove(['a','b','c'],'a')//['b','c']
```

##被修复了的ecma262方法

1. String.prototype.trim
2. Function.prototype.bind
3. Array.isArray<br>
   avalon内部使用它判定是否数组
4. Object.keys
5. Array.prototype.slice<br>
   IE6-8下有BUG,这里做了修复
6. Array.prototype.indexOf
7. Array.prototype.lastIndexOf
8. Array.prototype.forEach
9. Array.prototype.map
10. Array.prototype.filter
11. Array.prototype.some
12. Array.prototype.every


##实例成员

### avalon

1. 可以传入元素节点,文档对象,window对象构成一个avalon实例

```
var a = avalon(document.body)
console.log(a[0]) //document.body
console.log(a.element) // document.body
```

>与jQuery对象不同的是,它只能传入一个元素或对象,
而jQuery是可以传入CSS选符串获得一大堆元素节点,组成一个类数组对象
avalon作为一个MVVM框架, 目的是实现最小化刷新, 通常没有操作一大堆节点的需求
>
>avalon的实例方法主要供框架内部使用,除了自己写组件,所有操作的DOM的需求请使用ms-*
如果要使用第三方的jQuery插件,请务必将它们封装成**avalon的组件**

<pre>
@img("avalon.api.png",763,628)
</pre>

## css

用于获取或修改样式,自动修正厂商前缀及加px,与jQuery的css方法一样智能
```javascript
avalon(elem).css('float','left')
```

## width
取得目标的宽,不带单位,如果目标为window,则取得窗口的宽,为document取得页面的宽
```javascript
avalon(elem).width()
```
## height
取得目标的高,不带单位,如果目标为window,则取得窗口的高,为document取得页面的高

### innerWidth

类似于jQuery的innerWidth

### innerHeight
类似于jQuery的innerHeight


### outerWidth
类似于jQuery的outerWidth

### outerHeight
类似于jQuery的outerHeight

### offset
取得元素的位置, 如 `{top:111, left: 222}`


### attr

用于获取或修改属性
```javascript
avalon(elem).attr('title','aaa')
```
>注意,这个方法内部只使用setAttribute及getAttribute方法,非常弱
建议使用**ms-attr**指令实现相同的功能

### addClass
添加多个类名, 以空格隔开
```javascript
avalon(elem).addClass('red aaa bbb')
```

### removeClass
移除多个类名, 以空格隔开
```javascript
avalon(elem).removeClass('red aaa bbb')
```

### hasClass
判定目标元素是否包含某个类名

### toggleClass
切换多个类名
1. 类名,以空格隔开
2. 可选, 为布尔时强制添加或删除这些类名


```javascript
avalon(elem).toggleClass('aaa bbb ccc')
```

### bind
类似于avalon.bind

```javascript
avalon(elem).bind('click', clickFn)
```

### unbind
类似于avalon.unbind