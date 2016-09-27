# 类型转换器

由于我们从表单元素拿到的所有东西都是字符串或字符串数组, 因此从`avalon0.*`起就提供了几个ms-duplex的辅助指令来实现数据类型转换功能

格式为`ms-duplex-xxxx`

默认提供了4个数据转换器

* ms-duplex-string

* ms-duplex-number

* ms-duplex-boolean

* ms-duplex-checked


具体用法详见双工绑定

```html
<input ms-duplex-number='@aaa'>{{@aaa}}
```         
我们也可以自定义类型转换器, 直接在avalon.parser上添加
```javascript
parsers: {
    number: function (a) {
        return a === '' ? '' : /\d\.$/.test(a) ? a : parseFloat(a) || 0
    },
    string: function (a) {
        return a === null || a === void 0 ? '' : a + ''
    },
    boolean: function (a) {
        if(a === '')
            return a
        return a === 'true'|| a == '1'
    }
},
```         
上面number, string, boolean就是ms-duplex-xxx的实际转换方法, a为元素的value值. ms-duplex-checked比较特殊它是使用checked属性,因此不在其列.



上面number, string, boolean就是ms-duplex-xxx的实际转换方法, a为元素的value值. ms-duplex-checked比较特殊它是使用checked属性,因此不在其列.

我们看一下转换器的用法。
```html
  <div ms-controller="test">
      <input type="checkbox" value="1" ms-duplex="@aaa">
      <input type="checkbox" value="2" ms-duplex="@aaa">
      <input type="checkbox" value="3" ms-duplex="@aaa">
      <p>{{@aaa}}</p>
  </div>
  <script>
  var vm = avalon.define({
      $id: 'test',
      aaa: [1, 2]
  })
  </script>
```
如果不使用ms-duplex-number转换器，最开始时，第一个，第二个checkbox是能正确选中，但当我们点击第二个时，发现下面的文本没有变化。 因此我们是尝试从[1,2]数组中移除"2"这个字符串，当然移除不了，没有效果。

当我们改成这样
```html
<input type="checkbox" value="1" ms-duplex-number="@aaa">
<input type="checkbox" value="2" ms-duplex-number="@aaa">
<input type="checkbox" value="3" ms-duplex-number="@aaa">
```
点击第二个checkbox时，它会转换出input.value的“2”,然后再用avalon.parsers.number转换成数字，就能成功移除，于是文本就会变化。
