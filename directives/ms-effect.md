#动画绑定

ms-effect拥有这三种绑定形式:

```html
<p ms-effect="[@configObj,{is:'fade'}">属性值为字面量,其中一个对象必须包括is属性,这用于指定特效名</p>
<p ms-effect="{is:fade, stagger:300}">属性值为对象字面量, 里面拥有is属性</p>
<p ms-effect="@fadeConfig">属性值为vm的对象,里面拥有is属性</p>
```
avalon2实际上没有实现完整的动画模块，它只是对现有的CSS3动画或jquery animate再包装一层。

我们先说如何用CSS3为avalon实现动画效果。首先要使用avalon.effect注册一个特效。
```
avalon.effect(name, definition)
```
> 所有注册了的特效，都可以在avalon.effects对象中找到。

css3动画要求我们至少添加4个类名。这个是从angular那里学过来的。因此如何你以前的项目是基于angular，它那些CSS动画类可以原封不动地搬过来用。

```javascript
avalon.effect('animate', {
    enterClass: 'animate-enter',
    enterActiveClass: 'animate-enter-active',
    leaveClass: 'animate-leave',
    leaveActiveClass: 'animate-leave-active', 
})
```
当然，这些类名会默认帮你添加，因为它内部是这样实现的。

```javascript


avalon.effect = function (name, opts) {
    var definition = avalon.effects[name] = (opts || {})
    if (support.css && definition.css !== false) {
        patchObject(definition, 'enterClass', name + '-enter')
        patchObject(definition, 'enterActiveClass', definition.enterClass + '-active')
        patchObject(definition, 'leaveClass', name + '-leave')
        patchObject(definition, 'leaveActiveClass', definition.leaveClass + '-active')

    }
    patchObject(definition, 'action', 'enter')

}
function patchObject(obj, name, value) {
    if (!obj[name]) {
        obj[name] = value
    }
}
```

因此你可以简化成这样：

```javascript
avalon.effect('animate', {})
avalon.effect('animate')
```
definition配置对象css如果等于false,那么它会强制使用JS方式

注册完，我们就需要在样式表中添加真正的CSS类。

```css
.animate-enter, .animate-leave{
    width:100px;
    height:100px;
    background: #29b6f6;
    transition: width 2s;
    -moz-transition: width 2s; /* Firefox 4 */
    -webkit-transition: width 2s; /* Safari 和 Chrome */
    -o-transition: width 2s; /* Opera */
}  
.animate-enter-active, .animate-leave{
    width:300px;
}
.animate-leave-active{
    width:100px;
}
```
我们还得定义一个vm，里面指明动画的动作（默认有三种方式, enter, leave, move） 及动画结束时的回调（这是可选的）
```javascript
var vm = avalon.define({
    $id: 'effect',
    aaa: "test",
    action: 'enter',
    enterCb: function(){
        avalon.log('动画完成')
    },
    leaveCb: function(){
        avalon.log('动画回到原点')
    }
})
```
然后页面上这样使用：
```html
<div ms-controller='effect' >
    <div ms-effect="{is:'animate', action:@action,onEnterDone: @enterCb,onLeaveDone: @leaveCb}">
        {{@aaa}}
    </div>
    <button ms-click='@action = @action !== "leave" ? "leave": "enter"'
            type="button">click</button>
</div>
```
ms-effect的值为一个对象，其中is是必选。除了action, 还支持这么多种回调：
```
onEnterDone, onLeaveDone, onEnterAbort, onLeaveAbort, onBeforeEnter, onBeforeLeave
```
![](effect1.gif)

