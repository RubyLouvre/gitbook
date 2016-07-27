#双工绑定

双工绑定是MVVM框架中最强大的指令.react推崇单向数据流,没有双工绑定,
那么需要rudex等额外的库来实现相同的功能.

双工绑定只要用于表单元素上.或当一个div设置了contenteditable为true,也可以用ms-duplex指令.

##各个表单元素的用法

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: 'aaa',
            bbb: 'bbb',
            ccc: 'ccc'
        })

    </script>

    <input ms-duplex="@aaa"/>{{@aaa}}
    <input ms-duplex="@bbb" type="password"/>{{@bbb}}
    <textarea ms-duplex="@ccc" /></textarea>{{@ccc}}
</body>
```
上面有三个控件,text, password, textarea它们都是属于输入型控件, 只要每为控件敲入一个字符,
后面的文本都会**立即**变化.那是因为它们默认是绑定oninput事件,如果想控件全部输入好,失去焦点时
才同步,那么可以使用`change`过滤器
```html
<input ms-duplex="@aaa | change"/>{{@aaa}} 
```

如果你是做智能提示, 控件是绑定了一个AJAX请求与后端不断交互, 使用oninput事件会太频繁,
使用onchange事件会太迟钝,那么我们可以使用`debounce`过滤器
```html
<input ms-duplex="@aaa | debounce(300)"/>{{@aaa}} 
```
300ms同步一次.

另外,可编辑元素的用法与过滤器与上面三种控件一样.
```html
<div contenteditable="true" ms-duplex="@aaa | debounce(300)"/></div>
<p>{{@aaa}}</p>
```

>这两个过滤器只能适用于上面的情况.

此外, 控件还有许多种, 像checkbox, radio,它们的同步机制也不一样.
```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: '33',
            bbb: ['22']
        })

    </script>

    <input type="radio" value="11"  ms-duplex="@aaa"/>
    <input type="radio" value="22"  ms-duplex="@aaa"/>
    <input type="radio" value="33"  ms-duplex="@aaa"/>
    <input type="checkbox" value="11"  ms-duplex="@bbb"/>
    <input type="checkbox" value="22"  ms-duplex="@bbb"/>
    <input type="checkbox" value="33"  ms-duplex="@bbb"/>
    <p>radio: {{@aaa}}; checkbox:{{@bbb}}</p>
</body>
```

checkbox与radio是一点击就会更新.radio要求在vm中为一个简单数据类型数据,字符串,数字或布尔.
而checkbox则要求是一个数组.并且在最开始时,ms-duplex会令radio钩上其value值等vm属性的控件,
checkbox则可以勾选多个.如此一来,vm中的属性些总是等于radio与checkbox的属性值.但我们也可以让
vm的属性值等于此控件的勾选状态,这时需要用上`ms-duplex-checked`转换器.

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: false,
            bbb: false
        })

    </script>
    <input type="radio"  ms-duplex-checked="@aaa"/>
    <input type="checkbox"  ms-duplex-checked="@bbb"/>
    <p>radio: {{@aaa}}; checkbox:{{@bbb}}</p>
</body>
```

最后表单元素还有select控件,它根据其multiple属性分为单选下拉框与复选下拉框,
其在vm中的值与radio,checkbox一样.即单选时,必须是一个简单数据类型, 复选时为一个数组.
在最开始时, 当option元素的value值或innerText(不在value值)与数据相同,它们就会被选上.
```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: 'bbb'
            bbb: ['bbb','ccc'],
        })

    </script>
    <select :duplex="@aaa"><option>aaa</option><option>bbb</option><option>ccc</option></select>
    <select multiple="true" :duplex="@bbb"><option>aaa</option><option>bbb</option><option>ccc</option></select>
</body>
```

| 控件       | 触发时机           | 数据  |
| ------------- |:-------------:| -----:|
| text,password,textarea及可编辑元表 | oninput,onchange, debounce| 简单数据 |
| radio,checkbox                   | onclick             |  简单数据或数组 |
| select                           | onchange            |   简单数据或数组 |

##数据转换

