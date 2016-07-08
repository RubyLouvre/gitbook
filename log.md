# 更新日志

## 2.1.5 ##

重构ms-controller, ms-important指令

虚拟DOM移除template属性

修正ms-for的排序问题

fix 在chrome与firefox下删掉select中的空白节点，会影响到selectedIndex BUG 

添加htmlfy模块,解决IE6-7对colgroup,dd,dt,li,options,p,td,tfoot,th,thead,tr元素自闭合,
html parser解析出错的问题 

## 2.1.4 ##

修复光标问题

修复输入法问题	

修复双层注释节点ms-for循环问题(markRepeatRange BUG)

ms-html中script, style标签不生效的问题

## 2.1.3 ##
	
修正isSkip方法,阻止regexp, window, date被转换成子VM

checkbox改用click事件来同步VM #1532

ms-duplex-string在radio 的更新失效问题

## 2.1.2 ##
ms-duplex-string在radio 的更新失效问题

ms-for+expr在option元素不显示的问题（实质是节点对齐问题）

模板中的&copy;&times;没有被htmlDecode的问题

绑定在组件模板中最外层元素上的事件不生效

ie7,8下 ms-duplex 因为onproppertychange环调用，导致辞爆栈的问题

修正节点对齐算法中 对空白节点与script等容器处理的处理

## 2.1.1 ##

简化 VElement转换DOM的逻辑
将改 order的连接符为 ‘，’，这样就可以重用更简单的avalon.rword
修正e.which BUG
修正 ms-duplex-checked在低版本浏览器不断闪烁的问题

## 2.1.0 ##

**一个里程碑的版本**,
```
重构patch机制,现在是一边diff一边patch,一个遍历搞定!
```

修复IE6-8下复制闭包中的对象返回相同对象,导致ms-for出BUG的问题

所有vm都支持onReady,在它第一次刷新作用区载时触发 

添加新的对齐节点算法

重构lexer方法

完全重写ms-for, ms-html指令
重构ms-if指令

重构ms-text,让其刷新工作交给expr表达式处理

修正ms-html向下传参

修正on指令的UUID问题

修正__local__往下传递 问题

参考react 的classNames插件，重构ms-class/active/hover，

上线全新的parseHTML，内部基于avalon.lexer，能完美生成script, xml,svg元素

重构isInCache， saveInCache

-------------------------

## avalon内部运作原理 ##



avaon会在DOMReady对.ms-controller节点进行outerHTML操作
之前是直接用outerHTML,但后来发现outerHTML在各个浏览器下差异性太大了.
IE6-7会对colgroup,dd,dt,li,options,p,td,tfoot,th,thead,tr元素自闭合
让我的htmlPaser跪掉
于是写了htmlfy,手动取每个元素nodeName, attrName, attrValue, nodeValue来构建outerHTML

第2阶段,将这个字符串进行parser,转换为虚拟DOM
这个阶段对input/textarea元素补上type属性,
ms-*自定义元素补上ms-widget属性,
对table元素补上tbody,
在ms-for指令的元素两旁加上
`<!--ms-for-->`,`<!--ms-for-end-->`占位符,
并将它们的之间的元素放到一个数组中(表明它们是循环区域)
并去掉所有只有空白的文本节点

第3个阶段,优化,对拥有ms-*属性的虚拟DOM添加dynamic属性
表明它以后要保持其对应的真实节点
并对没有ms-*属性的元素添加skipAttrs属性,表明以后不需要遍历其属性
如果它的子孙没有ms-*或{{}}插值表达式或ms-自定义元素,那么还加上skipContent
表明以后不要遍历其孩子.

这三个属性,dynamic用于节点对齐算法,skipAttrs与skipContent用于diff算法

第4个阶段, 应用节点对齐算法, 将真实DOM中无用的空白节点移除,并插入占位符,
并将需要刷新的元素保持在以应的拥有dynamic属性的虚拟DOM中


第5个阶段,放进render方法中,render方法里面再调parseView,parseView会调每个指令的parse方法
将虚拟DOM树转换为一个$render方法

第6个阶段,执行$render方法,生成新的虚拟DOM,与最早的那个虚拟DOM树diff,一边diff一边更新真实DOM.

以后VM的属性发生变动,就直接执行第6个阶段.








