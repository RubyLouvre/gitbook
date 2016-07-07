#HTML绑定

HTML绑定类似于文本绑定,能将一个元素清空,填上你需要的内容

它要求VM对应的属性的类型为字符串

```html
<span ms-html="@aaa">不使用过滤器</span>
<span ms-html="@aaa | uppercase">使用过滤器</span>
```