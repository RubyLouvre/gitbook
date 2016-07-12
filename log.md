# 更新日志

## 2.1.6

{% em color="#b20000" %}**全新的lexer与插值表达式抽取方法**{% endem %}

添加unescapeHTML与escapeHTML方法

修正xmp元素的内容生成BUG

修正input.value = newValue的同步BUG

修正双击事件BUG

修正ms-widget遇上ms-if找到原先占位DOM的BUG

## 2.1.5 


{% em color="#b20000" %}**添加htmlfy模块**{% endem %}
```
用于解决IE6-7对colgroup,dd,dt,li,options,p,td,tfoot,th,thead,tr元素自闭合,html parser解析出错的问题 
```

重构ms-controller, ms-important指令

虚拟DOM移除template属性

修正ms-for的排序问题

fix 在chrome与firefox下删掉select中的空白节点，会影响到selectedIndex BUG 



## 2.1.4 

{% em color="##3399ff" %}**修复IE光标问题**{% endem %}

{% em color="#390" %}**修复输入法问题**{% endem %}

修复双层注释节点ms-for循环问题(markRepeatRange BUG)

ms-html中script, style标签不生效的问题

## 2.1.3 
	
修正isSkip方法,阻止regexp, window, date被转换成子VM

checkbox改用click事件来同步VM #1532

ms-duplex-string在radio 的更新失效问题

## 2.1.2 


ms-for+expr在option元素不显示的问题（实质是节点对齐问题）

模板中的&copy;&times;没有被htmlDecode的问题

绑定在组件模板中最外层元素上的事件不生效

ie7,8下 ms-duplex 因为onproppertychange环调用，导致辞爆栈的问题

修正节点对齐算法中 对空白节点与script等容器处理的处理

## 2.1.1

简化VElement转换DOM的逻辑

将改 order的连接符为 ‘，’，这样就可以重用更简单的avalon.rword

修正e.which BUG

**修正 ms-duplex-checked在低版本浏览器不断闪烁的问题**

## 2.1.0 

{% em color="#ffb515" %}**重构lexer方法**{% endem %}

**添加新的对齐节点算法**

修复IE6-8下复制闭包中的对象返回相同对象,导致ms-for出BUG的问题

**所有vm都支持onReady,在它第一次刷新作用区载时触发** 

重构ms-for, ms-html,ms-if,ms-text,ms-html,ms-on指令

参考react 的classNames插件，重构ms-class/active/hover，

上线全新的parseHTML，内部基于avalon.lexer，能完美生成script, xml,svg元素

重构isInCache， saveInCache