如果使用JS实现，则是这样的：
```html
<!DOCTYPE html>
<html>
    <head>
        <title>TODO supply a title</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="../dist/avalon.js"></script>
        <script src="//cdn.bootcss.com/jquery/3.0.0-beta1/jquery.js"></script>
        <style>
            .ani{
               width:100px;
               height:100px;
               background: #29b6f6;
            }
        </style>
        <script>
            avalon.effect('animate', {
               enter: function(el, done){
                   $(el).animate({width: 300},1000,done)
               },
               leave: function(el, done){
                   $(el).animate({width: 100},1000,done)
               }
            })
            var vm = avalon.define({
                $id: 'effect',
                aaa: "effect",
                action: 'enter',
                enterCb: function(){
                    avalon.log('动画完成')
                },
                leaveCb: function(){
                    avalon.log('动画回到原点')
                }
            })
           
          
        </script>
    </head>
    <body>
        <div ms-controller='effect' >
            <div class='ani' ms-effect="{is:'animate', action:@action,onEnterDone: @enterCb,onLeaveDone: @leaveCb}">
                {{@aaa}}
            </div>
            <button ms-click='@action = @action !== "leave" ? "leave": "enter"'
                    type="button">click</button>
        </div>
    </body>
</html>
```
一个CSS3位置效果
```html
<!DOCTYPE html>
<html>
    <head>
        <title>TODO supply a title</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="../dist/avalon.js"></script>
        <script src="//cdn.bootcss.com/jquery/3.0.0-beta1/jquery.js"></script>
        <style>
           .ani{
                width:100px;
                height:100px;
                background: #ff6e6e;
            }
            .wave-enter, .wave-leave {
                -webkit-transition:all cubic-bezier(0.250, 0.460, 0.450, 0.940) 0.5s;
                -moz-transition:all cubic-bezier(0.250, 0.460, 0.450, 0.940) 0.5s;
                -o-transition:all cubic-bezier(0.250, 0.460, 0.450, 0.940) 0.5s;
                transition:all cubic-bezier(0.250, 0.460, 0.450, 0.940) 0.5s;
            }

            .wave-enter {
                position:absolute;
                left:45%;
            }

            .wave-enter-active {
                left:0;
            }

            .wave-leave {
                position:absolute;
                left:0;
            }

            .wave-leave-active {
                left:45%;
            }

        </style>
        <script>
            avalon.effect('wave', {})
            var vm = avalon.define({
                $id: 'effect',
                action: 'enter',
                enterCb: function () {
                    avalon.log('动画完成')
                },
                leaveCb: function () {
                    avalon.log('动画回到原点')
                }
            })


        </script>
    </head>
    <body>
        <div ms-controller='effect' >
            <div class='ani' ms-effect="{is:'wave', action:@action,onEnterDone: @enterCb,onLeaveDone: @leaveCb}">
                <button ms-click='@action = @action !== "leave" ? "leave": "enter"'
                        type="button">click</button>
            </div>

        </div>
    </body>
</html>
```

```html
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <meta charset="utf-8">
        <meta name="format-detection" content="telephone=no">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0,user-scalable=no">
        <style>
            .animate-enter, .animate-leave{
                width:100px;
                height:100px;
                background: #29b6f6;
                transition: width 2s;
                -moz-transition: width 2s; /* Firefox 4 */
                -webkit-transition: width 2s; /* Safari 和 Chrome */
                -o-transition: width 2s; /* Opera */
            }  
            .animate-enter-active, .animate-leave{
                width:300px;
            }
            .animate-leave-active{
                width:100px;
            }
        </style>
        <title></title>
    </head>
    <body ms-controller="body">
        <button ms-click="@toggle">toggle</button>
        <div ms-effect="{is : 'animate',action: @action}"></div>
        <script src="../../dist/avalon.js"></script>
        <script>
            avalon.effect("animate",{
                onEnterDone: function(){
                   avalon.log('enter done')
                },
                onLeaveDone: function(){
                   avalon.log('leave done')
                }
            });
           
          var vm =  avalon.define({
                $id : "body",
                action: 'enter',
                toggle : function(){
                    vm.action = vm.action === 'enter' ? 'leave' : 'enter'
                    avalon.log('toggle', vm.action)
                }
            });
        </script>
    </body>
</html>

```
** ms-widget+ms-for+ms-if+ms-effect的动画效果 **

