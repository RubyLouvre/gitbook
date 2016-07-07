#属性绑定

属性绑定用于为元素节点添加一组属性, 因此要求属性值为对象或数组形式. 数组最后也会合并成一个对象.然后取此对象的键名为属性名, 键值为属性值为元素添加属性

如果键名如果为for, char这样的关键字,请务必在两边加上引号

如果键名如果带横杠,请务必转换为驼峰风格或两边加上引号

```html
  vm.obj = {title: 'title', algin: 'left'}
  <span ms-attr="@obj">直接引用对象</span>
  <span ms-attr="{width: @width, height: @height}">使用对象字面量</span>
  <span ms-attr="@array">直接引用数组</span>
  <span ms-attr="[@obj1, @obj2, (@toggle ? @obj3: @obj4)]">使用数组字面量,里面可以用三元运算符</span>
            
```