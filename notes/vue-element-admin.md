### 对```axios```的封装

**1、 创建 ```request.js``` 文件**
```javascript
import axios from 'axios'
import store from '@/store'
import { Message, MessageBox } from 'element-ui'
import { getToken } from '@/utils/auth'

// 创建一个axios实例
const request = axios.create({
  baseURL: process.env.BASE_API, // 定义api的baseUrl
  timeout: 5000 // 定义请求超时时间
  // withCredentials: true, // send cookies when cross-domain requests
})

// 添加请求拦截器
request.interceptors.request.use(
  config => {
    // 在发送请求之前做些什么
    if (store.getters.token) {
      config.headers['X-Token'] = getToken() // 让每个请求都携带“token”，“X-Token”代表为“headers”对象自定义的key，可以根据实际情况自行修改
    }
    return config
  },
  error => {
    // 对请求错误做些什么
    console.log(error)
    Promise.reject(error)
  }
)

// 添加响应拦截器
request.interceptors.response.use(

  // 如果你想得到头部信息或者状态信息，请返回 'response'
  // response => response,

  //  可以通过response.data中的自定义code来标识请求状态
  //  也可通过xmlhttprequest状态码来进行判断，逻辑可写在error函数中
  response => {
    const result = response.data
    if (result.code !== 20000) {
      Message({
        message: result.message || '出现错误，请联系管理员',
        type: 'error',
        duration: 5 * 1000
      })
  
      if (result.code === 50008 || result.code === 50012 || result.code === 50014) {  // 50008:非法的token; 50012:其他客户端登录了;  50014:Token 过期了;
        MessageBox.confirm('请求错误，你可以取消继续留在该页面，或者重新登录', '确定登出', {
          confirmButtonText: '重新登录',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          store.dispatch('user/resetToken').then(() => {
            location.reload() // 重新实例化vue-router对象，避免bug
          })
        })
      }
      return Promise.reject(new Error(result.message) || '出现错误，请联系管理员')
    } else {
      return result
    }
  },
  error => {
    console.log('err' + error)
    Message({
      message: error.message,
      type: 'error',
      duration: 5 * 1000
    })
    return Promise.reject(error)
  }
)

export default request
```

**2、使用封装好的 ```axios``` 请求**
```javascript
import request from '@/***/request'

export function getInfo(params) {
  return request({
    url: '/user/info',
    method: 'get',
    params
  })
}
```

>在第一段代码中，我们对 **数据请求** 与 **数据返回** 都进行了拦截，并塞入一定的自定义配置。配置如果成功，便直接返回。如果不成功，利用 `Promise.reject()` 返回错误，抛出异常。所以，拦截后的请求本身返回的就是一个 `Promise` 对象。因此，在第二段代码中，我们直接返回即可，无需再封装一个 `Promise` 对象。

在这个例子中，每一个请求都是要带 ```token``` 来验证权限的，这样封装一下的话我们就不用每个请求都手动来塞 ```token```，或者来做一些统一的异常处理，一劳永逸。而且因为我们的 ```api``` 是根据 ```env``` 环境变量动态切换的，如果以后线上出现了bug，我们只需配置一下 ```@/config/dev.env.js```的 ```api``` 地址，再重启一下服务，就能在本地模拟线上的环境了。

**3、配置 ```config```文件夹中的 ```dev.env.js``` 和 ```prod.env.js```文件**
```javascript
module.exports = {
  NODE_ENV: '"development"',
  ENV_CONFIG: '"dev"',
  BASE_API: '"localhost:8080/api"'
}
```

```javascript
module.exports = {
  NODE_ENV: '"production"',
  ENV_CONFIG: '"prod"',
  BASE_API: '"https://api.xxx.com"'
}
```


### 登录权限验证

在一个空白页面上写好两个 ```input``` 的框，一个是登录账号，一个是登录密码，再放置一个登录按钮。我们将登录按钮上绑上click事件，点击登录之后向服务端提交账号和密码进行验证。登录成功后，服务端会返回一个 ```token```（该 ```token``` 的是一个能唯一标示用户身份的一个 ```key``` ），之后我们将 ```token``` 存储在本地 ```cookie``` 之中，这样下次打开页面或者刷新页面的时候能记住用户的登录状态，不用再去登录页面重新登录了。并将 ```token``` 放在了 ```vuex``` 状态管理器中。

