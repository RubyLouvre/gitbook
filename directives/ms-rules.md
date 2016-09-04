#验证规则绑定

avalon2砍掉了不少功能（如`ms-include`,`ms-data`），腾出空间加了其他更有用的功能。 数据验证就是其中之一。现在avalon2内置的验证指令是参考之前的oniui验证框架与jquery validation。

此指令只能用于添加ms-duplex指令的表单元素上。

avalon内置验证规则有

| 规则 | 描述 |
| -- | -- |
| required(true) | 必须输入的字段 |
| norequired(true) | 不是必填的字段 |
| email(true) | 必须输入正确格式的电子邮件 |
| url(true) | 必须输入正确格式的网址 |
| date(true或正则) | 必须输入正确格式的日期。默认是要求YYYY-MM-dd这样的格式 |
| number(true) | 必须输入合法的数字（负数，小数） |
| digits(true) | 必须输入整数 |
| pattern(正则或true） | 让输入数据匹配给定的正则，如果没有指定，那么会到元素上找pattern属性转换成正则再匹配 |
| equalto(ID名） | 输入值必须和 #id 元素的value 相同 |
| maxlength：5 | 输入长度最多是 5 的字符串（汉字算一个字符） |
| minlength：10 | 输入长度最小是 10 的字符串（汉字算一个字符） |
| chs（true） | 要求输入全部是中文 |
| max:5 | 输入值不能大于 5 |
| min:10 | 输入值不能小于 10 |

这些验证规则要求使用ms-rules指令表示，要求为一个普通的JS对象。

此外要求验征框架能动起来，还必须在所有表单元素外包一个form元素，在form元素上加ms-validate指令。

```javascript
 var vm = avalon.define({
        $id: "validate1",
        aaa: "",
        bbb: '',
        ccc: '',
        validate: {
            onError: function (reasons) {
                reasons.forEach(function (reason) {
                    console.log(reason.getMessage())
                })
            },
            onValidateAll: function (reasons) {
                if (reasons.length) {
                    console.log('有表单没有通过')
                } else {
                    console.log('全部通过')
                }
            }
        }
    })
```

```html
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
```

因此，要运行起avalon2的内置验证框架，必须同时使用三个指令。ms-validate用于定义各种回调与全局的配置项（如什么时候进行验证）。[ms-duplex](ms-duplex.md)用于将单个表单元素及相关信息组成一个Field对象，放到ms-validater指令的fields数组中。ms-rules用于定义验证规则。如果验证规则不满足你，你可以自行在**avalon.validators**对象上添加。

现在我们可以一下ms-validate的用法。其对应一个对象。

| 配置项 | 描述 |
| -- | -- |
| fields | 框架自行添加，用户不用写。为一个数组，放置ms-duplex生成的Field对象。 |
| onSuccess| 空函数，单个验证成功时触发，this指向被验证元素this指向被验证元素，传参为一个对象数组外加一个可能存在的事件对象。 |
| onError | 空函数，单个验证失败时触发，this与传参情况同上 |
| onComplete |空函数，单个验证无论成功与否都触发，this与传参情况同上。 |
| onValidateAll | 空函数，整体验证后或调用了validateAll方法后触发；有了这东西你就不需要在form元素上ms-on-submit="submitForm"，直接将提交逻辑写在onValidateAll回调上|
| onReset | 空函数，表单元素获取焦点时触发，this指向被验证元素，大家可以在这里清理className、value |
| validateInBlur | true，在blur事件中进行验证,触发onSuccess, onError, onComplete回调 |
| validateInKeyup | true, 在keyup事件中进行验证,触发onSuccess, onError, onComplete回调。当用户在ms-duplex中使用change debounce过滤器时会失效 |
| validateAllInSubmit | true，在submit事件中执行onValidateAll回调 |
| resetInFocus | true，在focus事件中执行onReset回调 |
| deduplicateInValidateAll |false，在validateAll回调中对reason数组根据元素节点进行去重 |
	
我们看一下如何{% em color="#FF7F24" %}自定义验证规则{% endem %}.

比如说我们有一个变态的需求,一个字段可以不填，但如果要填的话一定要是合法的数字,并且大于零.
这就需要自定义规则了.

```
<!DOCTYPE html>
<html>
    <head>
        <title>ms-validate</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge" /> 
        <script src="../dist/avalon.js"></script>
        <script>
            avalon.validators.aaa = {
                message: '必须数字并大于0',
                get: function (value, field, next) {
                   //想知道它们三个参数是什么,可以console.log(value, field,next)
                    var ok = (value === '' || (Number(value) > 0))
                    next(ok)
                    return value
                }
            }
            var vm = avalon.define({
                $id: "test",
                aaa: '',
                validate: {
                    onError: function (reasons) {
                        reasons.forEach(function (reason) {
                            console.log(reason.getMessage())
                        })
                    },
                    onValidateAll: function (reasons) {
                        if (reasons.length) {
                            console.log('有表单没有通过')
                        } else {
                            console.log('全部通过')
                        }
                    }
                }
            })
        </script>
    </head>

    <body ms-controller="test">
        <form class="cmxform" ms-validate="@validate" >
            <fieldset>
                <legend>自定义规则</legend>
                <p>
                    <input 
                        ms-duplex="@aaa"
                        ms-rules="{aaa: true}" 
                        >
                </p>
            </fieldset>
            <p>
                <input class="submit" type="submit" value="提交">
            </p>
        </fieldset>
    </form>
</body>
</html>

```


	
在上表还有一个没有提到的东西是如何显示错误信息，这个avalon不帮你处理。但提示信息会帮你拼好，如果你没有写，直接用验证规则的message，否则在元素上找data-message或data-required-message这样的属性。
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
                $id: "test",
                rules:{required:true,email:true},
                email:'',
                validate: {
                    onError: function (reasons) {
                        reasons.forEach(function (reason) {
                            console.log(reason.getMessage())
                        })
                    },
                    onValidateAll: function (reasons) {
                        if (reasons.length) {
                            console.log('有表单没有通过')
                        } else {
                            console.log('全部通过')
                        }
                    }
                }
            })
        </script>
    </head>

    <body ms-controller="test">
        <form class="cmxform" ms-validate="@validate" >
            <fieldset>
                <legend>验证完整的表单</legend>
                <p>
                    <label for="email">Email</label>
                    <input id="email" 
                           name="email" 
                           type="email"
                           ms-duplex="@email"
                           ms-rules="@rules" 
                           data-required-message="请输入"
                           data-email-message="请输入一个正确的邮箱"
                           >
                </p>
                </fieldset>
                <p>
                    <input class="submit" type="submit" value="提交">
                </p>
            </fieldset>
        </form>
    </body>
