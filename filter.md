<!-- toc -->
#过滤器

##格式化过滤器
用于处理数字或字符串，多用`于{{}}`或ms-attr或ms-class

###uppercase

将字符串全部大写

```javascript
vm.aaa = "aaa"

<div>{{@aaa | uppercase}}</div>
```
###lowercase

将字符串全部小写

```javascript
vm.aaa = "AAA"

<div>{{@aaa | lowercase}}</div>
```
###truncate

对长字符串进行截短，有两个可选参数

number，最后返回的字符串的长度，已经将truncation的长度包含在内，默认为30。
truncation，告知用户它已经被截短的一个结尾标识，默认为"..."

```javascript
vm.aaa = "121323234324324"

<div>{{@aaa | truncate(10,'...')}}</div>
```

###camelize

驼峰化处理， 如"aaa-bbb"变成"aaaBBB"

###escape

对类似于HTML格式的字符串进行转义，如将<、 >转换为<、 >

###sanitize

对用户输入的字符串进行反XSS处理，去掉onclick, `javascript:alert`，`<script>`等危险属性与标签。

###number

对需要处理的数字的整数部分插入千分号（每三个数字插入一个逗号），有一个参数fractionSize，用于保留小数点的后几位。

fractionSize:小数部分的精度，默认为3。
```html
<!DOCTYPE html>
<html>
<head>
    <title>TODO supply a title</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width">
    <script src="avalon.js"></script>
    <script>
        avalon.define({
            $id: "number",
            aaa: 1234.56789
        })
    </script>
</head>
 
<body>
    <div ms-controller="number">
        <p>输入数字:
            <input ms-duplex="@aaa">
        </p>
        <p>不处理：{{@aaa}}</p>
        <p>不传参：{{@aaa|number}}</p>
        <p>不保留小数：{{@aaa|number(0)}}</p>
        <p>负数:{{-@aaa|number(4)}}</p>
    </div>
</body>
 
</html>
```

###currency

用于格式化货币，类似于number过滤器（即插入千分号），但前面加了一个货币符号，默认使用人民币符号`\uFFE5`

symbol, 货币符号，默认是`\uFFE5`
fractionSize，小数点后保留多少数，默认是2

###date

对日期进行格式化，date(formats), 目标可能是符合一定格式的字符串，数值，或Date对象。

```html
<!DOCTYPE html>
<html>
<head>
    <title>TODO supply a title</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width">
    <script src="avalon.js"></script>
    <script>
        avalon.define({
            $id: 'testtest',
            name: "大跃进右",
            d1: new Date,
            d2: "2011/07/08",
            d3: "2011-07-08",
            d4: "01-01-2000",
            d5: "03 04,2000",
            d6: "3 4,2000",
            d7: 1373021259229,
            d8: "1373021259229",
            d9: "2014-12-07T22:50:58+08:00",
            d10: "\/Date(1373021259229)\/"
 
        })
    </script>
</head>
<body>
    <div ms-controller="testtest">
        <p>生成于{{ @d1 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d2 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d3 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d4 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d5 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d6 | date("yyyy MM dd")}}</p>
        <p>生成于{{ @d7 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d8 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d9 | date("yyyy MM dd:HH:mm:ss")}} //这是ISO8601的日期格式</p>
        <p>生成于{{ @d10| date("yyyy MM dd:HH:mm:ss")}} //这是ASP.NET输出的JSON数据的日期格式</p>
    </div>
</body>
 
</html>
```

