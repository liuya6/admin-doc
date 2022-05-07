### 常用组件

### Page
页面相关组件

###### PageWrapper
用于包裹页面组件 <br>

用法：
```vue
<template>
  <div>
    <PageWrapper>
      <template #left>left</template>
      <template #right>right</template>
    </PageWrapper>
  </div>
</template>
<script>
  import { PageWrapper } from '/@/components/Page';
  import { defineComponent } from 'vue';
  export default defineComponent({
    components: { PageWrapper },
    setup() {
      return {};
    },
  });
</script>
```

