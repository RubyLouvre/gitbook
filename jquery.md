# 与jQuery共存

jQuery是世界上最流行的DOM库,它拥有各式各样的插件,在日常开发中我们可能还是离不开它.因此与它一起使用是常态,
下面是一些注意

## domReady后如何扫描

```javascript
$(function(){
   var vm = avalon.define({/* */})
   //如果你将vm定义在jQuery的ready方法内部,那么avalon的扫描就会失效,需要手动扫描
   avalon.scan(document.body) //现在只要传入扫描范围的根节点就行
})
```

## 如何AJAX提交数据

提交整个VM
```javascript
jQuery.ajax({
   method: "POST",
   url: 'url-adress',
   //这里是取vm的数据模型 ,通过JSON.stringify会去掉其所有方法, 变成JSON字符串
   //再用JSON.parse变回纯JS对象
   data: JSON.parse(JSON.stringify(vm.$model))
})
```

提交VM中的某个`对象`属性
```javascript
data: JSON.parse(JSON.stringify(vm.data.$model))

``
提交VM中的某个`数组`属性
```javascript
data: JSON.parse(JSON.stringify(vm.data.$model))

``


## 如何让后台回来的数据更新VM

后台的数据更新VM,只能是更新VM的某些已经定义属性.
如果后台数据很大,那么我们可以定义一个空对象(假如后台数据是对象类型)或一个空数组(假如后台数据是数组类型)来占位
```javascript
var vm = avalon.define({
    $id: 'aaa',
    array: []
})

jQuery.ajax({
   method: "POST",
   url: 'url-adress',
   data: {/**/},
   success: function(data){
     vm.array = data.array
  }
})
```

## 如何同步表单的数据

假如我的某个表单是用于jQuery的日历插件,那么它数据如何同步到vm
```javascript
$(datepick_input_css_selector).input(function(){
   vm.aaa = this.value
})
```

如何同步复选框
在avalon中,checkbox要对应一个数组
首选是取得所有同名的checkbox,并要求它们在选中状态,然后用map方法收集它们的value值
```javascript
$('checkbox').change(function(){
   var array = $('checkbox[name="'+this.name+'"]:checked').map(function(){
      return $(this).val();
   })
   vm.checkboxProps = array
})
```

如何同步下拉框

```javascript
$('select').change(function(){
   
   vm.selectProps = $(this).val()
})
```