| 标记 | 说明 |
| --  | -- |
| yyyy | 将当前的年份以4位数输出，如果那一年为300，则补足为0300 |
| yy | 将当前的年份截取最后两位数输出，如2014变成14， 1999变成99， 2001变成01 |
| y | 将当前的年份原样输出，如2014变成2014， 399变成399， 1变成1 |
| MMMM | 在中文中，MMMM与MMM是没有区别，都是"1月"，"2月"……英语则为该月份的单词全拼 |
| MMM | 在中文中，MMMM与MMM是没有区别，都是"1月"，"2月"……英语则为该月份的单词缩写(前三个字母) |
| MM | 将月份以01-12的形式输出(即不到两位数，前面补0) |
| M | 将月份以1-12的形式输出 |
| dd | 以日期以01-31的形式输出(即不到两位数，前面补0) |
| d | 以日期以1-31的形式输出 |
| EEEE | 将当前天的星期几以“星期一”，“星期二”，“星期日”的形式输出，英语则Sunday-Saturday |
| EEE | 将当前天的星期几以“周一”，“周二”，“周日”的形式输出，英语则Sun-Sat |
| HH | 将当前小时数以00-23的形式输出 |
| H | 将当前小时数以0-23的形式输出 |
| hh | 将当前小时数以01-12的形式输出 |
| h | 将当前小时数以0-12的形式输出 |
| mm | 将当前分钟数以00-59的形式输出 |
| m | 将当前分钟数以0-59的形式输出 |
| ss | 将当前秒数以00-59的形式输出 |
| s | 将当前秒数以0-59的形式输出 |
| a | 将当前时间是以“上午”，“下午”的形式输出 |
| Z | 将当前时间的时区以-1200-+1200的形式输出 |
| fullDate | 相当于y年M月d日EEEE 2014年12月31日星期三 |
| longDate | 相当于y年M月d日EEEE 2014年12月31日 |
| medium | 相当于yyyy-M-d H:mm:ss 2014-12-31 19:02:44 |
| mediumDate | 相当于yyyy-M-d 2014-12-31 |
| mediumTime | 相当于H:mm:ss 19:02:44 |
| short | 相当于yy-M-d ah:mm 14-12-31 下午7:02 |
| shortDate | 相当于yy-M-d 14-12-31 |
| shortTime | 相当于ah:mm 下午7:02 |

---------------------------
##循环过滤器

用于ms-for指令中

###limitBy

只能用于ms-for循环,对数组与对象都有效, 限制输出到页面的个数, 有两个参数

1.  limit: 最大个数,必须是数字或字符, 当个数超出数组长或键值对总数时, 等于后面
2.  begin: 开始循环的个数, 可选,默认0

```html
<script>
    avalon.define({
        $id: "limitBy",
        array: [1, 2, 3, 4, 5, 6],
        object: {a: 1, b: 2, c: 3, d: 4, e: 5},
        num: 3
    })
</script>
<div ms-controller='limitBy'>
    <select ms-duplex-number='@num'>
        <option>2</option>
        <option>3</option>
        <option>4</option>
        <option>5</option>
    </select>
    <ul>
        <li ms-for='el in @array | limitBy(@num)'>{{el}}</li>
    </ul>
    <ul>
        <li ms-for='el in @object | limitBy(@num)'>{{el}}</li>
    </ul>
</div>
``` 

###orderBy

只能用于ms-for循环,对数组与对象都有效, 用于排序, 有两个参数

1.  key: 要排序的属性名
2.  dir: -1或1, 顺序或倒序,可选,默认1

```html
<script>
avalon.define({
    $id: "orderBy",
    array: [{a: 1, b: 33},{a: 2, b: 22}, {a: 3, b: 11}],
    order: 'a',
    dir: -1
})
</script>
<div ms-controller='orderBy'>
    <select ms-duplex='@order'>
        <option>a</option>
        <option>b</option>
    </select>
    <select ms-duplex-number='@dir'>
        <option>1</option>
        <option>-1</option>
    </select>
    <table border='1' width='200'>
        <tr ms-for="el in @array | orderBy(@order, @dir)">
            <td ms-for='elem in el'>{{elem}}</td>
        </tr>
    </table>
</div>
```

###filterBy

只能用于ms-for循环,对数组与对象都有效, 用于获取它们的某一子集, 有至少一个参数

search，如果为函数时, 通过返回true决定成为子集的一部分; 如果是字符串或数字, 将转换成正则, 如果数组元素或对象键值匹配它,则成为子集的一部分,但如果是空字符串则返回原对象 ;其他情况也返回原对象。
其他参数, 只有当search为函数时有效, 这时其参数依次是组元素或对象键值, 索引值, 多余的参数
此过滤多用于自动完成的模糊匹配!