上面我们已经提到一个数据转换器ms-duplex-checked了.那只能用于checkbox与radio.

为什么会有这种东西呢?因为无论我们原来的数据类型是什么,跑到表单中都会变成字符串,然后我们通过事件取出来
它们也是字符串,不会主动变回`原来的类型`.我们需要一种机制保持数据原来的类型,这就是数据转换器.


avalon内置了4种过滤器

1. ms-duplex-string="@aaa" 
2. ms-duplex-number="@aaa" 
3. ms-duplex-boolean="@aaa" 
4. ms-duplex-checked="@aaa"


前三个是将元素的value值转换成string, number, boolean（只有为'false'时转换为false）
 
最后是根据当前元素（它只能是radio或checkbox）的checked属性值转换为vm对应属性的值。

它们都是放在属性名上。当数据从元素节点往vmodel同步时，转换成预期的数据。

```html
<input value="11"  ms-duplex-number="@aaa"/>
```

##数据格式化

一般来说,数据格式化是由过滤器实现的,如
```html
<input value="11"  ms-duplex="@aaa | uppercase"/>
```

但这里有一个隐患,可能导致死循环, 因此建议放在事件回调中实现.


```html
<body ms-controller="test">
    <script>
    var vm = avalon.define({
        $id: 'test',
        aaa: '111',
        bbb: '222',
        format1: function(e){//只能输入数字
           vm.aaa = e.target.value.replace(/\D/g,'')
        },
        format1: function(e){//只能输入数字
           vm.bbb = avalon.filter.date(e.target.value, 'yyyy-MM-dd')
        }
    })

    </script>

    <input :duplex="@aaa" :on-input="@format1"/>{{@aaa}}
    <input :duplex="@bbb" :on-change="@format2"/>{{@bbb}}
</body>
```


数据格式化是放在属性值时，以过滤器形式存在，如
```javascript
ms-duplex='@aaa | uppercase'
ms-duplex='@aaa | date('yyyy:MM:dd')'
```


##数据验证

这必须在所有表单元素的上方form元素加上ms-validate指令,
当前元素加上ms-rules才会生效

```html
<form ms-validate="@validation">
<input ms-duplex='@aaa' 
       ms-rules='require,email,maxlength' 
       data-maxlength='4' 
       data-maxlength-message='太长了' >
</form>
```
详见[ms-rules](./ms-rules.md)指令

##同步后的回调


ms-duplex还有一个回调，data-duplex-changed，用于与事件绑定一样， 默认第一个参数为事件对象。如果传入多个参数，那么使用$event为事件对象占位。
```html
<input value="11"  ms-duplex-number="@aaa" data-duplex-changed="@fn"/>
```

##示例

现在我们来一些实际的例子!

###全选与非全选

```javascript
var vm = avalon.define({
    $id: "duplex1",
    data: [{checked: false}, {checked: false}, {checked: false}],
    allchecked: false,
    checkAll: function (e) {
        var checked = e.target.checked
        vm.data.forEach(function (el) {
            el.checked = checked
        })
    },
    checkOne: function (e) {
        var checked = e.target.checked
        if (checked === false) {
            vm.allchecked = false
        } else {//avalon已经为数组添加了ecma262v5的一些新方法
            vm.allchecked = vm.data.every(function (el) {
                return el.checked
            })
        }
    }
})

<table ms-controller=" duplex1" border="1">
    <tr>
        <td><input type="checkbox" 
                   ms-duplex-checked="@allchecked" 
                   data-duplex-changed="@checkAll"/>全选</td>
    </tr>
    <tr ms-for="($index, el) in @data">
        <td><input type="checkbox" ms-duplex-checked="el.checked" data-duplex-changed="@checkOne" />{{$index}}::{{el.checked}}</td>
    </tr>
</table>
```

我们仔细分析其源码，allchecked是用来控制最上面的复选框的打勾情况， 数组中的checked是用来控制下面每个复选框的下勾情况。由于是使用ms-duplex，因此会监听用户行为， 当复选框的状态发生改变时，就会触发data-duplex-changed回调，将当前值传给回调。 但这里我们不需要用它的value值，只用它的checked值。