**1、click事件触发登录操作，提交```action```:**
```javascript
this.$store.dispatch('LoginByUsername', this.loginForm).then(() => {
  //登录成功之后重定向到首页
  this.$router.push({ path: '/' }); 
}).catch(err => {
  //登录失败提示错误
  this.$message.error(err); 
});
```

**2、action操作**
```javascript
LoginByUsername({ commit }, userInfo) {
  const username = userInfo.username.trim()
  return new Promise((resolve, reject) => {
    loginByUsername(username, userInfo.password).then(response => {
      const data = response.data
      Cookies.set('Token', response.data.token) //登录成功后将token存储在cookie之中
      commit('SET_TOKEN', data.token)
      resolve()
    }).catch(error => {
      reject(error)
    });
  });
}
```
为了保证安全性，后台所有 ```token``` 有效期( ```Expires/Max-Age``` )都是 ```Session``` ，就是当浏览器关闭了就丢失了。重新打开游览器都需要重新登录验证，后端也会在每周固定一个时间点重新刷新 ```token``` ，让后台用户全部重新登录一次，确保后台用户不会因为电脑遗失或者其它原因被人随意使用账号。

用户登录成功之后，在全局钩子 ```router.beforeEach``` 中拦截路由，判断是否已获得 ```token``` ，在获得 ```token``` 之后我们就要去获取用户的基本信息了

**3、路由导航守卫**
```javascript
// 每当进入一个新页面之前，都会先从 cookie 中查看是否存有 token，如果 token 不存在，就重新登录，如果有 token ,就会把这个 token 返给后端去拉取用户信息，保证用户信息是最新的。
router.beforeEach((to, from, next) => {
  // 如果Cookies里面包含Token信息
  if (getToken()) {
    // 如果即将前往登录页面
    if (to.path === '/login') {
      // 登录页面不用获取用户信息，直接再跳转到首页，进而获取用户信息
      next({ path: '/' })
    } else {  // 如果即将前往的页面不是登录页面
      if (store.getters.roles.length === 0) { // 如果vuex状态管理器里没有当前用户的用户信息
        // 提交action，获取用户信息
        store.dispatch('GetUserInfo').then(res => {
            const roles = res.data.roles	// 注意：roles 必须是一个数组，例如: ['editor','develop']
            store.dispatch('GenerateRoutes', { roles }).then(() => {	// GenerateRoutes方法详见下一节：路由设置
              // 根据roles权限生成可访问的路由表
              router.addRoutes(store.getters.addRouters) // 动态添加可访问路由表
              next({ ...to, replace: true }) // 确保addRoutes已完成
            })
          }).catch(err => { // 获取用户信息失败
            store.dispatch('FedLogOut').then(() => { // 清空Cookies及vuex状态管理器里的token
              Message.error(err || 'Verification failed, please login again')
              next({ path: '/' })
            })
          })
      } else { // 如果vuex状态管理器里存有当前用户的用户信息，直接跳转使用存储的信息
        next()
      }
    }
  } else {  // 如果本地Cookies不存在token
    
    // 在免登录白名单，直接进入
    if (whiteList.indexOf(to.path) !== -1) {
      next()
    } else {
      next('/login') // 否则全部重定向到登录页
    }
  }
})

```
**ps: ```vuex``` 状态管理器中 ```token``` 的值从 ```cookie``` 中获取**

### 路由设置

