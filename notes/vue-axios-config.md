## axios 拦截相同的请求

```javascript
import axios from 'axios';

let requestPending = []; // 声明一个数组，用于存储每个 http request 请求的取消函数和标识
let cancelToken = axios.CancelToken;
let removePending = ever => {
  for (let p in pending) {
    if (pending[p].u === ever.url + '&' + ever.method) {
      // 当前 http request 请求在数组中存在时，执行下面的函数逻辑
      pending[p].f(); // 取消 http request操作
      pending.splice(p, 1); // 把这条 http request 记录从数组中移除
    }
  }
};

const service = axios.create({
  baseURL: '',
  timeout: 5000
});

service.interceptors.request.use(
  config => {
    // ------------------------------------------------------------------------------------
    removePending(config); //在 http request 发送前执行一下取消操作
    config.cancelToken = new cancelToken(c => {
      // http request 标识使用请求地址&请求方式拼接的字符串，也可以选择其他的一些方式
      // 同一个页面，相同的请求不能同时多次调用，否则前面的请求会被取消（cancel）
      requestPending.push({ u: config.url + '&' + config.method, f: c });
    });
    // -----------------------------------------------------------------------------------------

    return config;
  },
  error => {
    console.log(error);
    return Promise.reject(error);
  }
);

service.interceptors.response.use(
  response => {
    // ------------------------------------------------------------------------------------------
    removePending(response.config); //在一个 http request 响应后再执行一下取消操作，把已经完成的请求从pending中移除
    // -------------------------------------------------------------------------------------------
  },
  error => {
    Message({
      message: error.message,
      type: 'error',
      duration: 5 * 1000
    });
    return Promise.reject(error);
  }
);

export default service;
```
