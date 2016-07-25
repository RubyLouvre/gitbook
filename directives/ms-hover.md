#hover绑定

用于类实现:hover伪类的效果, 当用户鼠标移动元素上方时添加几个类名, 鼠标移走时将刚才的 类名移除

用法类似于[类名绑定](ms-class.md),要求属性值为字符串,多个类名以空格隔开; 为对象时,键名为类名,键值为布尔或01,为数组时,为字符串数组

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: "aaa bbb ccc",
            bbb: {
                aa: 1,
                bb: 2,
                cc: 3
            },
            ccc: ['xxx', 'yyy', 'zzz'],
            ddd: 'eee',
            toggle: true,
            toggle2: false
        })

    </script>

    <span :hover="@aaa">直接引用字符串</span>
    <span :hover="@bbb">直接引用对象</span>
    <span :hover="@ccc">直接引用数组</span>
    <span :hover="{aaa: @toggle, bbb: @toggle2}">使用对象字面量</span>
    <span :hover="['aaa', 'bbb',(@toggle? 'ccc':'ddd')]">选择性添加类名</span>
    <span :hover="[@toggle && 'aaa']">选择性添加类名</span>
    <span :hover="[ @ddd + 4]">动态生成类名</span>
</body>
```