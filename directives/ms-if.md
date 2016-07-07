#if绑定

通过属性值决定是否渲染目标元素, 为否时原位置上变成一个注释节点

> `avalon1.*`中ms-if-loop指令已经被废掉,请使用limitBy, selectBy, filterBy过滤器代替相应功能

```html
<span ms-if="@toggle" ms-click='@toggle = !@toggle'>点我</span>
```