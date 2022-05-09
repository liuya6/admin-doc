### <font color=red>Form</font>
对 antv 的 form 组件进行封装，扩展一些常用的功能

### <font color=red>用法</font>

##### useForm方式
示例：
```vue
<template>
  <div class="m-4">
    <BasicForm
      :labelWidth="100"
      :schemas="schemas"
      :actionColOptions="{ span: 24 }"
      @submit="handleSubmit"
    />
  </div>
</template>
<script lang="ts">
  import { defineComponent } from 'vue';
  import { BasicForm, FormSchema } from '/@/components/Form';
  import { CollapseContainer } from '/@/components/Container';
  import { useMessage } from '/@/hooks/web/useMessage';
  const schemas: FormSchema[] = [
    {
      field: 'field',
      component: 'Input',
      label: '字段1',
      colProps: {
        span: 8,
      },
      defaultValue: '1',
      componentProps: {
        placeholder: '自定义placeholder',
        onChange: (e) => {
          console.log(e);
        },
      },
    },
  ];

  export default defineComponent({
    components: { BasicForm, CollapseContainer },
    setup() {
      const { createMessage } = useMessage();
      return {
        schemas,
        handleSubmit: (values: any) => {
          createMessage.success('click search,values:' + JSON.stringify(values));
        },
      };
    },
  });
</script>
```

##### template 方式
所有可调用函数见下方 `Methods` 说明

```vue
<template>
  <div class="m-4">
      <BasicForm
        :schemas="schemas"
        ref="formElRef"
        :labelWidth="100"
        @submit="handleSubmit"
        :actionColOptions="{ span: 24 }"
      />
  <div>
</template>
<script lang="ts">
  import { defineComponent, ref } from 'vue';
  import { BasicForm, FormSchema, FormActionType, FormProps } from '/@/components/Form';
  import { CollapseContainer } from '/@/components/Container';
  const schemas: FormSchema[] = [
  ];

  export default defineComponent({
    components: { BasicForm, CollapseContainer },
    setup() {
      const formElRef = ref<Nullable<FormActionType>>(null);
      return {
        formElRef,
        schemas,
        setProps(props: FormProps) {
          const formEl = formElRef.value;
          if (!formEl) return;
          formEl.setProps(props);
        },
      };
    },
  });
</script>
```



### <font color=red>useForm</font>
form 组件提供了 `useForm`，方便调用函数内部方法<br>
示例:
```vue
<template>
  <BasicForm @register="register" @submit="handleSubmit" />
</template>
<script lang="ts">
  import { defineComponent } from 'vue';
  import { BasicForm, FormSchema, useForm } from '/@/components/Form/index';
  import { CollapseContainer } from '/@/components/Container/index';
  import { useMessage } from '/@/hooks/web/useMessage';
  const schemas: FormSchema[] = [
    {
      field: 'field1',
      component: 'Input',
      label: '字段1',
      colProps: {
        span: 8,
      },
      componentProps: {
        placeholder: '自定义placeholder',
        onChange: (e: any) => {
          console.log(e);
        },
      },
    },
  ];

  export default defineComponent({
    components: { BasicForm, CollapseContainer },
    setup() {
      const { createMessage } = useMessage();
      const [register, { setProps }] = useForm({
        labelWidth: 120,
        schemas,
        actionColOptions: {
          span: 24,
        },
      });
      return {
        register,
        schemas,
        handleSubmit: (values: any) => {
          createMessage.success('click search,values:' + JSON.stringify(values));
        },
        setProps,
      };
    },
  });
</script>
```
##### 参数介绍
```
const [register, methods] = useForm(props);
```
参数 `props` 内的值可以是 `computed` 或者 ref 类型

###### register

register 用于注册 `useForm`，如果需要使用 `useForm` 提供的 api，必须将 register 传入组件的 `onRegister`

```
<template>
  <BasicForm @register="register" @submit="handleSubmit" />
</template>
<script>
  export default defineComponent({
    components: { BasicForm },
    setup() {
      const [register] = useForm();
      return {
        register,
      };
    },
  });
</script>
```

`Methods`见下方说明

##### Methods

