> antd的Select组件为什么没有addonBefore和addAfter属性

我觉得吧，这种问题我也不好意思提个工单，明明就是我自己的开发需求，怎么能甩锅呢。

我要自己写，嘿嘿，在简单扩展Select组件的时候，我总结了两个比较有意思的点
1. *restProps，缺省内容的应用*
2. *伪元素选择器的组合使用*

***

> 下面是正文代码，参考Input组件的addonBefore和addAfter属性，实现扩展
1. extendSelect.js,我为该组件添加了一个额外的样式.c-extend-select
```javascript
import React from "react";
import { Select } from "antd";
import "./style.less";

export default (props) => {
  const { children, addonBefore, addonAfter, ...restProps} = props;
  return (
    <span class="ant-input-group-wrapper c-extend-select">
      <span class="ant-input-wrapper ant-input-group">
        {addonBefore && <span class="ant-input-group-addon">{addonBefore}</span>}
        <Select {...restProps} >{children}</Select>
        {addonAfter && <span class="ant-input-group-addon">{addonAfter}</span>}
      </span>
    </span>
  );
}
```
**注意：上面的代码中使用到了ES6的特性Rest，即restProps，因为我只对Select组件进行addonBefore/addonAfter属性的扩展，其他的属于Select组件的属性需要设置在Select组件上，我把这些剩余的属性放在一个变量里，该变量表示剩余参数的集合，无需关心里面的内容，它们都属于Select组件，完全设置给Select组件即可**

2. style.less
```less
.c-extend-select{
  .ant-input-group{
    .ant-select{
      width: 100%;
    }
    .ant-select:not(:first-child):not(:last-child) {
      .ant-select-selection{
        border-radius: 0;
      }
    }
    .ant-select:not(:first-child) {
      .ant-select-selection{
        border-top-left-radius: 0;
        border-bottom-left-radius: 0;
      }
    }
    .ant-select:not(:last-child) {
      .ant-select-selection{
        border-top-right-radius: 0;
        border-bottom-right-radius: 0;
      }
    }
  }
}
```
**注意：Select组件的结构是外层ant-select，再往里一层是ant-select-selection。我在Select组件外层套了两层，ant-input-group-wrapper和ant-input-group，并且自定义了样式c-extend-select，有样式隔离的目的。在ant-input-group样式的子元素最多存在三个，select居中，一前一后是addon的内容，根据该特性使用not(:first-child):not(:last-child)来处理边框圆角的问题，实名赞扬这种操作，样式写得太好了**

3. 示范用例
```javascript
import ExtendSelect from "./extendSelect.js";

<ExtendSelect addonBefore="包含" mode="tags" >
  <Option value="1">1</Option>
  <Option value="2">2</Option>
</ExtendSelect>
```
