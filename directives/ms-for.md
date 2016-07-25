#循环绑定

avalon2.0的ms-for绑定集齐了ms-repeat, ms-each, ms-with的所有功能, 并且更好用, 性能提升七八倍

ms-for可以同时循环对象与数组

```html
<body :controller="test">
<script>
   avalon.define({
      $id:'test',
      aaa: [1,2,3]
   })
</script>
<ul>
    <li ms-for="el in @aaa">{{el}}</li>
</ul>
</body>

```
            
现在采用类似angular的语法, in前面如果只有一个变量,那么它就是数组元素或对象的属性名

```html
<body :controller="test">
<script>
   avalon.define({
      $id:'test',
      aaa: avalon.range(1,100),
      updateUlHeight: function(e){
         var li = e.target
         var ul = li.parentNode
         ul.style.height = "200px"//或其他动态计算出来的值
      }
   })
</script>
<ul>
    <li :for="(index,el) in @aaa">{{index}}::{{el}}</li>
</ul>
</body>
```

in 前面有两个变量, 它们需要放在小括号里,以逗号隔开, 那么分别代表数组有索引值与元素, 或对象的键名与键值, 这个与jQuery或avalon的each方法的回调参数一致。


如果你想截取数组的一部分出来单独循环,可以用limitBy过滤器, 使用使用as来引用新数组

```html
<ul>
    <li ms-for="el in @aaa | limitBy(10) as items">{{el}}</li>
</ul>
```
上例是显示数组的前10个元素, 并且将这10个元素存放在items数组中, 以保存过滤或排序结果


上面是每次循环一个li元素,如果想每次循环多个li元素可以使用注释节点

使用注释节点实现循环,解决同时循环多个元素的问题

```html
<ul>
   <!--ms-for:(index,el) in @aaa-->
   <li>{{index}}</li>
   <li>{{el}}</li>
   <!--ms-for-end:-->
</ul>
```
>`ms-for:`与前面的`!--`之间不能有空白,ms-for在注释节点中,也不能像属性那样改成`:for`

avalon 不需要像angular那样要求用户指定trace by或像react 那样使用key属性来提高性能,内部帮你搞定一切

ms-for还可以配套data-for-rendered回调,当列表渲染好时执行此方法
```html
<ul>
    <li ms-for="el in @aaa" data-for-rendered="@updateUlHeight">{{el}}</li>
</ul>
```


如果你只想循环输出数组的其中一部分,请使用filterBy,只想循环输出对象某一些键值并设置默认值,则用selectBy. 不要在同一个元素上使用ms-for与ms-if,因为这样做会在页面上生成大量的注释节点,影响页面性能

可用于ms-for中的过滤器有limitBy, sortBy, filterBy, selectby, orderBy

>ms-for支持下面的元素节点继续使用ms-for,形成双重循环与多级循环, 
但要求`双重循环`对应的`二维数组`.`几维循环`对应`几维数组`

```html
<body :controller="test">
<script>
   avalon.define({
      $id:'test',
      array: [{arr: [111,222, 333]},{arr: [111,222, 333]},{arr: [111,222, 333]}]
   })
</script>
<ul>
     <li :for="el in @array">
       <div :for='elem in el.arr'>{{elem}}</div>
    </li>
</ul>
</body>
```
array的元素里面有子数组,  形成2维数组


**如何双向绑定ms-for中生成的变量?**

由于 循环生成的变量前面不带@, 因此就找不到其对应的属性,需要特别处理一下


```html
<body :controller="test">
<script>
   avalon.define({
      $id:'test',
      styles: {
            width: 200,
            height: 200,
            borderWidth: 1,
            borderColor: "red",
            borderStyle: "solid",
            backgroundColor: "gray"
      }
   })
</script>
<div :for="(key,el) in @styles">
        <label>{{ key }}::{{ el }}</label>
        <input type="text" :duplex="@styles[key]" >
        <!--不能ms-duplex="el"-->
    </div>
</div>
</body>
```
