#可见性绑定

这是通过修改元素的style.display值改变元素的可见性, 要求属性值对应一个布尔，如果不是布尔， avalon会自动转换值为布尔。

```html
<span ms-visible="@toggle" ms-click="@toggle = !@toggle">点我</span>
```