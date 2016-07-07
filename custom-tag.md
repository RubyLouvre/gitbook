# 自定义标签

以ms-开头的自定义标签, 我们需要用avalon.component方法定义它,然后在里面使用ms-widget指令 为它添加更多行为.

avalon.component方法有两个参数,第一个标签名,必须以ms-开头;第二个是配置对象.

配置对象里也有4个配置项

1. template,自定义标签的outerHTML,它必须是用一个普通的HTML元素节点包起来,里面可以使用`ms-*`等指令
2. defaults,用来定义这个组件的VM有什么属性与方法
3. soleSlot,表示自定义标签的innerHTML为一个默认的插入点 (或可理解为定义标签的innerHTML为当前组件某个属性的属性值) ,可选
4. getTemplate, 用来修改template, 依次传入vm与template, 返回新的模板. 可选

```javascript
    avalon.component('ms-pager', {
      template: '<div><input type="text" ms-duplex-number="@num"/><button type="button" ms-on-click="@onPlus">+++</button></div>',
      defaults: {
          num: 1,
          onPlus: function () {
              this.num++;
          }
      },
      getTemplate: function(vm, template){
         return template.replace('ms-on-click','ms-on-mousenter')
      }
  });  
```

> 注意,在avalon2中是严禁在绑定属性中混入插值表达式