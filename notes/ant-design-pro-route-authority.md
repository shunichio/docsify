## 动态更新路由

[UmiJS 相关链接](https://umijs.org/docs/runtime-config)

用户登录前会先拉取路由信息，在 `patchRoutes` 中根据服务器下发的路由权限，刷新本地 `src\pages\.umi\core\routes.ts` 中 `routes` 的 `authority` 信息。这样，即使用户强制输入 `path` 进行访问也会被拒。

### 添加 `umi` 运行时配置，在 `src` 下添加 `app.tsx`

```typescriptreact
import pathRegexp from 'path-to-regexp';
import { Route } from '@/models/connect';

let extraRoutes: Route[];

const updateRouteAuthority = (
  path: string,
  routeData: Route[],
  sAuth: string[] | string | undefined,
) => {
  if (routeData && Array.isArray(routeData)) {
    routeData.forEach((route) => {
      if (route.path) {
        if (route.path === '/' || pathRegexp(`${route.path}/(.*)`).test(`${path}/`)) {
          if (route.path === path && sAuth) {
            // eslint-disable-next-line no-param-reassign
            route.authority = sAuth;
          }
          if (route.routes) {
            updateRouteAuthority(path, route.routes, sAuth);
          }
        }
      }
    });
  }
};

const patchEachRoute = (serverRoute: Route[], routes: Route[]) => {
  if (serverRoute && Array.isArray(serverRoute)) {
    serverRoute.forEach((eRoute) => {
      updateRouteAuthority(`${eRoute.path}`, routes, eRoute.authority);
      if (eRoute.routes) {
        patchEachRoute(eRoute.routes, routes);
      }
    });
  }
};

export function patchRoutes({ routes }: { routes: Route[] }) {
  if (extraRoutes) {
    patchEachRoute(extraRoutes, routes);
  }
}

export function render(oldRender: () => void) {
  fetch('/api/user/routes', { method: 'GET' })
    .then((res) => res.json())
    .then((res) => {
      if (res.code === 0) {
        extraRoutes = res.data;
      }
      oldRender();
    });
}
```

### 服务器下发路由格式

```json
{
  "code": 0,
  "data": [
    {
      "path": "/",
      "routes": [
        {
          "path": "/",
          "authority": ["admin", "user"],
          "routes": [
            {
              "path": "/"
            },
            {
              "path": "/welcome"
            },
            {
              "path": "/admin",
              "authority": ["admin", "user"],
              "routes": [
                {
                  "path": "/admin/sub-page",
                  "authority": ["admin", "user"]
                }
              ]
            },
            {
              "path": "/list",
              "authority": ["admin"]
            }
          ]
        }
      ]
    }
  ]
}
```
