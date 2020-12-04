**一、 在 ```devServer.before``` 中进行配置，在 ```build``` 文件目录下的 ```webpack.dev.conf.js``` 文件中，找到 ```devServer```对象，添加一个before函数，在函数内伪装（自定义）接口**
```javascript
before(app) {
  /**
   * “req”为请求接口时传递进来的参数
   * “res”为接口请求成功传递出去的值
   * “/some/path”为开发者伪装（自定义）的接口，以便在前端使用
   */
  app.get('/some/path', function(req, res) {
    
    const url = '/real/api/path'    // 获取数据的真实接口

    axios
      .get(url, {
        headers: {
          referer: 'https://xxx.xxx.xxx.com/',
          host: 'xxx.xxx.xxx.com'
        },
        params: req.query
      })
      .then(response => {
        // “response”为真实接口返回的数据，通过json()方法把数据透传到我们自定义的接口，伪造成自定义接口返回数据给给前端的假象
        res.json(response.data)
      })
      .catch(e => {
        console.log(e)
      })
  })
}
```
**PS：前端在请求数据时，直接请求自定义的接口 ```/some/path``` 即可**

**二、在 ```devServer.proxy``` 中进行配置，在 ```config``` 文件目录下的 ```index.js``` 文件中，找到 ```proxyTable```对象，添加代理即可。**
```javascript
// 代理配置表，在这里可以配置特定的请求代理到对应的API接口
// 例如将'localhost:8080/api/abc'代理到'www.example.com/api/abc'
// 使用方法：https://vuejs-templates.github.io/webpack/proxy.html
proxyTable: {
  '/api': {
    target: 'https://xxx.xx.xx.com', // 接口的域名
    secure: true,  // 如果是https接口，需要配置这个参数
    changeOrigin: true, // 如果接口跨域，需要进行这个参数配置
    pathRewrite: {
      '^/api': '/api'
    }
  }
}
```
**PS：在上面的例子中，```/api``` 代表的是真实数据路径 ```http://xxx.xx.xx.com``` ，调用接口 `/api/abc` 时，实际调用的接口是：```http://xxx.xx.xx.com/abc``` 。这与需要调用的真实接口 `www.example.com/api/abc` 存在明显错误，需要在 ```/abc``` 前面加上路径 ```/api``` ，因此利用 ```pathRewrite``` 补全请求路径。如果调用的真实接口为： `http://xxx.xx.xx.com/abc` ，则将 ```pathRewrite``` 中属性的值置为空 `''`**