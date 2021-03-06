<!-- toc -->
# 常见问题

##如何兼容到IE6

avalon只是提供数据绑定功能，没有JS动画，AJAX等模块。这需要其他库来提供，此外，IE的低版本不支持
JSON，XMLHttpRequest对象，高版本不支持Promise。这需要我们额外引入其他库来支持


首先我们需要引入IE7.js
https://github.com/roylory/ie7-js/blob/master/lib/IE7.js

然后JSON.js
https://github.com/bestiejs/json3/tree/master/lib

最后是Promise库

https://github.com/RubyLouvre/avalon/blob/master/test/promise.js

```html
<script src="./pathtoie7/IE7.js"></script>
<script src="./pathtojson/json.js"></script>
<script src="./pathtopromise/promise.js"></script>
<script src="./pathtoavalon2/avalon2.js"></script>
<script src="./pathtoyourcode/main.js"></script>
```
当然，前4个常用的库可以打包，或者全部打包

开发时，最好一边开发一边在IE8下测试（因为IE8下有开发者工具，能模拟IE6的情况），不要全部开发完再测试IE6


## 如何隐藏首屏加载页面时出现的花括号 ##
`答`:在页面上添加一个样式

```css
.ms-controller{
   visibility: hidden
}
```

使用在ms-controller, ms-important的元素上加上这个ms-controller类名
```
<div ms-controller="test" class="ms-controller">{{@aaa}}</div>

```
## IE6-8下为vm的数组重新赋给一个新数组失败? ##
[具体案例](https://github.com/RubyLouvre/avalon/issues/1531)
```javascript
vm.arr2 = vm.arr1 //报错

```
记住,任何时候,不能将vm中的数组或子对象取出来,再用它们赋给vm的某个数组或子对象,
因为放在vm中的数组与子对象已经变成VM了,而VM重写VM不被允许的.

并且你要保证原数据不被污染,需要使用深拷贝.
```javascript
vm.arr2 = avalon.mix(true, [], arr1)
vm.obj2 = avalon.mix(true, {}, obj1)

```
你也可以这样,将原数据转换为纯数据就行了
```javascript
vm.arr2 = vm.arr1.$model //正常

```
## 为什么我的指令没有效果? ##

```html
<p title="att-{{ddd}}">例子!</p>

```  
     
`答`:因为avalon只会对`ms-*`属性敏感, 另外, 花括号里的ddd要加上`@`,即
```html
<p ms-attr="{title: 'att-'+ @ddd}">例子!</p>

```

## 如何在页面扫描后执行一个回调 ##
`答`: avalon2支持onReady方法

```javascript
var vm = avalon.define({
   $id: 'test',
    ddd: false
})
vm.$watch('onReady', function(){
 //当test这个区域第一次扫描后会被执行
})
```
[详见API文档页](http://avalonjs.coding.me/cn/api.html#scan)

## 对表单元素的值输入进行限制

`答`: avalon提供了4个转换器,那是将value值上传到vm时用,也提供了许多格式式过滤器,
但在ms-duplex格式化很容易死循环,因此建议在另加input事件做处理.

比如说我们限制只能输入数字
```html
<script>
    avalon.define({
        $id: 'test',
        aaa: 111,
        fix: function(e){
            e.target.value = e.target.value.replace(/\D+/, '')
        }
    })

</script>

<body :controller="test">
    <input :duplex-number="@aaa" :input="@fix"/>{{@aaa}}
</body>


```


## 如果手动执行验证 ##

`答`: ms-validate提供了各种全自动的验证.但可能大家需要手动执行验证表单
在ms-validate的配置对象上添加一个onManual,页面被扫描后,你就可能拿这个方法来自己执行验证

```html
<!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>Drag-Drop</title>
        <script src="../dist/avalon.js"></script>
    </head>
    <body>
        <div ms-controller="validate1">
            <form ms-validate="@validate">
                <p><input ms-duplex="@aaa" placeholder="username"
                          ms-rules='{required:true,chs:true}' >{{@aaa}}</p>
                <p><input type="password" id="pw" placeholder="password"
                          ms-rules='{required:true}' 
                          ms-duplex="@bbb" /></p>
                <p><input type="password" 
                          ms-rules="{required:true,equalto:'pw'}" placeholder="再填一次"
                          ms-duplex="@ccc | change" /></p>
                <p><input type="submit" value="submit"/></p>
            </form>
        </div>
        <script>
var vm = avalon.define({
    $id: "validate1",
    aaa: "",
    bbb: '',
    ccc: '',
    validate: {
        onManual:avalon.noop,//IE6-8必须指定,avalon一会儿会重写这方法
        onError: function (reasons) {
            reasons.forEach(function (reason) {
                console.log(reason.getMessage())
            })
        },
        onValidateAll: function (reasons) {//它会被onManual调用
            if (reasons.length) {
                console.log('有表单没有通过')
            } else {
                console.log('全部通过')
            }
        }
    }
})

setTimeout(function(){
    vm.validate.onManual()
})
        </script>
    </body>
</html>
```

## 页面用了avalon后, 元素间没有距离了

答: 因为avalon在页面加载好后,会清掉所有空白文本,减少页面的节点数,从而减少以后diff的节点个数. 详见[这里](virtualdom.md).

## 组件的注意事项 ##

`答`: 最好指定全局不重复的$id,特别在ms-for循环中,必须指定$id
```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="UTF-8">
    <script src="../dist/avalon.js"></script>
    <script>
        var vm = avalon.define({
            $id: 'test',
            tests: [0,1]
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
<body>
<ul ms-controller="test">
     <li ms-for="(index,test) in @tests">
        <span ms-text="test"></span>
        <wbr ms-widget='{is:"ms-button",$id:"btn_"+index}'/>
    </li>
</ul>
</body>
</html>
```

##ajax提交出问题

请确保你提交的数据是纯JS数据，而不是vm。请见[数据模型](http://avalonjs.coding.me/vm.html#数据模型)

```javascript
function submitData(){
    var data = vm.Item.$model //如果Item是数组，在IE6－8，请改用
    // data = vm.Item.toJSON(), 如果想提交整个vm，请改用
    // data = vm.$model
    $.ajax({
          url: '../sdfwds/dsfds/dsfsd.action',
          data:data,
          type: 'GET',
          success: function(){

          }

    })
}
```

## 为什么我的日期不能同步
```javascript
var vm = avalon.define({
  $id:'aaa',
  date: new Date
})

setTimeout(function(){
  vm.date = new Date
}, 1000)

```

`答`: 因为avalon只会对number, string, boolean, 纯对象, 纯数组这几个类型同步, 其他类型需要转换.
将上面的`new Date`改成`new Date - 0`即可

## 如何将页面模块化？
`答`: https://github.com/RubyLouvre/avalon/issues/1655