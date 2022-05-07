### Icon
用于项目内组件的展示，基本支持所有图标库（支持按需加载，只打包所用到的图标）

icon 组件位于 `src/components/Icon` 内

> 提示<br>
> icon 的值可以在 [Iconify](https://iconify.design/) 或 [Netlify](https://icones.netlify.app/collection/ant-design) 上查询。（进不去的话就要科学上网了）

#### 用法
```vue
<template>
  <Icon icon="gg:loadbar-doc"></Icon>
</template>

<script>
  import { defineComponent } from 'vue';
  import { Icon } from '/@/components/Icon';
  export default defineComponent({
    components: { Icon },
  });
</script>
```

#### Props

| 属性 | 类型 | 默认值 | 说明 |
| :-: | :-: | :-: | :-: |
| icon | `string` | - | 图标名 |
| color | `string` | - | 图标颜色 |
| size | `number` | 16 | 图标大小 |
| prefix | `string` | - | 图标前缀 |

### SvgIcon
用于使用项目 svg 雪碧图

#### 用法
```vue
<template>
  <div>
    <SvgIcon name="test"> </SvgIcon>
  </div>
</template>
<script>
  import { SvgIcon } from '/@/components/Icon';
  import { defineComponent } from 'vue';
  export default defineComponent({
    components: { SvgIcon },
  });
</script>
```

#### Props
| 属性 | 类型 | 默认值 | 说明 |
| :-: | :-: | :-: | :-: |
| name | `string` | - | svg图标名 |
| size | `number` | 16 | 图标大小 |


