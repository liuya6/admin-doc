### 路由
项目路由配置存放于 `src/router/routes` 下面。 `src/router/routes/modules`用于存放路由模块，在该目录下的文件会自动注册。

###### 模块说明
在 `src/router/routes/modules` 内的 .ts 文件会被视为一个路由模块。

一个路由模块包含以下结构
```
import type { AppRouteModule } from '/@/router/types';

import { LAYOUT } from '/@/router/constant';
import { t } from '/@/hooks/web/useI18n';

const dashboard: AppRouteModule = {
  path: '/dashboard',
  name: 'Dashboard',
  component: LAYOUT,
  redirect: '/dashboard/analysis',
  meta: {
    icon: 'ion:grid-outline',
    title: t('routes.dashboard.dashboard'),
  },
  children: [
    {
      path: 'analysis',
      name: 'Analysis',
      component: () => import('/@/views/dashboard/analysis/index.vue'),
      meta: {
        affix: true,
        title: t('routes.dashboard.analysis'),
      },
    },
    {
      path: 'workbench',
      name: 'Workbench',
      component: () => import('/@/views/dashboard/workbench/index.vue'),
      meta: {
        title: t('routes.dashboard.workbench'),
      },
    },
  ],
};
export default dashboard;
```

### 多级路由
> 注意事项
> 1. 整个项目所有路由 name 不能重复
> 2. 所有的多级路由最终都会转成二级路由，所以不能内嵌子路由
> 3. 除了 layout 对应的 path 前面需要加 /，其余子路由都不要以/开头

示例
```
import type { AppRouteModule } from '/@/router/types';
import { getParentLayout, LAYOUT } from '/@/router/constant';
import { t } from '/@/hooks/web/useI18n';
const permission: AppRouteModule = {
  path: '/level',
  name: 'Level',
  component: LAYOUT,
  redirect: '/level/menu1/menu1-1/menu1-1-1',
  meta: {
    icon: 'ion:menu-outline',
    title: t('routes.demo.level.level'),
  },

  children: [
    {
      path: 'tabs/:id', 
      name: 'TabsParams',
      component: getParentLayout('TabsParams'),
      meta: {
        carryParam: true,
        hidePathForChildren: true, // 本级path将会在子级菜单中合成完整path时会忽略这一层级
      },
      children: [
        path: 'tabs/id1', // 其上级有标记hidePathForChildren，所以本级在生成菜单时最终的path为  /level/tabs/id1
        name: 'TabsParams',
        component: getParentLayout('TabsParams'),
        meta: {
          carryParam: true,
          ignoreRoute: true,  // 本路由仅用于菜单生成，不会在实际的路由表中出现
        },
      ]
    },
    {
      path: 'menu1',
      name: 'Menu1Demo',
      component: getParentLayout('Menu1Demo'),
      meta: {
        title: 'Menu1',
      },
      redirect: '/level/menu1/menu1-1/menu1-1-1',
      children: [
        {
          path: 'menu1-1',
          name: 'Menu11Demo',
          component: getParentLayout('Menu11Demo'),
          meta: {
            title: 'Menu1-1',
          },
          redirect: '/level/menu1/menu1-1/menu1-1-1',
          children: [
            {
              path: 'menu1-1-1',
              name: 'Menu111Demo',
              component: () => import('/@/views/demo/level/Menu111.vue'),
              meta: {
                title: 'Menu111',
              },
            },
          ],
        },
      ],
    },
  ],
};

export default permission;
```

### 外部页面嵌套
只需要将 `frameSrc` 设置为需要跳转的地址即可
```
const IFrame = () => import('/@/views/sys/iframe/FrameBlank.vue');
{
  path: 'doc',
  name: 'Doc',
  component: IFrame,
  meta: {
    frameSrc: 'https://vvbin.cn/doc-next/',
    title: t('routes.demo.iframe.doc'),
  },
}
```

### 外链
只需要将 `path` 设置为需要跳转的**HTTP地址**即可
```
{
  path: 'https://vvbin.cn/doc-next/',
  name: 'DocExternal',
  component: IFrame,
  meta: {
    title: t('routes.demo.iframe.docExternal'),
  },
}
```

### 新增路由

在 `src/router/routes/modules` 内新增一个模块文件。
示例，新增 test.ts 文件
```
import type { AppRouteModule } from '/@/router/types';
import { LAYOUT } from '/@/router/constant';
import { t } from '/@/hooks/web/useI18n';

const dashboard: AppRouteModule = {
  path: '/about',
  name: 'About',
  component: LAYOUT,
  redirect: '/about/index',
  meta: {
    icon: 'simple-icons:about-dot-me',
    title: t('routes.dashboard.about'),
  },
  children: [
    {
      path: 'index',
      name: 'AboutPage',
      component: () => import('/@/views/sys/about/index.vue'),
      meta: {
        title: t('routes.dashboard.about'),
        icon: 'simple-icons:about-dot-me',
      },
    },
  ],
};

export default dashboard;
```
此时路由已添加完成，不需要手动引入，放在`src/router/routes/modules` 内的文件会自动被加载。

### 路由刷新
采用的是**重定向**方式<br/>
实现:
```
import { useRedo } from '/@/hooks/web/usePage';
import { defineComponent } from 'vue';
export default defineComponent({
  setup() {
    const redo = useRedo();
    // 执行刷新
    redo();
    return {};
  },
});
```

### Redirect
查看`src/views/sys/redirect/index.vue`
```
import { defineComponent, unref } from 'vue';
import { useRouter } from 'vue-router';
export default defineComponent({
  name: 'Redirect',
  setup() {
    const { currentRoute, replace } = useRouter();
    const { params, query } = unref(currentRoute);
    const { path } = params;
    const _path = Array.isArray(path) ? path.join('/') : path;
    replace({
      path: '/' + _path,
      query,
    });
    return {};
  },
});
```

### 页面跳转
页面跳转建议采用项目提供的 useGo
示例：
```
import { useGo } from '/@/hooks/web/usePage';
import { defineComponent } from 'vue';
export default defineComponent({
  setup() {
    const go = useGo();

    // 执行刷新
    go();
    go(PageEnum.Home);
    return {};
  },
});
```

### 如何开启页面缓存

开启缓存有 3 个条件
1. 在 `src/settings/projectSetting.ts` 内将`openKeepAlive` 设置为 `true`
2. 路由设置 `name`，且不能重复
3. 路由对应的组件加上 `name`，与路由设置的 `name` 保持一致

```
{
   ...,
    // name
    name: 'Login',
    // 对应组件组件的name
    component: () => import('/@/views/sys/login/index.vue'),
    ...
},

// /@/views/sys/login/index.vue
export default defineComponent({
    // 需要和路由的name一致
    name:"Login"
});

```
> **注意**<br>
> keep-alive 生效的前提是：需要将路由的 name 属性及对应的页面的 name 设置成一样。因为：<br>
> **include - 字符串或正则表达式，只有名称匹配的组件会被缓存**

### 如何更改首页路由
首页路由指的是应用程序中的默认路由，当不输入其他任何路由时，会自动重定向到该路由下，并且该路由在Tab上是固定的，即使设置affix: false也不允许关闭

例：首页路由配置的是`/dashboard/analysis`，那么当直接访问 `http://localhost:3100/` 会自动跳转到`http://localhost:3100/#/dashboard/analysis` 上(用户已登录的情况下)

可以将`pageEnum.ts`中的`BASE_HOME`更改为需要你想设置的首页即可
```
export enum PageEnum {
    // 更改此处即可
    BASE_HOME = '/dashboard',
}
```
