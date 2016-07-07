# 过滤器

数据格式绑定属性

##uppercase

将字符串全部大写

```
vm.aaa = "aaa"

<div>{{@aaa | uppercase}}</div>
```
##lowercase

将字符串全部小写


vm.aaa = "AAA"

<div>{{@aaa | lowercase}}</div>
truncate

对长字符串进行截短，有两个可选参数

number，最后返回的字符串的长度，已经将truncation的长度包含在内，默认为30。
truncation，告知用户它已经被截短的一个结尾标识，默认为"..."

vm.aaa = "121323234324324"

<div>{{@aaa | truncate(10,'...')}}</div>
camelize

驼峰化处理， 如"aaa-bbb"变成"aaaBBB"

escape

对类似于HTML格式的字符串进行转义，如将<、 >转换为<、 >

sanitize

对用户输入的字符串进行反XSS处理，去掉onclick, javascript:alert，<script>等危险属性与标签。

number

对需要处理的数字的整数部分插入千分号（每三个数字插入一个逗号），有一个参数fractionSize，用于保留小数点的后几位。

fractionSize:小数部分的精度，默认为3。

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

输入数字:  
1234.56789

不处理：1234.56789

不传参：1,234.568

不保留小数：1,235

负数:-1,234.5679
currency

用于格式化货币，类似于number过滤器（即插入千分号），但前面加了一个货币符号，默认使用人民币符号\uFFE5

symbol, 货币符号，默认是\uFFE5
fractionSize，小数点后保留多少数，默认是2
date

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
