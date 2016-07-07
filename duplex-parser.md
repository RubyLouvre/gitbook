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
<input ms-duplex-number='@aaa | debounce(200)'>{{@aaa}}
```         
我们也可以自定义类型转换器, 直接在avalon.parser上添加
```javascript
parsers: {
    number: function (a) {
        return a === '' ? '' : parseFloat(a) || 0
    },
    string: function (a) {
        return a === null || a === void 0 ? '' : a + ''
    },
    boolean: function (a) {
        return a === 'true'
    }
},
```         
上面number, string, boolean就是ms-duplex-xxx的实际转换方法, a为元素的value值. ms-duplex-checked比较特殊它是使用checked属性,因此不在其列.
