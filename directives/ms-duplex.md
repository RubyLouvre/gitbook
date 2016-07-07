#双工绑定

在许多表单应用，我们经常遇到点击一个复选框（或下拉框）会引发旁边的复选框（或下拉框）发生改变， 这种联动效果用avalon来做是非常简单的。因为avalon拥有经典MVVM框架的一大利器，双向绑定！ 绝大部分的指令是从vm单向拍到页面，而双向绑定，则通过监听元素的value值变化，反向同步到vm中。 如果没有这种机制，则需要引入额外的机制(flux云云)来处理此事。

这个指令在1.0中已经不断增强，到2.0， 它的服务对象已经不局限于表单元素，还扩展到可编辑元素（contenteditable＝true）上了。 此外ms-duplex还可以与新加入的ms-validate指令一起使用。因此双工指令是集成数据转换，数据格式化，数据验证， 光标处理4大功能。

数据转换与之前1.5一样，使用四大转换器

1. ms-duplex-string="@aaa" 
2. ms-duplex-number="@aaa" 
3. ms-duplex-boolean="@aaa" 
4. ms-duplex-checked="@aaa"


前三个是将元素的value值转换成string, number, boolean（只有为'false'时转换为false）， 最后是根据当前元素（它只能是radio或checkbox）的checked属性值转换为vm对应属性的值。

它们都是放在属性名上。当数据从元素节点往vmodel同步时，转换成预期的数据。

数据格式化是放在属性值时，以过滤器形式存在，如
```javascript
ms-duplex='@aaa | uppercase'
ms-duplex='@aaa | date('YYYY:MM:dd')'
```

此外还存在两个控制同步时机的过滤器，change与debounce。

change过滤器相当于之前的`data-duplex-event="change"`.

debounce是对频繁输入进行节流处理。它既不像那oninput事件那样密集（由于使用了虚拟DOM，每一个字符， 都会重新短成一个全新的虚拟DOM树），也不像onchange事件那么滞后。 这在自动元素的suggest组件中非常有用。debounce可以传参，为毫秒数

```javascript
ms-duplex='@aaa | debounce(300)'
```

然后是数据验证，这必须在所有表单元素的上方，加上ms-validate才会生效。 这时每个表单元素要加上data-duplex-validator.

```html
<form ms-validate="@validation">
<input ms-duplex='@aaa' 
       data-validators='require,email,maxlength' 
       data-maxlength='4' 
       data-maxlength-message='太长了' >
</form>
```

最后是光标处理，目的是确保光标不会一下子跑到最前还是最后。

除此之后，ms-duplex还有一个回调，data-duplex-changed，用于与事件绑定一样， 默认第一个参数为事件对象。如果传入多个参数，那么使用$event为事件对象占位。

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