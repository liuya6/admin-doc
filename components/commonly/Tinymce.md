### Tinymce
富文本组件位于 src/components/TinyMce,富文本组件使用的是 CDN 方式引入

### 用法
```
<template>
  <Tinymce v-model="value" @change="handleChange" width="100%" />
</template>
<script lang="ts">
  import { defineComponent, ref } from 'vue';
  import { Tinymce } from '/@/components/Tinymce/index';

  export default defineComponent({
    components: { Tinymce },
    setup() {
      const value = ref('hello world!');
      function handleChange(value: string) {
        console.log(value);
      }
      return { handleChange, value };
    },
  });
</script>
```

### Props

| 属性 | 类型 | 默认值 | 说明 |
| -- | -- | -- | -- |
| options | any | {} | tinymce 的配置项 |
| value(v-model) | string | - | 双向绑定值 |
| height | number , string | 400 | 高度 |
| width | number , string | auto | 宽度 |
| toolbar | string[] | - | 工具栏 |
| plugins | string[] | - | 插件 |
| showImageUpload | boolean | true | 是否显示上传按钮 |

### Events

| 事件名 | 回调参数 | 返回值 | 说明 |
| -- | -- | -- | -- |
| change | (str:string)=>{}	 | - | 富文本内容改变触发事件 |
