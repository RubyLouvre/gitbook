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
            var elem = field.dom
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
    }
 })
```

**手动调用验证**并根据点击不同按钮**提交不同网址**的例子


```html
<!DOCTYPE html>
<html>
    <head>
        <title>ms-validate</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge" /> 
        <script src="../dist/avalon.js"></script>
        <script>
            avalon.validators.gtOne = {
                message: '必须数字并大于1',
                get: function (value, field, next) {
                    //想知道它们三个参数是什么,可以console.log(value, field,next)
                    var ok = Number(value) > 1
                    next(ok)
                    return value
                }
            }
            var greasons = []
            var vm = avalon.define({
                $id: "test",
                aaa: '',
                url: 'javascript:void(0)',
                message: '',
                submit:  function (url) {//submit是真正的验证方法,通过点击时手动验证
                    vm.validate.onManual()
                    setTimeout(function () {
                        if (greasons.length) {
                            var a = greasons.map(function (el) {
                                return '<p>' + el.getMessage() + '</p>'
                            })
                            vm.message = a.join('')//打印所有错误
                            vm.url = 'javascript:void(0)'
                        } else {
                            greasons = []
                            vm.message = ''
                            vm.url = url
                        }
                    })

                },
                validate: {
                    //禁止提交时自动验证
                    validateAllInSubmit: false,
                    //这个是用来占位的
                    onManual: avalon.noop,
                    //这个转移到sumbit方法
                    onValidateAll: function (reasons) {
                        greasons = reasons.concat()
                    }
                }
            })
        </script>
    </head>

    <body ms-controller="test">
        <form class="cmxform" ms-validate="@validate" ms-attr='{action: @url}' >
            <fieldset>
                <legend>自定义规则</legend>
                <p>
                    <input 
                        ms-duplex="@aaa"
                        ms-rules="{required: true, number:true, gtOne: true}" 
                        />
                </p>
                <p>
                    <input :click="@submit('/add.php')" type="submit" value="add"/>
                    <input :click="@submit('/update.php')" type="submit" value="update"/>
                </p>
                <p ms-html="@message" style="color: red"></p>
            </fieldset>
        </form>
    </body>
</html>

```