| 方法名 | 类型 | 说明 |
| :-: | :-: | :-: |
| getFieldsValue | () => Recordable | 获取表单值 |
| setFieldsValue | <T>(values: T) => Promise<void> | 设置表单字段值 |
| resetFields | ()=> Promise<any> | 重制表单值 |
| validateFields | (nameList?: NamePath[]) => Promise<any> | 校验指定表单项 |
| validate | (nameList?: NamePath[]) => Promise<any> | 校验整个表单 |
| submit | () => Promise<void> | 提交表单 |
| scrollToField | (name: NamePath, options?: ScrollOptions) => Promise<void> | 滚动到对应字段位置 |
| clearValidate | (name?: string &vert; string[]) => Promise<void> | 清空校验 |
| setProps | (formProps: Partial<FormProps>) => Promise<void> | 设置表单 Props |
| removeSchemaByFiled | (field: string &vert; string[]) => Promise<void> | 根据 field 删除 Schema |
| appendSchemaByField | ( schema: FormSchema, prefixField: string &vert; undefined, first?: boolean &vert; undefined ) => Promise<void> | 插入到指定 filed 后面，如果没传指定 field，则插入到最后,当 first = true 时插入到第一个位置 |
| updateSchema | (data: Partial<FormSchema> &vert; Partial<FormSchema>[]) => Promise<void> | 更新表单的 schema, 只更新函数所传的参数 |

##### Props

> 除以下参数外，antv form的props也都支持

| 属性 | 类型 | 默认值 | 可选值 | 说明 |
| :-: | :-: | :-: | :-: | :-: |
| schemas | Schema[] | - | - | 表单配置，见下方FormSchema配置 |
| submitOnReset | boolean | false | - | 重置时是否提交表单 |
| labelCol | Partial<ColEx> | - | - | 表单的labelCol配置 |
| wrapperCol | Partial<ColEx> | - | - | 表单的wrapperCol配置 |
| baseColProps | Partial<ColEx> | - | - | 配置所有选子项的 ColProps，不需要逐个配置，子项也可单独配置优先与全局 |
| baseRowStyle | object | - | - | 配置所有Row的style样式 |
| labelWidth | number,string | - | - | 扩展 form 组件，增加 label 宽度，表单内所有组件适用，可以单独在某个项覆盖或者禁用 |
| labelAlign | string | - | left,right | label布局 |
| mergeDynamicData | object | - | - | 额外传递到子组件的参数 values |
| autoFocusFirstItem | boolean | false | true/false | 是否聚焦第一个输入框，只在第一个表单项为 input 的时候作用 |
| compact | boolean | false | true/false | 紧凑类型表单，减少 margin-bottom |
| size | string | default | default,small,large | 向表单内所有组件传递 size 参数,自定义组件需自行实现 size 接收 |
| disabled | boolean | false | true/false | 向表单内所有组件传递 disabled 属性，自定义组件需自行实现 disabled 接收 |
| autoSetPlaceHolder | boolean | true | true/false | 自动设置表单内组件的 placeholder，自定义组件需自行实现 |
| autoSubmitOnEnter | boolean | false | true/false | 向表单内所有组件传递 disabled 属性，自定义组件需自行实现 disabled 接收 |
| rulesMessageJoinLabel | boolean | false | true/false | 如果表单项有校验，会自动生成校验信息，该参数控制是否将字段中文名字拼接到自动生成的信息后方 |
| showAdvancedButton | boolean | false | true/false | 是否显示收起展开按钮 |
| emptySpan | number , Partial<ColEx> | 0 | true/false | 空白行格,可以是数值或者 col 对象 数 |
| autoAdvancedLine | number | 3 | - | 如果 showAdvancedButton 为 true，超过指定行数行默认折叠 |
| alwaysShowLines | number | 1 | - | 折叠时始终保持显示的行数 |
| showActionButtonGroup | boolean | true | true/false | 是否显示操作按钮(重置/提交) |
| actionColOptions | Partial<ColEx> | - | - | 操作按钮外层 Col 组件配置，如果开启 showAdvancedButton，则不用设置，具体见下方 actionColOptions |
| showResetButton | boolean | true | - | 是否显示重置按钮 |
| resetButtonOptions | object | - | - | 重置按钮配置见下方 ActionButtonOption |
| showSubmitButton | boolean | true | - | 是否显示提交按钮 |
| submitButtonOptions | object | - | - | 确认按钮配置见下方 ActionButtonOption |
| resetFunc | () => Promise<void> | - | - | 自定义重置按钮逻辑() => Promise<void>; |
| submitFunc | () => Promise<void> | - | - | 自定义提交按钮逻辑() => Promise<void>; |
| fieldMapToTime | [string, [string, string], string?][] | - | - | 用于将表单内时间区域的应设成 2 个字段,见下方说明	 |

