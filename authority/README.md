### 权限（后台动态获取）
原理：通过接口动态生成路由表，且遵循一定的数据结构返回。前端根据需要处理该数据为可识别的结构，再通过 router.addRoutes 添加到路由实例，实现权限的动态生成。

### 实现
1. 在项目配置将系统内权限模式改为 BACK 模式
```
// ! 改动后需要清空浏览器缓存
const setting: ProjectConfig = {
  // 权限模式
  permissionMode: PermissionModeEnum.BACK,
};
```
2. 路由拦截，与角色权限模式一致
`permissionStore.buildRoutesAction` 用于过滤动态路由，详细代码见 `/@/store/modules/permission.ts`
```
// 主要代码
if (permissionMode === PermissionModeEnum.BACK) {
  const { createMessage } = useMessage();

  createMessage.loading({
    content: t('sys.app.menuLoading'),
    duration: 1,
  });

  // !Simulate to obtain permission codes from the background,
  // this function may only need to be executed once, and the actual project can be put at the right time by itself
  let routeList: AppRouteRecordRaw[] = [];
  try {
    this.changePermissionCode();
    routeList = (await getMenuList()) as AppRouteRecordRaw[];
  } catch (error) {
    console.error(error);
  }

  // Dynamically introduce components
  routeList = transformObjToRoute(routeList);

  //  Background routing to menu structure
  const backMenuList = transformRouteToMenu(routeList);
  this.setBackMenuList(backMenuList);

  routeList = flatMultiLevelRoutes(routeList);
  routes = [PAGE_NOT_FOUND_ROUTE, ...routeList];
}
```

###### getMenuList 返回值格式
返回值由多个路由模块组成
```
[
  {
    path: '/dashboard',
    name: 'Dashboard',
    component: '/dashboard/welcome/index',
    meta: {
      title: 'routes.dashboard.welcome',
      affix: true,
      icon: 'ant-design:home-outlined',
    },
  },
  {
    path: '/permission',
    name: 'Permission',
    component: 'LAYOUT',
    redirect: '/permission/front/page',
    meta: {
      icon: 'carbon:user-role',
      title: 'routes.demo.permission.permission',
    },
    children: [
      {
        path: 'back',
        name: 'PermissionBackDemo',
        meta: {
          title: 'routes.demo.permission.back',
        },

        children: [
          {
            path: 'page',
            name: 'BackAuthPage',
            component: '/demo/permission/back/index',
            meta: {
              title: 'routes.demo.permission.backPage',
            },
          },
          {
            path: 'btn',
            name: 'BackAuthBtn',
            component: '/demo/permission/back/Btn',
            meta: {
              title: 'routes.demo.permission.backBtn',
            },
          },
        ],
      },
    ],
  },
]
```

### 动态更换菜单
使用项目中的 `usePermission` 方便角色相关操作
```
import { usePermission } from '/@/hooks/web/usePermission';
import { RoleEnum } from '/@/enums/roleEnum';

export default defineComponent({
  setup() {
    const { changeMenu } = usePermission();

    // 更改菜单的实现需要自行去修改
    changeMenu();
    return {};
  },
});
```

### 细粒度权限

###### 函数方式
usePermission 提供了按钮级别的权限控制。<br>
示例：
```
<template>
  <a-button v-if="hasPermission(['20000', '2000010'])" color="error" class="mx-4">
    拥有[20000,2000010]code可见
  </a-button>
</template>
<script lang="ts">
  import { usePermission } from '/@/hooks/web/usePermission';
  import { RoleEnum } from '/@/enums/roleEnum';

  export default defineComponent({
    setup() {
      const { hasPermission } = usePermission();
      return { hasPermission };
    },
  });
</script>
```
###### 组件方式
使用项目中 `src/components/Authority` <br>
示例：
```
<template>
  <div>
    <Authority :value="RoleEnum.ADMIN">
      <a-button type="primary" block> 只有admin角色可见 </a-button>
    </Authority>
  </div>
</template>
<script>
  import Authority from '/@/components/Authority';
  import { defineComponent } from 'vue';
  export default defineComponent({
    components: { Authority },
  });
</script>
```

###### 指令方式
示例：
```
<a-button v-auth="'1000'" type="primary" class="mx-4"> 拥有code ['1000']权限可见 </a-button>
```

### 如何初始化 code
通常，如需做按钮级别权限，后台会提供相应的 code，或者类型的判断标识。这些编码只需要在登录后获取一次即可。
```
import { getPermCodeByUserId } from '/@/api/sys/user';
import { permissionStore } from '/@/store/modules/permission';
async function changePermissionCode(userId: string) {
  // 从后台获取当前用户拥有的编码
  const codeList = await getPermCodeByUserId({ userId });
  permissionStore.commitPermCodeListState(codeList);
}
```
