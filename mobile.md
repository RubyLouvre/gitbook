# 移动端支持


avalon在[这里](https://github.com/RubyLouvre/avalon/tree/master/src/gesture)提供了几种手势模块, 来满足你的移动开发.

PC端与移动端的事件是完全不一样,移动端的大多数事件都是基于ontouchxxx事件合成出来.
这些JS, 我们在使用时,可以直接这样

```javascript
var avalon = require('../avalon')
var tap = require('../tap')
```
使用时

```
<div ms-on-tap="@tapfn">点我</div>
```
> 当你使用tap模块,请把tap与recognizer.js模块放在同一文件夹下.

> drag, pinch, press, rotate, swipe, tap都依赖于recongizer模块.

**drag模块**：在指定的dom区域内，一个手指放下并移动事件，即触屏中的拖动事件。这个事件在屏触开发中比较常用，如：左拖动、右拖动等. 如手要上使用QQ时向右滑动出现功能菜单的效果。具体事件有： 
1. dragstart：拖动开始 
2. dragmove：拖动过程 
3. dragend：拖动结束

**pinch模块**：在指定的dom区域内，两个手指相对（越来越近）移动或相向（越来越远）移动时事件。 具体事件有： 
1. pinchstart：多点触控开始 
2. pinch：多点触控过程 
3. pinchend：多点触控结束 
4. pinchin：多点触控时两手指距离越来越近 
5. pinchout：多点触控时两手指距离越来越远

**press模块**：在指定的dom区域内触屏版本的点击事件(longtap)，这个事件相当于PC端的click事件， 该不能包含任何的移动，最小按压时间为500毫秒，常用于我们在手机上用的“复制、粘贴”等功能。 具体事件有：

1. longtap：长按 
2. doubletap： 双击

**rotate模块**：在指定的dom区域内，当单个手指围绕某个点转动时触发事件. 具体事件有：

1. rotatestart：旋转开始 
2. rotatemove：旋转过程 
3. rotateend：旋转结束

**swipe模块**：在指定的dom区域内，一个手指快速的在触屏上滑动。即我们平时用到最多的滑动事件。 具体事件有：

1. swipeleft：向左滑动 
2. swiperight：向右滑动 
3. swipeup：向上滑动 
4. swipedown：向下滑动 
5. swipe: 滑动(可以通过事件对象的direction属性知道当前滑动方向)

**tap模块**：在指定的dom区域内，一个手指轻拍或点击时触发该事件(类似PC端的click)。 该事件最大点击时间为250毫秒，如果超过250毫秒则按longtap事件进行处理。 具体事件有： 

1. tap: 轻拍