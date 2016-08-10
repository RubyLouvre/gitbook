#active绑定

为元素添加:active效果,当元素被点击时添加几个类名, 鼠标弹起后则立即移除

```html
    <body ms-controller="test">
        <script>
            avalon.define({
                $id: 'test',
                aaa: "aaa bbb ccc",
                bbb: 'ddd',
                ccc: ['xxx', 'yyy', 'zzz'],
                ddd: 'eee',
                toggle: true,
                toggle2: false
            })

        </script>

        <span :active="@aaa">直接引用字符串</span>
        <span :active="@ccc">直接引用数组</span>
        <span :active="[@aaa, @bbb]">使用数组字面量</span>
        <span :active="['aaa', 'bbb',(@toggle? 'ccc':'ddd')]">选择性添加类名</span>
        <span :active="[@toggle && 'aaa']">选择性添加类名</span>
        <span :active="[ @ddd + 4]">动态生成类名</span>
    </body>
```