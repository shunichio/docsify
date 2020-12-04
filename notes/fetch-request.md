### `Storage` 封装

```javascript
const storage = {
  adapter: window.localStorage,

  get(key) {
    try {
      return JSON.parse(this.adapter.getItem(key));
    } catch (err) {
      return null;
    }
  },
  set(key, value) {
    this.adapter.setItem(key, JSON.stringify(value));
  },
  unset(key) {
    const data = this.get(key);
    this.adapter.removeItem(key);
    return data;
  },
  clear() {
    this.adapter.clear();
  }
};

export default storage;
```

### `fetch` 请求封装

```javascript
import { message } from 'antd'; // 从 ant design pro 引入消息提示组件
import storage from './storage'; // 引入localStorage

// 默认请求参数
const defaultRequestOptions = {
  method: 'GET',
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json'
  }
};

// 请求 baseUrl
const baseURL = '/api';

// 拦截响应头信息
function resolveResponse(response) {
  const contentType = response.headers.get('Content-Type');
  if (contentType && contentType.match(/application\/json/i)) {
    return Promise.all([response.json(), response]);
  }

  return Promise.all([response.text(), response]);
}

// 拦截响应数据
function resolveData([result, response]) {
  if (result.code !== '0') {
    const error = new Error(result.msg || result.error);
    error.code = result.code;
    error.response = response;
    throw error;
  }
  return result;
}

// 错误处理
function handleError(error) {
  const { response, code } = error;
  if (!response) {
    error = new Error('网络连接失败，请检查网络');
  }
  message.destroy();
  message.error(error.message || response.statusText, 1.2);
  if (code === '401') {
    // defer to ensure next steps working
    setTimeout(() => {
      // clear stored data
      storage.clear();
      window.location.reload();
    }, 500);
  }

  throw error;
}

export default function request(url, opts) {
  const options = { ...defaultRequestOptions, ...opts };
  options.headers.TOKEN = storage.get('token');
  return fetch(baseURL + url, options)
    .then(resolveResponse)
    .then(resolveData)
    .catch(handleError);
}
```

### `fetch` 请求使用

```javascript
import request from '../utils/request'; // 引入封装好的请求
import { message } from 'antd'; // 从 ant design pro 引入消息提示组件

export function fetchUsers(path, guid, key, pageNum, pageSize) {
  return request('/dataService/doUser/getUserResInfo', {
    method: 'POST',
    body: JSON.stringify({
      guid,
      addressPath: path,
      userName: key,
      pageIndex: pageNum,
      pageSize: pageSize
    })
  }).then(res => res, err => ({ res: [], size: 0 }));
}
```