##### ColEx
```
type ColSpanType = number | string;
export interface ColEx {
  style?: any;
  /**
   * 要占用的单元格数，0 对应不显示（display:none）
   * @default none (0)
   * @type ColSpanType
   */
  span?: ColSpanType;

  /**
   * 用于弹性布局模式
   * @default 0
   * @type ColSpanType
   */
  order?: ColSpanType;

  /**
   * flex的布局填充
   * @default none
   * @type ColSpanType
   */
  flex?: ColSpanType;

  /**
   * 从左侧偏移 Col 的单元格数
   * @default 0
   * @type ColSpanType
   */
  offset?: ColSpanType;

  /**
   * 栅格向右移动的单元格数
   * @default 0
   * @type ColSpanType
   */
  push?: ColSpanType;

  /**
   * 栅格向左移动的单元格数
   * @default 0
   * @type ColSpanType
   */
  pull?: ColSpanType;

  /**
   * <576px 也是默认设置，可以是跨度值或包含上述道具的对象
   * @type { span: ColSpanType, offset: ColSpanType } | ColSpanType
   */
  xs?: { span: ColSpanType; offset: ColSpanType } | ColSpanType;

  /**
   * ≥576px，可以是跨度值或包含上述道具的对象
   * @type { span: ColSpanType, offset: ColSpanType } | ColSpanType
   */
  sm?: { span: ColSpanType; offset: ColSpanType } | ColSpanType;

  /**
   * ≥768px，可以是跨度值或包含上述道具的对象
   * @type { span: ColSpanType, offset: ColSpanType } | ColSpanType
   */
  md?: { span: ColSpanType; offset: ColSpanType } | ColSpanType;

  /**
   * ≥992px,可以是跨度值或包含上述道具的对象
   * @type { span: ColSpanType, offset: ColSpanType } | ColSpanType
   */
  lg?: { span: ColSpanType; offset: ColSpanType } | ColSpanType;

  /**
   * ≥1200px, 可以是跨度值或包含上述道具的对象
   * @type { span: ColSpanType, offset: ColSpanType } | ColSpanType
   */
  xl?: { span: ColSpanType; offset: ColSpanType } | ColSpanType;

  /**
   * ≥1600px,可以是跨度值或包含上述道具的对象
   * @type { span: ColSpanType, offset: ColSpanType } | ColSpanType
   */
  xxl?: { span: ColSpanType; offset: ColSpanType } | ColSpanType;
}
```

##### fieldMapToTime
将表单内时间区域的值映射成 2 个字段<br>
如果表单内有时间区间组件，获取到的值是一个数组，但是往往我们传递到后台需要是 2 个字段

```
useForm({
  fieldMapToTime: [
    // data为时间组件在表单内的字段，startTime，endTime为转化后的开始时间于结束时间
    // 'YYYY-MM-DD'为时间格式，参考moment
    ['datetime', ['startTime', 'endTime'], 'YYYY-MM-DD'],
    // 支持多个字段
    ['datetime1', ['startTime1', 'endTime1'], 'YYYY-MM-DD HH:mm:ss'],
  ],
});

// fieldMapToTime没写的时候表单获取到的值
{
  datetime: [Date(),Date()]
}
//  ['datetime', ['startTime', 'endTime'], 'YYYY-MM-DD'],之后
{
    datetime: [Date(),Date()],
    startTime: '2020-08-12',
    endTime: '2020-08-15',
}
```

##### FormSchema


