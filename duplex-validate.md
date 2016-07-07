# 表单验证

avalon内置了强大的表单验证功能，它需要结合[ms-duplex](directives/ms-duplex.md), [ms-validate](directives/ms-validate.md), [ms-rules](directives/ms-rules.md)这个三个指令一起使用。

* ms-duplex负责监控每个表单元素的输入。
* ms-rules负责对表单元素的值进行各种检测，包括非空验证，长度验测，格式匹配等等。
* ms-validate负责控制验证的时机，及针对每个表单元素的验证结果触发各种回调。


验证规则定义在avalon.validators对象中, 为一个个带有message与get属性的对象.

具体用法详见[验证规则绑定](directives/ms-rules.md)

```javascript
avalon.shadowCopy(avalon.validators, {
    pattern: {
        message: '必须匹配{{pattern}}这样的格式',
        get: function (value, field, next) {
            var elem = field.element
            var data = field.data
            if (!isRegExp(data.pattern)) {
                var h5pattern = elem.getAttribute("pattern")
                data.pattern = new RegExp('^(?:' + h5pattern + ')$')
            }
            next(data.pattern.test(value))
            return value
        }
    },
    digits: {
        message: '必须整数',
        get: function (value, field, next) {//整数
            next(/^\-?\d+$/.test(value))
            return value
        }
    },
    number: {
        message: '必须数字',
        get: function (value, field, next) {//数值
            next(isFinite(value))
            return value
        }
    }
    
 })
```