### Drawer 抽屉组件
对 antv 的 drawer 组件进行封装，扩展拖拽，全屏，自适应高度等功能。

### 用法
由于 drawer 内部代码一般独立成单独文件，推荐独立成组件来进行开发，所以示例都是以独立的文件来进行说明 <br>

独立组件代码，用于写组件内部的内容

```vue
<template>
  <BasicDrawer v-bind="$attrs" title="Drawer Title" width="50%"> Drawer Info. </BasicDrawer>
</template>
<script lang="ts">
  import { defineComponent } from 'vue';
  import { BasicDrawer } from '/@/components/Drawer';
  export default defineComponent({
    components: { BasicDrawer },
  });
</script>
```

页面引用弹窗
```vue
<template>
  <div>
    <Drawer @register="register" />
  </div>
</template>
<script lang="ts">
  import { defineComponent } from 'vue';
  import { Alert } from 'ant-design-vue';
  import { useDrawer } from '/@/components/Drawer';
  import Drawer from './Drawer.vue';

  export default defineComponent({
    components: { Drawer },
    setup() {
      const [register, { openDrawer }] = useDrawer();
      return {
        register,
        openDrawer,
      };
    },
  });
</script>
```

### useDrawer

#### useDrawer 用于操作组件
```vue
const [register, { openDrawer, setDrawerProps }] = useDrawer();
```
#### register
register 用于注册 `useDrawer`，如果需要使用 `useDrawer` 提供的 api，必须将 `register` 传入组件的 `onRegister`。

原理其实很简单，就是 vue 的组件子传父通信，内部通过 `emit("register"，instance)` 实现。

同时，独立出去的组件需要将 `attrs` 绑定到 Drawer 的上面。

```vue
<BasicDrawer v-bind="$attrs"> Drawer Info. </BasicDrawer>
```

#### openDrawer
用于打开/关闭弹窗
```vue
// true/false: 打开关闭弹窗
// data: 传递到子组件的数据
openDrawer(true, data);
```

#### closeDrawer 
用于关闭弹窗
```vue
closeDrawer();
```

#### setDrawerProps
用于更改 drawer 的 props 参数因为 drawer 内容独立成组件，如果在外部页面需要更改 props 可能比较麻烦，所以提供 setDrawerProps 方便更改内部 drawer 的 props <br>
Props 内容可以见下方

### Props
除以下参数外，官方文档内的 props 也都支持，具体可以参考 antv drawer。

| 属性 | 类型 | 默认值 | 说明 |
| -- | -- | -- | -- |
| isDetail | boolean | false | 是否为详情模式 |
| loading | boolean | false | loading状态 |
| loadingText | string | '' | loading文本 |
| showDetailBack | boolean | true | - | isDetail=true 状态下是否显示返回按钮 |
| closeFunc | () => Promise<boolean> | - | - | 自定义关闭函数，返回true关闭，否则不关闭 |
| showFooter | boolean | - | - | 是否显示底部 |
| footerHeight | number | 60 | - | 底部区域高度 |

### Events

| 事件名 | 回调参数 | 说明 |
| -- | -- | -- | -- |
| close | (e)=>void | 点击关闭回调 |
| visible-change | (visible:boolean)=>void | 弹窗打开关闭时触发 |
| ok | (e)=>void | 点击确定回调 |