| 属性 | 类型 | 默认值 | 可选值 | 说明 |
| :-: | :-: | :-: | :-: | :-: |
| field | string | - | - | 字段名 |
| label | string | - | - | 标签名 |
| subLabel | string | - | - | 二级标签名 |
| suffix | string , number , ((values: RenderCallbackParams) => string &vert; number); | - | - | 组件后面的内容 |
| changeEvent | string | - | - | 表单更新事件名称 |
| helpMessage | string , string[] | - | - | 标签名右侧温馨提示 |
| helpComponentProps | HelpComponentProps | - | - | 标签名右侧温馨提示组件 props,见下方 HelpComponentProps |
| labelWidth | string , number | - | - | 覆盖统一设置的 labelWidth |
| disabledLabelWidth | boolean | false | true/false | 禁用 form 全局设置的 labelWidth,自己手动设置 labelCol 和 wrapperCol |
| component | string | - | - | 组件类型，见下方 ComponentType |
| componentProps | any,()=>{} | - | - | 所渲染的组件的 props |
| rules | ValidationRule[] | - | - | 校验规则,见下方 ValidationRule |
| required | boolean | - | - | 简化 rules 配置，为 true 则转化成 [{required:true}]。2.4.0之前的版本只支持string类型的值 |
| rulesMessageJoinLabel | boolean | false | - | 校验信息是否加入 label |
| itemProps | any | - | - | 参考下方 FormItem |
| colProps | ColEx | - | - | 参考上方 actionColOptions |
| defaultValue | object | - | - | 所渲渲染组件的初始值 |
| render | string | (renderCallbackParams: RenderCallbackParams) => VNode / VNode[] / string | - | 自定义渲染组件 |
| renderColContent | (renderCallbackParams: RenderCallbackParams) => VNode / VNode[] / string | - | - | 自定义渲染组件（需要自行包含 formItem） |
| renderComponentContent | (renderCallbackParams: RenderCallbackParams) => any / string | - | - | 自定义渲染组内部的 slot |
| slot | string | - | - | 自定义 slot，渲染组件 |
| colSlot | string | - | - | 自定义 slot，渲染组件 （需要自行包含 formItem） |
| show | boolean / ((renderCallbackParams: RenderCallbackParams) => boolean) | - | - | 动态判断当前组件是否显示，css 控制，不会删除 dom |
| ifShow | boolean / ((renderCallbackParams: RenderCallbackParams) => boolean) | - | - | 动态判断当前组件是否显示，js 控制，会删除 dom |
| dynamicDisabled | boolean / ((renderCallbackParams: RenderCallbackParams) => boolean) | - | - | 动态判断当前组件是否禁用 |
| dynamicRules | boolean / ((renderCallbackParams: RenderCallbackParams) => boolean) | - | - | 动态判返当前组件你校验规则 |

###### RenderCallbackParams
```
export interface RenderCallbackParams {
  schema: FormSchema;
  values: any;
  model: any;
  field: string;
}
```
###### componentProps
1. 当值为对象类型时,该对象将作为component所对应组件的的 props 传入组件
2. 当值为一个函数时候

参数有 4 个
* schema: 表单的整个 schemas
* formActionType: 操作表单的函数。与 useForm 返回的操作函数一致
* formModel: 表单的双向绑定对象，这个值是响应式的。所以可以方便处理很多操作
* tableAction: 操作表格的函数，与 useTable 返回的操作函数一致。注意该参数只在表格内开启搜索表单的时候有值，其余情况为null,

简单例子
```
{
  // 简单例子，值改变的时候操作表格或者修改表单内其他元素的值
  component:'Input',
  componentProps: ({ schema, tableAction, formActionType, formModel }) => {
    return {
      // xxxx props
      onChange:e=>{
        const {reload}=tableAction
        reload()
        // or
        formModel.xxx='123'
      }
    };
  };
}
```

###### HelpComponentProps
```
export interface HelpComponentProps {
  maxWidth: string;
  // 是否显示序号
  showIndex: boolean;
  // 文本列表
  text: any;
  // 颜色
  color: string;
  // 字体大小
  fontSize: string;
  icon: string;
  absolute: boolean;
  // 定位
  position: any;
}
```

###### ComponentType
schema 内组件的可选类型
```
export type ComponentType =
  | 'Input'
  | 'InputGroup'
  | 'InputPassword'
  | 'InputSearch'
  | 'InputTextArea'
  | 'InputNumber'
  | 'InputCountDown'
  | 'Select'
  | 'ApiSelect'
  | 'TreeSelect'
  | 'RadioButtonGroup'
  | 'RadioGroup'
  | 'Checkbox'
  | 'CheckboxGroup'
  | 'AutoComplete'
  | 'Cascader'
  | 'DatePicker'
  | 'MonthPicker'
  | 'RangePicker'
  | 'WeekPicker'
  | 'TimePicker'
  | 'Switch'
  | 'StrengthMeter'
  | 'Upload'
  | 'IconPicker'
  | 'Render'
  | 'Slider'
  | 'Rate'
  | 'Render'
  | 'Divider' (tip:见下面说明)
```