根据使用环境的差异，采用不同的方式加载组件，加载方式写成两个不同的的文件，在路由中引入即可
**开发环境文件： ```_import_development.js```**
```javascript
module.exports = file => require('@/views/' + file + '.vue').default 	// vue-loader at least v13.0.0+
```
**生产环境文件： ```_import_production.js```**
```javascript
module.exports = file => () => import('@/views/' + file + '.vue')
```
顶部，侧边栏，底部都写入 ```Layout``` 组件中，然后在不同的Url路径中都引用这一组件，可以有效解决Url冗余的问题，当左边栏目某一项的子项目数量为 ```"1"```时，可将当前路由的子路由路径写成 **空路由** ，减少Url冗余。
```javascript
// 根据使用环境的差异，采用不同的方式加载组件，引入不同的文件
const _import = require('./_import_' + process.env.NODE_ENV)

// 所有权限通用路由表 
// 如首页和登录页和一些不用权限的公用页面
export const constantRouterMap = [
  {
    path: '/',
    redirect: '/dashboard'
  },
  {
    path: '/login',
    component: _import('login/index')
  },
  {
    path: '/authredirect',
    component: _import('login/authredirect')
  },
  {
    path: '/404',
    component: _import('errorPage/404')
  },
  {
    path: '/401',
    component: _import('errorPage/401')
  },
  {
    path: '/dashboard',
    component: Layout,
    children: [
      {
        path: '',
        component: _import('dashboard/index'),
        name: 'dashboard',
        meta: { title: 'dashboard', icon: 'dashboard', noCache: true }
      }
    ]
  },
  {
    path: '/documentation',
    component: Layout,
    redirect: '/documentation/index',
    children: [
      {
        path: 'index',
        component: _import('documentation/index'),
        name: 'documentation',
        meta: { title: 'documentation', icon: 'documentation', noCache: true }
      }
    ]
  }
]

// 实例化vue的时候只挂载constantRouter
export default new Router({
  routes: constantRouterMap
})

// 异步挂载的路由
// 根据用户权限动态加载的路由表 
export const asyncRouterMap = [
  {
    path: '/permission',
    component: Layout,
    redirect: '/permission/page',
    meta: {
      title: 'permission',
      icon: 'lock',
      roles: ['admin', 'editor'] // you can set roles in root nav
    },
    children: [
      {
        path: 'page',
        component: _import('permission/page'),
        name: 'pagePermission',
        meta: {
          title: 'pagePermission',
          roles: ['admin']        // or you can only set roles in sub nav
        }
      },
      {
        path: 'directive',
        component: _import('permission/directive'),
        name: 'directivePermission',
        meta: {
          title: 'directivePermission'
          // if do not set roles, means: this page does not require permission
        }
      }
    ]
  },
  { path: '*', redirect: '/404', hidden: true }
]
```

这里我们根据 ```vue-router``` 官方推荐的方法通过 ```meta``` 标签来标示改页面能访问的权限有哪些。如 ```meta: { role: ['admin','super_editor'] }``` 表示该页面只有 ```admin``` 和超级编辑```super_editor```才能有资格进入。

还记得吗，我们在前面提交了一个名为 ```GenerateRoutes``` 的 ```action```,我们将权限作为```vuex```状态管理器的一个模块（ ```module``` ）,写在 ```permisssion.js``` 文件中。
```javascript
import { asyncRouterMap, constantRouterMap } from '@/router'

/**
 * 通过meta.role判断是否与当前用户权限匹配
 * @param roles
 * @param route
 */
function hasPermission(roles, route) {
  if (route.meta && route.meta.roles) {
    return roles.some(role => route.meta.roles.indexOf(role) >= 0)
  } else {
    return true
  }
}

/**
 * 递归过滤异步路由表，返回符合用户角色权限的路由表
 * @param asyncRouterMap
 * @param roles
 */
function filterAsyncRouter(asyncRouterMap, roles) {
  const accessedRouters = asyncRouterMap.filter(route => {
    if (hasPermission(roles, route)) {
      if (route.children && route.children.length) {
        route.children = filterAsyncRouter(route.children, roles)
      }
      return true
    }
    return false
  })
  return accessedRouters
}

const permission = {
  state: {
    routers: constantRouterMap,
    addRouters: []
  },
  mutations: {
    SET_ROUTERS: (state, routers) => {
      state.addRouters = routers
      state.routers = constantRouterMap.concat(routers)
    }
  },
  actions: {
    GenerateRoutes({ commit }, data) {
      return new Promise(resolve => {
        const { roles } = data
        let accessedRouters
        if (roles.indexOf('admin') >= 0) {
          accessedRouters = asyncRouterMap
        } else {
          accessedRouters = filterAsyncRouter(asyncRouterMap, roles)
        }
        commit('SET_ROUTERS', accessedRouters)
        resolve()
      })
    }
  }
}

export default permission
```
**注意事项：** 这里有一个需要非常注意的地方就是 ```404``` 页面一定要最后加载，如果在constantRouterMap声明了 ```404```，```asyncRouterMap``` 中 ```*``` 后面的所以页面都会被拦截到404