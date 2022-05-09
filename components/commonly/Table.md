### Table

项目的table组件是二次封装的[vxe-table](https://vxetable.cn/v4/)，使用之前可以先去看看[vxe-table](https://vxetable.cn/v4/)的官方文档。

### 完整示例

```
  import { MTable } from '/@/components/MTable';
  import { useTable } from '/@/hooks/component/useTable';

  let { params, total, tableLoading, tableData, getTableData, onChange, onShowSizeChange } = useTable(initData);
  const { getTableSum } = useTableSum();

  // 表头数据
  const tableTitle = ()=>{
    return [
        {
            title: '基础信息',
            // field: 'name',
            // headerTips: '测试headerTip',
            children: [
              {
                title: '姓名',
                field: 'name',
                headerTips: 'cs',
              },
              {
                title: '地址',
                field: 'address',
                headerTips: '2222',
              },
              {
                title: 'number',
                field: 'no',
                headerTips: '2222',
              },
            ],
        },
        {
            title: 'beginTime',
            field: 'beginTime',
            headerTips: ['测试headerTip', '测试headerTip2', '测试headerTip3'],
        },
        {
            title: 'endTime',
            field: 'endTime',
            headerTips: () => {
              return ['endTime', 'endTime222'];
            },
        },
        {
            title: '状态',
            field: 'status',
        },
    ];
  }

  // table
    <MTable
      loading={unref(tableLoading)}
      page={params.page}
      limit={params.limit}
      total={unref(total)}
      data={unref(tableData)}
      tableTitle={tableTitle()}
      refresh={initData}
      onChange={onChange}
      onShowSizeChange={onShowSizeChange}
      showFooter
      footerMethod={getTableSum}
    />
  
    // 数据请求
    function initData() {
        return getTableData(async () => {
            const data = await getDemoTableData(params);
            console.log(data, 'data');
            tableData.value = data.result.list;
            total.value = data.result.total;
        });
    }
```

### Props

| 属性 | 类型 | 默认值 | 可选值 | 说明 |
| -- | -- | -- | -- | -- |
| loading | boolean | false | true/false | 表格请求loading效果 |
| page | number | 1 | - | 表格分页当前页码 |
| limit | number | 20 | - | 表格分页每页条数 |
| total | number | 100 | - | 表格分页数据总数 |
| data | any[] | - | - | 表格数据 |
| tableTitle | any[] | - | - | 表头数据 |
| showFooter | boolean | false | true/false | 是否显示合计列 |
| showToolbar | boolean | true | true/false | 是否表格头部工具栏（工具栏包含了刷新/分页等功能） |

### Methods
| 事件名 | 类型 | 说明 |
| -- | -- | -- |
| refresh | Function | 表格刷新 |
| onChange | Function | 分页页码改变触发 |
| onShowSizeChange | Function | 分页每页显示条数改变触发 |
| footerMethod | Function | 表格合计方法回调 |


### useTable

useTable主要是配合table一起使用，封装了很多公共方法。

主要代码：
``` 
export function useTable(initData?: () => void) {
  const formElRef = ref<Nullable<FormActionType>>(null);
  const route = useRoute();

  let params = reactive<ParamsDefinition>({
    pageNum: 1,
    pageSize: 30,
  });

  let notPageParams = reactive<Partial<ParamsDefinition>>({});

  const config = reactive({
    total: 0,
    tableData: [] as any,
    tableLoading: false,
    selectedList: [] as any,
  });

  // 分页操作
  function onShowSizeChange(_: number, limit: number) {
    params.pageSize = limit;
    if (initData) {
      initData();
    }
  }
  function onChange(page: number, _: number) {
    params.pageNum = page;
    if (initData) {
      initData();
    }
  }

  // 查询操作
  function handleSubmit(data: any) {
    params = Object.assign(params, data);
    notPageParams = Object.assign(notPageParams, data);
    if (initData) {
      initData();
    }
  }

  // 表格复选框操作
  function selectAllEvent({ checked, records }) {
    if (checked) {
      config.selectedList = unionBy(config.selectedList, records, 'id');
    } else {
      const defaultTableData = cloneDeep(config.tableData);
      config.selectedList = xorBy(config.selectedList, defaultTableData, 'id');
    }
  }
  function selectChangeEvent({ checked, records, _, row }) {
    if (checked) {
      config.selectedList = unionBy(config.selectedList, records, 'id');
    } else {
      config.selectedList = xorBy(config.selectedList, [row], 'id');
    }
  }

  // 获取表格数据
  async function getTableData(fn: () => void) {
    try {
      config.tableLoading = true;
      await fn();
      config.tableLoading = false;
    } catch (e) {
      console.warn(e);
    }
  }

  return {
    id: route.name,
    params,
    notPageParams,
    ...toRefs(config),
    formElRef,
    onShowSizeChange,
    onChange,
    handleSubmit,
    getTableData,
    selectAllEvent,
    selectChangeEvent,
  };
}
```

###### 用法

```
    import { useTable } from '/@/hooks/component/useTable';

    let { params, total, tableLoading, tableData, getTableData, onChange, onShowSizeChange } = useTable(initData);

    <MTable
      loading={unref(tableLoading)}
      page={params.page}
      limit={params.limit}
      total={unref(total)}
      data={unref(tableData)}
      tableTitle={tableTitle()}
      refresh={initData}
      onChange={onChange}
      onShowSizeChange={onShowSizeChange}
      showFooter
      footerMethod={getTableSum}
    />
```