###### Divider
Divider类型用于在schemas中占位，将会渲染成一个分割线（始终占一整行的版面），可以用于较长表单的版面分隔。请只将Divider类型的schema当作一个分割线，而不是一个常规的表单字段。
* Divider仅在showAdvancedButton为false时才会显示（也就是说如果启用了表单收起和展开功能，Divider将不会显示）
* Divider 使用schema中的label以及helpMessage来渲染分割线中的提示内容
* Divider 可以使用componentProps来设置除type之外的props
* Divider 不会渲染AFormItem，因此schema中除label、componentProps、helpMessage、helpComponentProps以外的属性不会被用到

### <font color=red>自行添加需要的组件类型<font>

在 src/components/Form/src/componentMap.ts 内，添加需要的组件，并在上方 ComponentType 添加相应的类型 key

#### 方式 1
这种写法适用与适用频率较高的组件。（全局注册，首次加载就请求）
```
componentMap.set('componentName', 组件);

// ComponentType
export type ComponentType = xxxx | 'componentName';
```
#### 方式 2
使用 useComponentRegister 进行注册 <br>

这种写法只能在当前页使用，页面销毁之后会从 componentMap 删除相应的组件 <br>

方式 2 出现的原因是为了减少打包体积，如果某个组件体积很大，用方式 1 的话可能会使首屏体积增加

```
import { useComponentRegister } from '@/components/form/index';

import { StrengthMeter } from '@/components/strength-meter/index';

useComponentRegister('StrengthMeter', StrengthMeter);

```

### <font color=red>render<font>
自定义渲染内容 <br>

template模式中
```vue
<template>
  <div class="m-4">
    <BasicForm @register="register" @submit="handleSubmit" />
  </div>
</template>
<script lang="ts">
  import { defineComponent, h } from 'vue';
  import { BasicForm, FormSchema, useForm } from '/@/components/Form/index';
  import { useMessage } from '/@/hooks/web/useMessage';
  import { Input } from 'ant-design-vue';
  const schemas: FormSchema[] = [
    {
      field: 'field1',
      component: 'Input',
      label: '字段1',
      colProps: {
        span: 8,
      },
      rules: [{ required: true }],
      render: ({ model, field }) => {
        return h(Input, {
          placeholder: '请输入',
          value: model[field],
          onChange: (e: ChangeEvent) => {
            model[field] = e.target.value;
          },
        });
      },
    },
    {
      field: 'field2',
      component: 'Input',
      label: '字段2',
      colProps: {
        span: 8,
      },
      rules: [{ required: true }],
      renderComponentContent: () => {
        return {
          suffix: () => 'suffix',
        };
      },
    },
  ];
  export default defineComponent({
    components: { BasicForm },
    setup() {
      const { createMessage } = useMessage();
      const [register, { setProps }] = useForm({
        labelWidth: 120,
        schemas,
        actionColOptions: {
          span: 24,
        },
      });
      return {
        register,
        schemas,
        handleSubmit: (values: any) => {
          createMessage.success('click search,values:' + JSON.stringify(values));
        },
        setProps,
      };
    },
  });
</script>
```

tsx模式中
```tsx
import TaskAward from './components/TaskAward/index.vue';

const schemas = (
  isEdit: Ref<boolean>,
  showPeriod: Ref,
  rewardActionType: Ref,
  additional: Ref,
): FormSchema[] => {
  return [
    {
      label: '标签',
      field: 'tags',
      component: 'Select',
      colProps: {
        span: 6,
      },
      componentProps: {
        mode: 'tags',
      },
      required: true,
    },

    {
      label: '奖励信息',
      component: 'Divider',
      field: 'divider-award',
    },

    {
      label: '时长',
      field: 'duration',
      component: 'Render',
      ifShow: additional.value === 1,
      colProps: {
        span: 8,
      },
      required: true,
      suffix: '分钟',
      render:(scope)=> {
        return (
        // tsx代码
            <div>
                <input type='number' />
            </div>
        )
      }
    },

    {
      label: '添加奖励',
      field: 'taskReWard',
      component: 'Render',
      ifShow: rewardActionType.value === 2,
      colProps: {
        span: 24,
      },
      required: true,
      render: (scope: any) => {
        const { field, model } = scope;
        return (
          // 直接使用引入的组件
          <TaskAward
            onChange={(value: any) => {
              const list: any = [];
              value.forEach((item: any) => {
                const { _X_ROW_KEY, ...other } = item;
                list.push(other);
              });
              model[field] = list;
            }}
          />
        );
      },
    },
  ];
};
```

