**1、根据 ```vue.js``` 官网的使用说明，利用 ```npm``` 安装 ```vue-cli``` 脚手架。**

**2、根据 ```vux``` 官网的文档，安装 ```vux``` 及 ```vux-loader```。**

```
npm install vux --save

npm install vux-loader --save-dev
``` 

**3、由于 ```vux``` 使用的 ```css``` 预处理工具是 ```less``` ，因此还需要安装 ```less``` 及 ```less-loader``` 。**

```
npm install less less-loader --save-dev
``` 

**4、如果在编码过程使用 ```stylus``` 作为 ```css``` 的预处理工具，那还需安装 ```stylus``` 及 ```stylus-loader``` 。**

```
npm install stylus stylus-loader --save-dev
``` 

**5、安装 ```babel-polyfill``` 和 ```babel-runtime``` 。**

```
npm install babel-polyfill --save-dev

npm install babel-runtime --save
``` 

**6、安装 ```fastclick``` 移除移动端点击延迟效果。**

```
npm install fastclick --save
```

**7、 ```vux2``` 必须配合 ```vux-loader``` 使用, 需在 ```build/webpack.base.conf.js``` 里对代码进行配置。**

```javascript
const vuxLoader = require('vux-loader')
const webpackConfig = originalConfig // originalConfig代表原来的 module.exports 的值（Object）
module.exports = vuxLoader.merge(webpackConfig, {
  plugins: ['vux-ui']
})
```