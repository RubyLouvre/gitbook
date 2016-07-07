#样式绑定

CSS绑定用于为元素节点添加一组样式, 因此要求属性值为对象或数组形式. 数组最后也会合并成一个对象.然后取此对象的键名为样式名, 键值为样式值为元素添加样式

> 如果键名为表示长宽,字体大小这样的样式, 那么键值不需要加单位,会自动加上px

> 如果键名如果为float,请务必在两边加上引号

> 如果键名如果为font-size,请务必转换为驼峰风格或两边加上引号


```html
vm.obj = {width: 200, height: 300}
<span ms-css="@obj">直接引用对象</span>
<span ms-css="{width: @width, height: @height}">使用对象字面量</span>
<span ms-css="@array">直接引用数组</span>
<span ms-css="[@obj1, @obj2, (@toggle ? @obj3: @obj4)]">使用数组字面量,里面可以用三元运算符</span>
```