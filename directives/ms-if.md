#if绑定

通过属性值决定是否渲染目标元素, 为否时原位置上变成一个注释节点

> `avalon1.*`中ms-if-loop指令已经被废掉,请使用limitBy, selectBy, filterBy过滤器代替相应功能

```html
<body :controller="test">
<script>
var vm = avalon.define({
  $id: "test",
  aaa: "这是被隐藏的内容"
  toggle: false
})
</script>
<p><button type="button" :click='@toggle = !@toggle'>点我</span></p>
<div :if="@toggle">{{@aaa}}</div>
</body>
```
