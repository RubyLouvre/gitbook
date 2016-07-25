#类名绑定

属性绑定用于为元素节点添加几个类名, 因此要求属性值为字符串,多个类名以空格隔开; 为对象时,键名为类名,键值为布尔或01,为数组时,为字符串数组

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

    <span :class="@aaa">直接引用字符串</span>
    <span :class="@bbb">直接引用对象</span>
    <span :class="@ccc">直接引用数组</span>
    <span :class="{aaa: @toggle, bbb: @toggle2}">使用对象字面量</span>
    <span :class="['aaa', 'bbb',(@toggle? 'ccc':'ddd')]">选择性添加类名</span>
    <span :class="[@toggle && 'aaa']">选择性添加类名</span>
    <span :class="[ @ddd + 4]">动态生成类名</span>
</body>
```