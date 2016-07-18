#active绑定

为元素添加:active效果,当元素被点击时添加几个类名, 鼠标弹起后则立即移除

```html
vm.aaa = "aaa bbb ccc"
vm.bbb = {
   aa: 1,
   bb: 2,
   cc: 3
}
vm.ccc = ['xxx', 'yyy', 'zzz']
<span ms-active="@aaa">直接引用字符串</span>
<span ms-active="@bbb">直接引用对象</span>
<span ms-active="@ccc">直接引用数组</span>
<span ms-active="{aaa: @toggle, bbb: @toggle2}">使用对象字面量</span>
<span ms-active="['aaa', 'bbb',(@toggle? 'ccc':'ddd")]">使用数组字面量,里面可以用三元运算符</span>
```