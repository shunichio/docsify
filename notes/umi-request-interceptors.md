## Umi Request 请求拦截

```typescript
/**
 * request 网络请求工具
 * 更详细的 api 文档: https://github.com/umijs/umi-request
 */
import { extend } from 'umi-request'
import { notification } from 'antd'
import { stringify } from 'querystring'

import {
  LIGHT_SYSTEM_USER_LOGIN_URL,
  LIGHT_SYSTEM_USER_TOKEN,
} from '@/globalData'

const isDev = process.env.NODE_ENV === 'development'

const baseUrl = 'https://myapi.com'

const ERROR_MESSAGE: string = '发生自定义错误'

const codeMessage = {
  200: '服务器成功返回请求的数据。',
  201: '新建或修改数据成功。',
  202: '一个请求已经进入后台排队（异步任务）。',
  204: '删除数据成功。',
  400: '发出的请求有错误，服务器没有进行新建或修改数据的操作。',
  401: '用户没有权限（令牌、用户名、密码错误）。',
  404: '发出的请求针对的是不存在的记录，服务器没有进行操作。',
  403: '用户得到授权，但是访问是被禁止的。',
  406: '请求的格式不可得。',
  410: '请求的资源被永久删除，且不会再得到的。',
  422: '当创建一个对象时，发生一个验证错误。',
  500: '服务器发生错误，请检查服务器。',
  502: '网关错误。',
  503: '服务不可用，服务器暂时过载或维护。',
  504: '网关超时。',
}

/**
 * 异常处理程序
 * 处理程序如果 return，则将值作为返回。
 * 'return;' 相当于 return undefined, 在处理结果时判断 response 是否有值即可。
 * 这时相当于 'resolve( undefined || { key: value} )'，
 * 如果没有 return ， 正确则表现为 'resolve( {key: value} )'， 错误则表现为 'reject( error )'
 */
const errorHandler = async (error: any) => {
  if (error.message === ERROR_MESSAGE) {
    return
  }
  const { response } = error
  if (response && response.status) {
    const errorText = codeMessage[response.status] || response.statusText
    const { status, url } = response
    notification.error({
      message: `请求错误 ${status}: ${url}`,
      description: errorText,
    })
  } else if (!response) {
    notification.error({
      description: '您的网络发生异常，无法连接服务器',
      message: '网络异常',
    })
  }
  // eslint-disable-next-line no-useless-return
  return
}

/**
 * 配置request请求时的默认参数
 */
const request = extend({
  errorHandler, // 默认错误处理
  credentials: 'include', // 默认请求是否带上cookie
})

/**
 * request 拦截
 */
request.interceptors.request.use((url, options) => {
  return {
    url,
    options: {
      ...options,
      headers: {
        ...options.headers,
        Authorization: getToken(), // 发送请求时，头部携带用户验证信息
      },
    },
  }
})

/**
 * response 拦截
 */
request.interceptors.response.use(async (response) => {
  const { status } = response
  if (status === 401) {
    localStorage.clear()
    const queryString = stringify({
      redirect: window.location.href,
    })
    window.location.href = `/user/login?${queryString}`
  }
  try {
    const data = await response.clone().json()
    if (data.code === 1) {
      // 对自定义的 code 进行判断
      notification.error({
        message: '请求错误',
        description: data.msg,
      })

      return Promise.reject(new Error(ERROR_MESSAGE))
    }
  } catch (error) {
    console.log('error', error)
  }
  return response
})

export default request
```

## 注意

> response 拦截器会先于异常处理程序对返回值进行处理与判断