```html
<script>
    avalon.define({
        $id: "filterBy",
        array: ['aaaa', 'aab', 'acb', 'ccc', 'dddd'],
        object: {a: 'aaaa', b: 'aab', c: 'acb', d: 'ccc', e: 'dddd'},
        search: "a",
        searchFn: function (el, i) {
            return i > 2
        },
        searchFn2: function (el, i) {
            return el.length === 4
        },
        searchFn3: function (el, i) {
            return this.key === 'b' || this.key === 1
        }
    })
</script>
<div ms-controller='filterBy'>
    <select ms-duplex='@search'>
        <option>a</option>
        <option>b</option>
        <option>c</option>
    </select>
    <p><button ms-click="@search = @searchFn | prevent">变成过滤函数</button></p>
    <p><button ms-click="@search = @searchFn2 | prevent">变成过滤函数2</button></p>
    <p><button ms-click="@search = @searchFn3 | prevent">变成过滤函数3</button></p>
    <ul>
        <li ms-for='el in @array | filterBy(@search)'>{{el}}</li>
    </ul>
    <ul>
        <li ms-for='el in @object | filterBy(@search)'>{{el}}</li>
    </ul>
</div>
 
````


###selectBy

只能用于ms-for循环,只对对象有效, 用于抽取目标对象的几个值,构成新数组返回.

1.  array，要抽取的属性名
2.  defaults，如果目标对象不存在这个属性,那么从这个默认对象中得到默认值,否则为空字符串, 可选
这个多用于表格, 每一列的对象可能存在属性顺序不一致或缺少的情况

```html
<script>
    avalon.define({
        $id: "selectBy",
        obj: {a: 'aaa', b: 'bbb', c: 'ccc', d: 'ddd', e: 'eee'},
        grid: [{a: 1, b: 2, c: 3}, {c: 11, b: 22, a: 33}, {b: 23, a: 44}],
        defaults: {
            a:'@@@',
            b:'$$$',
            c:'###'
        }
    })
</script>
<div ms-controller='selectBy'>
   <ul>
       <li ms-for='el in @obj | selectBy(["c","a","b"])'>{{el}}</li>
   </ul>
   <table border='1' width='200'>
       <tr ms-for="tr in @grid">
           <td ms-for="td in tr | selectBy(['a','b','c'],@defaults)">{{td}}</td>
       </tr>
   </table>
</div>
```


##事件过滤器

事件过滤器只要是对一些常用操作进行简化处理

对按键事件(keyup,keydown,keypress)到底按下了哪些功能键 或方向键进行友好的处理.许多人都记不清回车退格的keyCode是多少.
对阻止默认行为与防止冒泡进行封装
###esc

当用户按下esc键时,执行你的回调

###tab

当用户按下tab键时,执行你的回调

###enter

当用户按下enter键时,执行你的回调

###space

当用户按下space键时,执行你的回调

###del

当用户按下del键时,执行你的回调

###up

当用户按下up键时,执行你的回调

###down

当用户按下down键时,执行你的回调

###left

当用户按下left键时,执行你的回调

###right

当用户按下right键时,执行你的回调

###prevent

阻止默为行为,多用于form的submit事件防止页面跳转,相当于调用了event.preventDefault

```html
<a href='./api.html' ms-click='@fn | prevent'>阻止跳转</a>
```           
###stop

阻止事件冒泡,相当于调用了event.stopPropagation

> 页面的过滤器只能用于[事件绑定](directives/ms-on.md)

##同步频率过滤器

这两个过滤器只用于[ms-duplex](directives/ms-duplex.md)

###change

在文本域或文本区使用ms-duplex时,默认是每输入一个字符就同步一次. 当我们想在失去焦点时才进行同步, 那么可以使用此过滤器
```html
<input ms-duplex='@aaa | change'>{{@aaa}}
```          
###debounce

当我们实现搜索框的自动完成时, 每输入一个字符可能就会向后台请求一次(请求关键字列表), 这样太频繁,后端撑不住,但使用change过滤器,则又太慢了.改为每隔几十毫秒请求一次就最好. 基于此常用需要开发出此过滤器. 拥有一个参数.

1.  debounceTime: 数字, 不写默认是300,不能少于4,否则做无效处理
```html
<input ms-duplex='@aaa | debounce(200)'>{{@aaa}}
```
