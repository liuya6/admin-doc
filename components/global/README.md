### 全局组件

###### button
二次封装按钮组件，且使用相同的组件名替换全局的 a-button 组件

> 提示： <br>
> 按钮不需要 import，已经全局注册，直接使用 a-button 标签即可 <br>
> 如果是 Tsx 文件，需要手动 import <br>

###### 用法

```vue
<template>
  <a-button color="success">成功按钮</a-button>
  <a-button color="error">错误按钮</a-button>
  <a-button color="warning">警告按钮</a-button>
</template>
```

###### Props
保持 anv design button 组件 原有功能的情况下扩展以下属性

| 属性 | 类型 | 默认值 | 说明 |
| :-: | :-: | :-: | :-: |
| color | '`error`' '`warning`' '`success`' | - | 按钮的颜色场景状态颜色 |
| preIcon | `string` | - | 按钮文本前图标 |
| postIcon | `string` | - | 按钮文本后图标 |
| iconSize | `number` | 14 | 按钮图标大小 |

