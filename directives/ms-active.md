#active绑定

用于类实现`:active伪类`的效果, 当用户点时元素时添加几个类名, 鼠标松开或移开时将刚才的 类名移除

用法类似于类名绑定,要求属性值为字符串,多个类名以空格隔开; 为对象时,键名为类名,键值为布尔或01,为数组时,为字符串数组

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