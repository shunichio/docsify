#### 微信请求数据的方法

我们可以利用微信提供给我们的 ```API``` 直接向后台请求数据，而无需处理跨域等操作。

```javascript
wx.request({
  url: 'test.php', //仅为示例，并非真实的接口地址
  data: {
    x: '',
    y: ''
  },
  header: {
    'content-type': 'application/json' // 默认值
  },
  success: function(res) {
    console.log(res.data)
  }
})
```

#### 数据请求方法封装

将数据请求方法`Promise`化，在相应的```JS```文件中进行封装。

```javascript
//请求方法
const request = (url, data, method, resolve, reject) => {
  return wx.request({
    url: url,
    data: data,
    method: method,
    success: resolve,
    fail: reject
  })
}

export const post = (url, data) => {
  return new Promise((resolve, reject) => {
    request(url, data, 'POST', resolve, reject)
  })
}

export const get = (url, data) => {
  return new Promise((resolve, reject) => {
    request(url, data, 'GET', resolve, reject)
  })
}

```

最后，在其他 ```JS``` 文件中调用封装好的 ```post``` 方法和 ```get``` 方法即可。