</html>

```



最后给一个复杂的例子：
```html  
<script>
    var vm = avalon.define({
        $id: "validate2",
        firstname: '司徒正美',
        lastname: '',
        username: '',
        password: '',
        confirm_password: '',
        email: '',
        agree: false,
        topic: [],
        toggle: false,
        validate: {
            onError: function (reasons) {
                reasons.forEach(function (reason) {
                    console.log(reason.getMessage())
                })
            },
            onValidateAll: function (reasons) {
                if (reasons.length) {
                    console.log('有表单没有通过')
                } else {
                    console.log('全部通过')
                }
            }
        }
    })
    avalon.validators.checked = {
        message: '必须扣上',
        get: function (value, field, next) {
            next(value)
            return value
        }
    }
    avalon.validators.selecttwo = {
        message: '至少选择两个',
        get: function (value, field, next) {
            next(!vm.toggle || value.length >= 2)
            return value
        }
    }
   </script>
  
    <div ms-controller="validate2">
        <form class="cmxform" ms-validate="@validate" >
            <fieldset>
                <legend>验证完整的表单</legend>
                <p>
                    <label for="firstname">名字</label>
                    <input id="firstname" 
                           name="firstname" 
                           ms-duplex="@firstname"
                           ms-rules="{required:true, pattern: /[\u4e00-\u9fa5a-z]{2-8}/i }" 
                           data-required-message="必须是中文或字母(3-8个字符)" >
                </p>
                <p>
                    <label for="lastname">姓氏</label>
                    <input id="lastname" 
                           name="lastname"
                           ms-duplex="@lastname"
                           ms-rules="{required:true}" 
                           data-required-message="请输入您的姓氏"
                           >
                </p>
                <p>
                    <label for="username">用户名</label>
                    <input id="username" 
                           name="username"
                           ms-duplex="@username | change"
                           ms-rules="{required:true, minlength:2}" 
                           >
                </p>
                <p>
                    <label for="password">密码</label>
                    <input id="password" 
                           name="password" 
                           type="password"
                           ms-duplex="@password"
                           ms-rules="{required:true,minlength:5}" 
                           data-required-message="请输入密码"
                           data-required-message="密码长度不能小于 5 个字母"

                           >
                </p>
                <p>
                    <label for="confirm_password">验证密码</label>
                    <input id="confirm_password" 
                           name="confirm_password" 
                           type="password"
                           ms-duplex="@confirm_password | change"
                           ms-rules="{required:true,equalto:'password'}" 
                           data-equalto-message="两次密码输入不一致"
                           >
                </p>
                <p>
                    <label for="email">Email</label>
                    <input id="email" 
                           name="email" 
                           type="email"
                           ms-duplex="@email"
                           ms-rules="{email:true}" 
                           data-email-message="请输入一个正确的邮箱"
                           >
                </p>
                <p>
                    <label for="agree">请同意我们的声明</label>
                    <input type="checkbox" class="checkbox" id="agree" name="agree"
                           ms-duplex-checked="@agree"
                           ms-rules="{checked:true}" 
                           >
                </p>
                <p>
                    <label for="newsletter">我乐意接收新信息</label>
                    <input type="checkbox" class="checkbox" 
                           id="newsletter" 
                           name="newsletter"
                           ms-duplex-checked="@toggle"
                           >
                </p>
                <fieldset id="newsletter_topics" ms-visible="@toggle" >
                    <legend>主题 (至少选择两个) </legend>
                    <label for="topic_marketflash">
                        <input type="checkbox" 
                               id="topic_marketflash" 
                               value="marketflash" 
                               name="topic[]" 
                               ms-duplex="@topic"
                               ms-rules="{selecttwo:true}"
                               >Marketflash
                    </label>
                    <label for="topic_fuzz">
                        <input type="checkbox"
                               id="topic_fuzz"
                               value="fuzz"
                               name="topic[]"
                               ms-duplex="@topic"
                               ms-rules="{selecttwo:true}"
                               >Latest fuzz
                    </label>
                    <label for="topic_digester">
                        <input type="checkbox" 
                               id="topic_digester"
                               value="digester"
                               name="topic[]"
                               ms-duplex="@topic"
                               ms-rules="{selecttwo:true}"
                               >Mailing list digester
                    </label>
                    <label for="topic" class="error" style="display:none">至少选择两个</label>
                </fieldset>
                <p>
                    <input class="submit" type="submit" value="提交">
                </p>
            </fieldset>
        </form>
    </div>
```