```html
<!DOCTYPE html>
<html>
    <head>
        <title>ms-if</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width">
        <script src="../dist/avalon.js"></script>
        <script src="//cdn.bootcss.com/jquery/3.0.0-beta1/jquery.js"></script>
        <style>
            .ani{
                width:100px;
                height:100px;
                background: #ff6e6e;
            }
        </style>
        <script >
            avalon.component('ms-button', {
                template: '<button type="button"><span><slot name="buttonText"></slot></span></button>',
                defaults: {
                    buttonText: "button"
                },
                soleSlot: 'buttonText'
            })
            avalon.effect('zoom', {
                enter: function (el, done) {

                    $(el).css({width: 0, height: 0}).animate({
                        width: 100, height: 100
                    }, 1000, done)
                },
                leave: function (el, done) {
                    $(el).animate({
                        width: 0, height: 0
                    }, 1000, done)
                }
            })
            var vm = avalon.define({
                $id: "effect1",
                arr: [1,2,3],
                aaa: 222,
                toggle: true
            })

        </script>

    </head>
    <body ms-controller="test" >
        <div ms-for="el in @arr">
        <div class='ani' 
             ms-attr="{eee:el}" 
             ms-if="@toggle" 
             ms-widget='{is:"ms-button"}' 
             ms-effect="{is:'zoom'}">{{@aaa}}::{{el}}</div>
        </div>
        <button ms-click="@toggle = !@toggle " >点我 </button >
    </body>
</html>
```
** ms-for与stagger的动画效果 **

这次为了与angular一致，stagger改为一个小数，它会让当前元素延迟stagger秒执行。

```html
<!DOCTYPE html>
<html>
 
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="../dist/avalon.js"></script>
    <style>
        .my-repeat-animation {
            width: 400px;
            height: 30px;
            -webkit-animation-duration: 1s;
            animation-duration: 1s;
        }
         
        .ng-enter {
            -webkit-animation-name: enter_animation;
            animation-name: enter_animation;
        }
        .ng-enter-stagger {
           animation-delay:300ms;
           -webkit-animation-delay:300ms;
        }
        .ng-leave {
            -webkit-animation-name: leave_animation;
            animation-name: leave_animation;
        }
         
        @keyframes enter_animation {
            0% {
                opacity: 0;
            }
            100% {
                opacity: 1;
            }
        }
         
        @keyframes leave_animation {
            from {
                opacity: 1;
            }
            to {
                opacity: 0;
            }
        }
         
        @-webkit-keyframes enter_animation {
            from {
                opacity: 0;
            }
            to {
                opacity: 1;
            }
        }
         
        @-webkit-keyframes leave_animation {
            from {
                opacity: 1;
            }
            to {
                opacity: 0;
            }
        }
    </style>
    <script>
        avalon.effect("my-repeat-animation", {
            enterClass: "ng-enter",
            leaveClass: "ng-leave"
        })
        var vm = avalon.define({
            $id: "test",
            array: [1, 2, 3, 4],
            getBg: function() {
                return '#' + Math.floor(Math.random() * 16777215).toString(16);
            },
            add: function() {
                vm.array.push(vm.array.length + 1)
                vm.array.push(vm.array.length + 1)
                vm.array.push(vm.array.length + 1)
                vm.array.push(vm.array.length + 1)
                vm.array.push(vm.array.length + 1)
                vm.array.push(vm.array.length + 1)
                vm.array.push(vm.array.length + 1)
                vm.array.push(vm.array.length + 1)
                vm.array.push(vm.array.length + 1)
            },
            value: ""
        })
        vm.$watch("value", function(a) {
            if (a) {
                vm.array.removeAll(function(el) {
                    return el !== a
                })
            } else {
                if(vm.array.length < 12)
                  vm.add()
            }
        })
    </script>
</head>
 
<body ms-controller="test">
    <button ms-click="@add">Add</button>
    <input placeholder="只保留" ms-duplex-number="@value" />
    <div class="my-repeat-animation"  ms-for="item in @array" 
         ms-css="{background:@getBg()}"
         ms-effect="{is:'my-repeat-animation',stagger:0.3}">
        {{item}}
    </div>
</body>
 
</html>
```

目前，avalon的ms-effect可以与ms-visible,ms-if,ms-repeat连用。ms-effect也可以单独或与其他指令使用，这时需要你指定action。

```html
<div ms-effect="{is:"effectName", action: @action}">
```