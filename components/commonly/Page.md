### Page
页面相关组件

### PageWrapper
用于包裹页面组件 <br>

#### 用法：
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
#### props

| 属性 | 类型 | 默认值 | 说明 |
| :-: | :-: | :-: | :-: |
| title | `string` | - | pageHeader title |
| dense | `boolean` | `false` | 是否缩小主体区域,为 true 将会取消 padding/margin |
| content | `string` | - | pageHeader Content 内容 |
| contentStyle | `object` | - | 主题区域样式 |
| contentClass | `string` | - | 主题区域class |
| contentBackground | `boolean` | - | 主题区域背景 |
| contentFullHeight | `boolean` | `false` | 主体区域是否占满整个屏幕高度 |
| fixedHeight | `boolean` | `false` | 固定主体区域高度 |

#### Slots
pageHeader 的 slot 都支持

| 名称 | 说明 |
| :-: | :-: |
| leftFooter | PageFooter左侧区域 |
| rightFooter | PageFooter右侧区域 |
| leftFooter | PageFooter字体内容 |
| default | 主体区域 |

### PageFooter
用于页面底部工具栏<br>

#### 用法：
```vue
<template>
  <div>
    <PageFooter>
      <template #left>left</template>
      <template #right>right</template>
    </PageFooter>
  </div>
</template>
<script>
  import { PageFooter } from '/@/components/Page';
  import { defineComponent } from 'vue';
  export default defineComponent({
    components: { PageFooter },
    setup() {
      return {};
    },
  });
</script>
```
#### Slots

| 名称 | 说明 |
| :-: | :-: |
| left | 左侧区域 |
| right | 右侧区域 |
