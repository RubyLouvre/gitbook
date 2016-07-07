#skip绑定

让avalon的扫描引擎跳过某一部分区域, 方便{{@aaa}}能原样输出

ms-skip能大大提高性能

```html
  <div ms-controller='aaa'>
      <div ms-skip>
          {{@aaa}}会直接输出来
      </div>
      {{@aaa}}会替换成vm.aaa的值
  </div>
```
