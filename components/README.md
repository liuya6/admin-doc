### 组件介绍

项目的组件大部分都是二次封装的antv（二次封装的意义：在原始组件基础上扩展一些自己的方法），即antv原始的方法也都适用。<br>

项目的组件大部分没有进行全局注册。采用了按需引入注册方式，如下

```vue
<template>
  <ConfigProvider>
    <router-view />
  </ConfigProvider>
</template>

<script lang="ts">
  import { defineComponent } from 'vue';
  import { ConfigProvider } from 'ant-design-vue';
  export default defineComponent({
    name: 'App',
    components: { ConfigProvider },
  });
</script>
```