最上面的复选框对应的回调是checkAll，它是用来更新数组的每个元素的checked属性，因此一个forEach循环赋值就是。

下面的复选框对应的checkOne，它们是用来同步最上面的复选框，只要它们有一个为false上面的复选框就不能打勾， 当它们被打勾了，它们就得循环整个数组，检查是否所有元素都为true，是才给上面的checkall属性置为true。

现在我们学了循环指令，结合它来做一个表格看看。现在有了强大无比的orderBy, limitBy, filterBy, selectBy。 我们做高性能的大表格是得心应手的！

```javascript
 if (!Date.now) {//fix 旧式IE
    Date.now = function() {
        return new Date - 0;
    }
}
avalon.define({
    $id: "duplex2",
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
```

```html
<div ms-controller=" duplex2">
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
</div>
```

我们再来一个文本域与下拉框的联动例子，它只用到ms-duplex，不过两个控件都是绑定同一个属性。

```
avalon.define({
    $id: "fruit",
    options: ["苹果", "香蕉", "桃子", "雪梨", "葡萄",
        "哈蜜瓜", "橙子", "火龙果", "荔技", "黄皮"],
    selected: "桃子"
})
```

```html
<div ms-controller=" fruit">
    <h3>文本域与下拉框的联动</h3>
    <input  ms-duplex="@selected" />
    <select ms-duplex="@selected" >
        <option ms-for="el in @options" ms-attr="{value: el}" >
        {{el}}
        </option>
    </select>
</div>
```

###下拉框三级联动

```javascript
var map = {
    "中国": ["江南四大才子", "初唐四杰", "战国四君子"],
    "日本": ["日本武将", "日本城堡", "幕府时代"],
    "欧美": ["三大骑士团", "三大魔幻小说", "七大奇迹"],
    "江南四大才子": ["祝枝山", "文征明", "唐伯虎", "周文宾"],
    "初唐四杰": ["王勃", "杨炯", "卢照邻", "骆宾王"],
    "战国四君子": ["楚国春申君黄歇", "齐国孟尝君田文", "赵国平原君赵胜", "魏国信陵君魏无忌"],
    "日本武将": ["织田信长", "德川家康", "丰臣秀吉"],
    "日本城堡": ["安土城", "熊本城", "大坂城", "姬路城"],
    "幕府时代": ["镰仓", "室町", "丰臣", "江户"],
    "三大骑士团": ["圣殿骑士团", "医院骑士团", "条顿骑士团"],
    "三大魔幻小说": ["冰与火之歌", "时光之轮", "荆刺与白骨之王国"],
    "七大奇迹": ["埃及胡夫金字塔", "奥林匹亚宙斯巨像", "阿尔忒弥斯月神殿", "摩索拉斯陵墓", "亚历山大港灯塔", "巴比伦空中花园", "罗德岛太阳神巨像"]
}
var vm = avalon.define({
    $id: 'linkage',
    first: ["中国", "日本", "欧美"],
    second: map['日本'].concat(),
    third: map['日本武将'].concat(),
    firstSelected: "日本",
    secondSelected: "日本武将",
    thirdSelected: "织田信长"
})


vm.$watch("firstSelected", function (a) {
    vm.second = map[a].concat()
    vm.secondSelected = vm.second[0]
})
vm.$watch("secondSelected", function (a) {
    vm.third = map[a].concat()
    vm.thirdSelected = vm.third[0]
})
```

```html
<div ms-controller=" linkage">
<h3>下拉框三级联动</h3>
<select ms-duplex="@firstSelected" >
    <option  ms-for="el in @first" ms-attr="{value:el}" >{{el}}</option>
</select>
<select ms-duplex="@secondSelected" >
    <option  ms-for="el in @second" ms-attr="{value:el}" >{{el}}</option>
</select>
<select ms-duplex="@thirdSelected" >
    <option  ms-for="el in @third" ms-attr="{value:el}" >{{el}}</option>
</select>
</div>
```


这里的技巧在于使用$watch回调来同步下一级的数组与选中项。注意，使用concat方法来复制数组。