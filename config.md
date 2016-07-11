<!-- toc -->
# 配置

avalon2遵循coc原则，配置项比较少。只有两个配置项。

双花括号也默认是python一些著名模板的界定符，为了防止冲突，我们有更换界定符的需求。 这时我们可以这样做

```javascript
 avalon.config({
    interpolate: ['{%','%}']
 })
 //或
 avalon.config({
    interpolate: ['{?','?}']
 })
 //或
 avalon.config({
    interpolate: ['{&','&}']
 })
```
注意,左右界定符的长度应该为2,并且不能出现`<`, `>`,因为出现源码括号在旧式IE下会变成注释节点
我们再看下一个有用的配置项，debug。avalon默认是在控制台下打印没有调试消息的， 上线时我们不愿用户看到它们，可以这样关掉它们。

```javascript
 avalon.config({
    debug: false
 })
```
  