### <font color=red>ifShow/show/dynamicDisabled<font>
自定义显示/禁用
```vue
<template>
  <div class="m-4">
    <BasicForm @register="register" />
  </div>
</template>
<script lang="ts">
  import { defineComponent } from 'vue';
  import { BasicForm, FormSchema, useForm } from '/@/components/Form/index';
  const schemas: FormSchema[] = [
    {
      field: 'field1',
      component: 'Input',
      label: '字段1',
      colProps: {
        span: 8,
      },
      show: ({ values }) => {
        return !!values.field5;
      },
    },
    {
      field: 'field2',
      component: 'Input',
      label: '字段2',
      colProps: {
        span: 8,
      },
      ifShow: ({ values }) => {
        return !!values.field6;
      },
    },
    {
      field: 'field3',
      component: 'DatePicker',
      label: '字段3',
      colProps: {
        span: 8,
      },
      dynamicDisabled: ({ values }) => {
        return !!values.field7;
      },
    },
  ];

  export default defineComponent({
    components: { BasicForm },
    setup() {
      const [register, { setProps }] = useForm({
        labelWidth: 120,
        schemas,
        actionColOptions: {
          span: 24,
        },
      });
      return {
        register,
        schemas,
        setProps,
      };
    },
  });
</script>
```

### <font color=red>ApiSelect<font>
远程下拉加载组件，该组件可以用于学习参考如何自定义组件集成到 Form 组件内，将自定义组件交由 Form 去管理 <br>

#### 用法：
```vue
const schemas: FormSchema[] = [
  {
    field: 'field',
    component: 'ApiSelect',
    label: '字段',
  },
];
```

#### Props

| 属性 | 类型 | 默认值 | 说明 |
| :-: | :-: | :-: | :-: |
| numberToString | boolean | false | 是否将number值转化为string |
| api | ()=>Promise<{ label: string; value: string; disabled?: boolean }[]> | - | 数据接口，接受一个 Promise 对象 |
| params | object | - | 接口参数。此属性改变时会自动重新加载接口数据 |
| resultField | string | - | 接口返回的字段，如果接口返回数组，可以不填。支持x.x.x格式 |
| labelField | string | label | 下拉数组项内label显示文本的字段，支持x.x.x格式 |
| valueField | string | value | 下拉数组项内value实际值的字段，支持x.x.x格式 |
| immediate | boolean | true | 是否立即请求接口，否则将在第一次点击时候触发请求 |

### <font color=red>ApiTreeSelect<font>
远程下拉树加载组件，和ApiSelect类似

#### Props

| 属性 | 类型 | 默认值 | 说明 |
| :-: | :-: | :-: | :-: |
| numberToString | boolean | false | 是否将number值转化为string |
| api | ()=>Promise<{ label: string; value: string; disabled?: boolean }[]> | - | 数据接口，接受一个 Promise 对象 |
| params | object | - | 接口参数。此属性改变时会自动重新加载接口数据 |
| resultField | string | - | 接口返回的字段，如果接口返回数组，可以不填。支持x.x.x格式 |
| immediate | boolean | true | 是否立即请求接口 |

### <font color=red>RadioButtonGroup<font>
Radio Button 风格的选择按钮

#### 用法
```
const schemas: FormSchema[] = [
  {
    field: 'field',
    component: 'RadioButtonGroup',
    label: '字段',
  },
];
```

#### Props
| 属性 | 类型 | 默认值 | 说明 |
| :-: | :-: | :-: | :-: |
| options | { label: string; value: string; disabled?: boolean }[] | - | 数据字段 |
