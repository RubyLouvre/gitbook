# 更新日志

## 2.2.4

* 修复IE下 orderBy BUG
* 更改下载Promise的提示
* 修复avalon.modern 在Proxy 模式下使用ms-for 循环对象时出错的BUG
* 修复effect内部传参 BUG
* 重构ms-validate的绑定事件的机制  

## 2.2.3

* 修复 VElement hackIE BUG
* [增强] 让avalon.bind 在绑定非元素节点也修正事件对象 
* 处理expr的null undefined情况     
* 修正error函数参数顺序导致的错误
* [增强] 支持组件继承(对象形式与函数形式皆可)

## 2.2.2

* fix ms-controller BUG, 上下VM相同时,不会进行合并
* [增强] 为监听数组添加toJSON方法
* IE7的checked属性应该使用defaultChecked来设置
* 对旧版firefox的children进行polyfill
* 修复ms-if,ms-text同在一个元素时出BUG的情况 
* 修复ms-visible,ms-effect同在一个元素时出BUG的情况
* 修复selected属性同步问题

## 2.2.1

* [增强] **支持计算属性**
* [增强] **支持事务**


## 2.2.0

* **全新的依赖收集系统**
* [breakpoint]  减少VM的系统属性，`__const__`, `__data__`,`__proxy__`, `$skipArray`被废掉
* vmodel模块全部重写，让它内部用到的私用方法更加合理
* directives模块全部重写，因为现在不走react的渲染模板思路了
* component模块全部重写，它现在是完全独立的作用域
* strategy模块被打散了，细分为parser与renders与vtree这三个模块。renders里面有domRender与serverRender。
* [breakpoint] $watch不支持`*`
* [breakpoint] if, for不再支持动画


## 2.1.17

*  修复validate BUG
*  解决$render方法不存在的BUG， 这个是老BUG，在2.1.14已经修了
*  fix IE6 fixEvent BUG
*  fix IE6-8 script元素由虚拟DOM变成DOM的BUG
*  fix IE6-8 对noscript元素innerText操作时抛错的BUG
*  fix IE6-8 对opacity对值不正确的BUG
*  fix  Object.freeze方法不存在的BUG
*  添加可用的arthur.js迷你库

## 2.1.16  {% em color='red' %}存在严重BUG{% endem %}

* fix parseExpr BUG #1768 与 #1765
* 优化ms-effect指令,与ms-css指令共同相同的diff
* [增强] data-duplex-changed回调支持更多参数
* 处理$watch监听复杂数BUG #1762
* 处理date过滤器不解析 BUG
* 重构ms-important后面的指令不执行的BUG
* 改成 {% em color='red' %}es6 modules{% endem %}组织依赖,rollup.js打包

## 2.1.15

* [增强] 普通vm也支持onReady, onDispose方法(生命周期)
* [增强] 添加norequire验证规则
* 强化UUID的生成策略
* fix replaceChild的重写BUG(用于onDispose方法)
* [增强] xmp, wbr, template可以直接使用is属性代替ms-widget属性,
* 简化attr指令的实现,其diff逻辑与css指令的diff一样,直接用css指令的
* 一劳永逸解决IE6-8下VBS属性重复定义抛错的BUG
* 新的 jsparser

## 2.1.14

* 修复 ms-important的BUG
* 重构 escapeHTML与unescapeHTML方法
* [breakpoint] 改用id来定义组件VM的$id
* 修复pattern验证规则
* 添加大量测试, {% em color='red' %}覆盖率达到90%{% endem %}

## 2.1.13

* 修复 ms-controller, ms-important的移除类名的实现
* [增强] 实现后端渲染
* fix safari, 微信不支持使用Object.defineProperty重写元素属性的BUG
* 分离DOM API
* 修复 ms-on BUG 

## 2.1.12

* 修复 ms-click 在 ms-if 下失效的问题 #1652


## 2.1.11

* 修复 limitBy BUG
* 修复 节点对齐算法 BUG
* 优化 mediatorFactory
* 修复 data-for-rendered BUG


## 2.1.10

* 修复 ms-for两个BUG
* 修复 ms-controller BUG

## 2.1.9


* 重写for, widget指令,

## 2.1.8

* component/initjs中的protected变量更名为immunity,方便在严格模式下运行
* 为伪事件对象过滤掉原生事件对象中的常量属性   
* 修复class,hover,active指令互相干扰的BUG
* 修复事件绑定中表达式太复杂,不会补上($event)的BUG
* 当组件被移出DOM树并且没有被cached时,其虚拟DOM应该清空上面的事件

## 2.1.7

* 修复注释节点包括HTML结构(里面有引号),节点对齐算法崩溃的BUG
* 修复tap事件误触发BUG
* 升级ms-widget的slot机制,让它们的值也放到组件VM中
* 添加:xxx短指令的支持

## 2.1.6

* {% em color="#ffafc9" %}**全新的lexer与插值表达式抽取方法**{% endem %}
* 添加unescapeHTML与escapeHTML方法
* 修复xmp元素的内容生成BUG
* 修复input.value = newValue的同步BUG
* 修复双击事件BUG
* 修复ms-widget遇上ms-if找到原先占位DOM的BUG

## 2.1.5 


* {% em color="#b20000" %}**添加htmlfy模块**{% endem %} 用于解决IE6-7对colgroup,dd,dt,li,options,p,td,tfoot,th,thead,tr元素自闭合,html parser解析出错的问题 
* 重构ms-controller, ms-important指令
* 虚拟DOM移除template属性
* 修复ms-for的排序问题
* fix 在chrome与firefox下删掉select中的空白节点，会影响到selectedIndex BUG 


## 2.1.4 

* {% em color="##3399ff" %}**修复IE光标问题**{% endem %}
* {% em color="#390" %}**修复输入法问题**{% endem %}
* 修复双层注释节点ms-for循环问题(markRepeatRange BUG)
* ms-html中script, style标签不生效的问题

## 2.1.3 
	
* 修复isSkip方法,阻止regexp, window, date被转换成子VM
* checkbox改用click事件来同步VM #1532
* ms-duplex-string在radio 的更新失效问题

## 2.1.2 

* ms-for+expr在option元素不显示的问题（实质是节点对齐问题）
* 模板中的&copy;&times;没有被htmlDecode的问题
* 绑定在组件模板中最外层元素上的事件不生效
* ie7,8下 ms-duplex 因为onproppertychange环调用，导致辞爆栈的问题
* 修复节点对齐算法中 对空白节点与script等容器处理的处理

## 2.1.1

* 简化VElement转换DOM的逻辑
* 将改 order的连接符为 ‘，’，这样就可以重用更简单的avalon.rword
* 修复e.which BUG
* **修复 ms-duplex-checked在低版本浏览器不断闪烁的问题**

## 2.1.0 

* {% em color="#ffb515" %}**重构lexer方法**{% endem %}
* **添加新的对齐节点算法**
* 修复IE6-8下复制闭包中的对象返回相同对象,导致ms-for出BUG的问题
* [增强] **所有vm都支持onReady,在它第一次刷新作用区载时触发** 
* 重构ms-for, ms-html,ms-if,ms-text,ms-html,ms-on指令
* 参考react 的classNames插件，重构ms-class/active/hover，
* 上线全新的parseHTML，内部基于avalon.lexer，能完美生成script, xml,svg元素
* 重构isInCache， saveInCache


