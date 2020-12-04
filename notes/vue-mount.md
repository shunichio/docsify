#### 1、路由懒加载

使用 `babel` 的 [pluginsbabel-plugin-dynamic-import-node](https://github.com/airbnb/babel-plugin-dynamic-import-node)。它只做一件事，就是将所有的 `import()` 转化为 `require()` 。这样就可以用这个插件将所有 **异步组件** 都用 **同步的方式** 引入，并结合 [BABEL_ENV](https://babeljs.io/docs/en/babelrc/#env-option) 这个 `bebel` 环境变量，让它只作用于开发环境下，在开发环境中将所有 `import()` 转化为 `require()`。这种方案解决了重复打包的问题，同时对代码的侵入性也很小，平时写路由的时候只需要按照官方文档路由懒加载的方式就可以了，其它的都交给 `bable` 来处理，当不想用这个方案的时候，也只要将它从 `babel` 的 `plugins` 中移除就可以了。

- 安装

```shell
npm install babel-plugin-dynamic-import-node --save-dev
```

- 在 `package.json` 中增加 `BABEL_ENV`

```json
"dev": "cross-env BABEL_ENV=development webpack-dev-server --inline --progress --config build/webpack.dev.conf.js"
```

- 在 `.babelrc` 只能加入 `babel-plugin-dynamic-import-node` 这个 `plugins` ，并让它只有在 `development` 模式中才生效

```json
{
  "env": {
    "development": {
      "plugins": ["dynamic-import-node"]
    }
  }
}
```

- 大功告成，路由只要像平时一样写就可以了

```js
{ path: '/login', component: () => import('@/views/login/index')}
```

#### 2、组件按需引入

> 根据项目实际使用的框架或库，直接看文档里面的按需引入方式，根据文档说明方式优化了。

#### 3、使用 ```CDN``` 加载外部资源

**在 `build` 目录下创建一个名为 `external-assets` 的 `js` 文件**

```javascript
const HtmlWebpackIncludeAssetsPlugin = require('html-webpack-include-assets-plugin');
const package = require('../package');

// 外部扩展默认为空
const externals = {};

// 判断运行环境。开发环境使用本地 node_modules 中的 vue，生产环境中使用 CDN 引入的 vue
if (process.env.NODE_ENV === 'production') {
  externals['vue'] = 'Vue';
  externals['Vuex'] = 'Vuex';
  externals['vue-router'] = 'VueRouter';
  externals['axios'] = 'axios';
  externals['element-ui'] = 'ELEMENT';
  externals['js-cookie'] = 'Cookies';
}

// 获取外部扩展的版本号，进行固定
const vueVersion = package.devDependencies['vue'].substring(1);
const vuexVersion = package.devDependencies['vuex'].substring(1);
const vueRouterVersion = package.devDependencies['vue-router'].substring(1);
const axiosVersion = package.devDependencies['axios'].substring(1);
const elementVersion = package.devDependencies['element-ui'].substring(1);
const cookieVersion = package.devDependencies['js-cookie'].substring(1);

// 生产环境默认注入 vue，开发环境中不注入
const externalLinks =
  process.env.NODE_ENV === 'production'
    ? [
        { path: `vue@${vueVersion}/dist/vue.min.js`, type: 'js' },
        { path: `vuex@${vuexVersion}/dist/vuex.min.js`, type: 'js' },
        { path: `vue-router@${vueRouterVersion}/dist/vue-router.min.js`, type: 'js' },
        { path: `axios@${axiosVersion}/dist/axios.min.js`, type: 'js' },
        { path: `element-ui@${elementVersion}/lib/index.js`, type: 'js' },
        { path: `js-cookie@${cookieVersion}/src/js.cookie.js`, type: 'js' },
        { path: `element-ui@${elementVersion}/lib/theme-chalk/index.css`, type: 'css' }
      ]
    : [];



const plugins = [
  new HtmlWebpackIncludeAssetsPlugin({
    assets: externalLinks,
    // 是否是在 webpack 注入的 js 文件后新增 js 文件？ true 代表 append, false 代表 prepend。
    // 生产环境中，这些 js 应该先加载。
    append: process.env.NODE_ENV !== 'production',
    publicPath: 'https://unpkg.com/'
  })
];

module.exports = {
  externals,
  plugins
};

```

**在 `webpack.base.conf.js` 文件中引入相关配置**

```javascript

// 引入外部扩展和插件
const {externals, plugins} = require('./external-assets')

module.exports = {
  ...,
  externals,
  plugins,
  ...
};
```

**如果使用的是 `Vue-Cli 3.0` 搭建项目，则需要在 `vue.config.js` 中进行修改**

```javascript
// vue.config.js文件

// 引入html-webpack-include-assets-plugin插件
const HtmlWebpackIncludeAssetsPlugin = require('html-webpack-include-assets-plugin');

// 获取外部扩展的版本号，进行固定
const reliances = require('./package');
const vueVersion = reliances.devDependencies['vue'];
const vuexVersion = reliances.devDependencies['vuex'];
const vueRouterVersion = reliances.devDependencies['vue-router'];
const axiosVersion = reliances.devDependencies['axios'];
const elementVersion = reliances.devDependencies['element-ui'];
const echartsVersion = reliances.devDependencies['echarts'];
const cookieVersion = reliances.devDependencies['js-cookie'];
const momentVersion = reliances.devDependencies['moment'];
const npVersion = reliances.devDependencies['nprogress'];

module.exports = {
  configureWebpack: config => {
    // 外部扩展，默认为空
    let externals = {};
    // 外部扩展链接及类型，默认为空
    let externalLinks = [];
    if (debug) {
      // 开发环境配置
      // 外部扩展相关配置
      externalLinks = [];
      externals = {};
    } else {
      // 生产环境配置
      // 外部扩展相关配置
      externalLinks = [
        { path: `vue@${vueVersion}/dist/vue.min.js`, type: 'js' },
        { path: `vuex@${vuexVersion}/dist/vuex.min.js`, type: 'js' },
        { path: `vue-router@${vueRouterVersion}/dist/vue-router.min.js`, type: 'js' },
        { path: `axios@${axiosVersion}/dist/axios.min.js`, type: 'js' },
        { path: `element-ui@${elementVersion}/lib/index.js`, type: 'js' },
        { path: `echarts@${echartsVersion}/dist/echarts.min.js`, type: 'js' },
        { path: `js-cookie@${cookieVersion}/src/js.cookie.js`, type: 'js' },
        { path: `moment@${momentVersion}/moment.js`, type: 'js' },
        { path: `nprogress@${npVersion}/nprogress.js`, type: 'js' },
        { path: `element-ui@${elementVersion}/lib/theme-chalk/index.css`, type: 'css' },
        { path: `nprogress@${npVersion}/nprogress.css`, type: 'css' }
      ];
      externals['vue'] = 'Vue';
      externals['Vuex'] = 'Vuex';
      externals['vue-router'] = 'VueRouter';
      externals['axios'] = 'axios';
      externals['element-ui'] = 'ELEMENT';
      externals['echarts'] = 'echarts';
      externals['js-cookie'] = 'Cookies';
      externals['moment'] = 'moment';
      externals['nprogress'] = 'NProgress';
    }
    Object.assign(config, {
      // 开发生产共同配置
      externals
    });
    config.plugins = [
      ...config.plugins,
      // html-webpack-include-assets-plugin插件只能在html-webpack-plugin，否则会报错。
      new HtmlWebpackIncludeAssetsPlugin({
        assets: externalLinks,
        // 是否是在 webpack 注入的 js 文件后新增 js 文件？ true 代表 append, false 代表 prepend。
        // 生产环境中，这些 js 应该先加载。
        append: debug,
        publicPath: 'https://unpkg.com/'
      })
    ];
  },
}
```


#### 总结
遇到webpack打包性能问题，先执行npm run build --report分析一波，然后根据分析结果来做相应的优化，谁占体积大就优化谁。路由很多的复杂页面，路由懒加载是肯定要做的，现在很多库都有提供按需加载的功能，有需要的话可以按照官方文档的做法来按需加载，webpack提供的externals可以配合外部资源CDN轻松大幅度减少打包体积，适用于echarts、jQuery、lodash这种暴露了一个全局变量的库，千万不要忘了开启Gzip压缩。本文讲的只是针对于webpack层面的优化，性能优化不只这些，还有其他方面的优化，比如页面渲染优化（减少重排）、网络加载优化等。



