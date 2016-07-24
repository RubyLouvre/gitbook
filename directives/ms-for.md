#循环绑定

avalon2.0的ms-for绑定集齐了ms-repeat, ms-each, ms-with的所有功能, 并且更好用, 性能提升七八倍

ms-for可以同时循环对象与数组

```html
<ul>
    <li ms-for="el in @aaa">{{el}}</li>
</ul>
```
            
现在采用类似angular的语法, in前面如果只有一个变量,那么它就是数组元素或对象的属性名

```html
<ul>
    <li ms-for="(aaa, bbb) in @aaa">{{el}}</li>
</ul>
```

in 前面有两个变量, 它们需要放在小括号里,以逗号隔开, 那么分别代表数组有索引值与元素, 或对象的键名与键值, 这个与jQuery或avalon的each方法的回调参数一致。


如果你想截取数组的一部分出来单独循环,可以用limitBy过滤器, 使用as来引用新数组

```html
<ul>
    <li ms-for="el in @aaa | limitBy(10) as items">{{el}}</li>
</ul>
```
上例是显示数组的前10个元素, 并且将这10个元素存放在items数组中, 以保存过滤或排序结果

使用注释节点实现循环,解决同时循环多个元素的问题

```html
<!DOCTYPE html>
<html>
    <head>
        <title>TODO supply a title</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="../dist/avalon.js"></script>
        <script>
            vm = avalon.define({
                $id: 'for4',
                arr: [1, 2, 3, 4]
            })
        </script>
    </head>
    <body>
        <div ms-controller='for4'  >
            <!--ms-for: el in @arr-->
            <p>{{el}}</p>
            <p>{{el}}</p>
            <!--ms-for-end:-->
        </div>
    </body>
</html>
```

avalon 不需要像angular那样要求用户指定trace by或像react 那样使用key属性来提高性能,内部帮你搞定一切

ms-for还可以配套data-for-rendered回调,当列表渲染好时执行此方法

如果你只想循环输出数组的其中一部分,请使用filterBy,只想循环输出对象某一些键值并设置默认值,则用selectBy. 不要在同一个元素上使用ms-for与ms-if,因为这样做会在页面上生成大量的注释节点,影响页面性能

可用于ms-for中的过滤器有limitBy, filterBy, selectby, orderBy

>ms-for支持下面的元素节点继续使用ms-for,形成双重循环与多级循环, 但要求双重循环对应的二维数组.几维循环对应几维数组

```javascript
vm.array = [{arr: [111,222, 333]},{arr: [111,222, 333]},{arr: [111,222, 333]}]
<p>array的元素里面有子数组,形成2维数组</p>        
<ul>
    <li ms-for="el in @array"><div ms-for='elem in el.arr'>{{elem}}</div></li>
</ul>
```

**如何双向绑定ms-for中生成的变量?**

由于 循环生成的变量前面不带@, 因此就找不到其对应的属性,需要特别处理一下

```
<div ms-controller="test">
<div ms-for="(key,el) in @styles">
        <label>{{ key }}::{{ el }}</label>
        <input type="text" ms-duplex="@styles[key]" >
        <!--不能ms-duplex="el"-->
    </div>
</div>

<script type="text/javascript">
    var root = avalon.define({
        $id: "test",
        styles: {
            width: 200,
            height: 200,
            borderWidth: 1,
            borderColor: "red",
            borderStyle: "solid",
            backgroundColor: "gray"
        }
    })
</script>
```

**过滤器**

**limitBy(limit[,begin])**

limit<br/>
Type:Number<br/>
指定截取数组的数量，由数组首位开始截取至指定limit值结束，如limit值大于数组数量则返回整个数组

begin(可选值，默认值为0)<br/>
Type:Number<br/>
指定截取开始位置，若为负值则由末尾向前取值。**注意，begin与limit的关系不是start与length的关系，而是start与end的关系。相当于slice(begin,limit)的截取**

