### PopConfirmButton 按钮

带有 PopConfirm 下拉菜单功能的按钮

### 用法

```
<template>
  <PopConfirmButton>按钮文本</PopConfirmButton>
</template>

<script>
  import { defineComponent } from 'vue';
  import { PopConfirmButton } from '/@/components/Button';
  export default defineComponent({
    components: { PopConfirmButton },
  });
</script>
```

### Props
保持 anv design popconfirm 组件 原有功能的情况下扩展以下属性

| 属性 | 类型 | 默认值 | 说明 |
| :-: | :-: | :-: | :-: |
| enable | boolean | true | 是否启用下拉菜单，为 false 则显示默认按钮 |