如果需要在循环中访问截取数组的属性时，可通过el值直接访问，或通过as生成新引用数组。
```html
<!DOCTYPE html>
<html>
    <head>
        <title>Test</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="../dist/avalon.js"></script>
        <script>
            vm = avalon.define({
                $id: 'test',
                arr:[{name:'a',age:11},{name:'b',age:3},{name:'c',age:22},{name:'d',age:33}]
            })
        </script>
    </head>
    <body>
        <div ms-controller='test'  >
            <div ms-for="(index,el) in @arr | limitBy(4,2) as items">
                <label>{{items[index].name}}::{{el.age}}</label>
            </div>
        </div>
    </body>
</html>
```

<br/>
**filterBy(search)**

search<br/>
Type:Function,Number,String<br/>
当search值为数字或字符串时，返回循环中值包含search的选项。如search为函数，则通过传递的函数过滤。

需要注意，filterBy生成新引用数组的方式与limitBy不同
```
<div ms-for="(index,el) in @arrayOrObject as newArray｜ filterBy('name')">{{el}}::{{newArray.length}}</div>
```

<br/>
**selectby(array[,defaults])**

array<br/>
Type:Array<br/>
array为属性名集合，将循环中包含array属性名的属性筛选出来。

defaults(可选值)<br/>
Type:Object<br/>
当循环值中无array中的属性名，则可通过defaults为属性设置初始值。默认初始值为''
```html
<!DOCTYPE html>
<html>
    <head>
        <title>Test</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="../dist/avalon.js"></script>
        <script>
            vm = avalon.define({
                $id: 'test',
                arr:[{name:'a',age:11},{name:'b',age:3},{name:'c',age:22},{name:'d',age:33}]
            })
        </script>
    </head>
    <body>
        <div ms-controller='test'  >
            <table border="1">
                <tr ms-for="(index,el) in @arr">
                    <td ms-for="value in el | selectBy(['name','age','grade'],{grade:'male'})">{{value}}</td>
                </tr>
            </table>
        </div>
    </body>
</html>
```

<br/>
**orderBy(criteria[,reverse])**

criteria<br/>
Type:String,Function<br/>
当criteria为字符串时，将根据指定的属性名排序。当criteria为方法时，则按照传入的方法排序。

reverse(可选值，默认值1)<br/>
Type:Number<br/>
reverse为1时，正序排列。反之为-1时，倒序排列。

```html
<!DOCTYPE HTML>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge" /> 
        <script src="./dist/avalon.js" ></script>
        <script>
            if (!Date.now) {//fix 旧式IE
                Date.now = function() {
                    return new Date - 0;
                }
            }
            var model = avalon.define({
                $id: "test",
                selected: "name",
                options: ["name", "size", "date"],
                trend: 1,
                data: [
                    {name: "aaa", size: 213, date: Date.now() + 20},
                    {name: "bbb", size: 4576, date:Date.now() - 4},
                    {name: "ccc", size: 563, date: Date.now() - 7},
                    {name: "eee", size: 3713, date: Date.now() + 9},
                    {name: "555", size: 389, date: Date.now() - 20}
                ]
            })

        </script>
    </head>
    <body ms-controller="test">
        <div style="color:red">
            <p>本例子用于显示如何做一个简单的表格排序</p>
        </div>
        <p>
            <select ms-duplex="@selected">
                <option  ms-for="el in @options">{{el}}</option>
            </select>
            <select ms-duplex-number="@trend">
                <option value="1">up</option>
                <option value="-1">down</option>
            </select>
        </p>
        <table width="500px" border="1">
            <tbody >
                <tr ms-for="el in @data | orderBy(@selected, @trend)">
                    <td>{{el.name}}</td> <td>{{el.size}}</td> <td>{{el.date}}</td>
                </tr>
            </tbody>
        </table>
    </body>
</html>
```
