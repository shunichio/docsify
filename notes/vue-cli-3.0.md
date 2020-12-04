#### 文件`vue.config.js`的相关配置及其说明

```javascript
const path = require("path");
const debug = process.env.NODE_ENV !== "production";
function resolve(dir) {
  return path.resolve(__dirname, dir);
}
module.exports = {
  baseUrl: process.env.BASE_URL, // URL基本路径
  assetsDir: "static", // 静态资源目录 (js, css, img, fonts等所在文件夹名称)
  productionSourceMap: true, // 是否在构建生产包时生成 sourceMap 文件，false将提高构建速度
  // webpack配置，值为对象时会合并配置，为方法时会改写配置
  configureWebpack: config => {
    if (debug) {
      // 开发环境配置
      // ...
    } else {
      // 生产环境配置
      // ...
    }
    // 开发生产共同配置
    Object.assign(config, {
      // 目录别名
      resolve: {
        extensions: [".js", ".vue", ".json"],
        alias: {
          vue$: "vue/dist/vue.esm.js",
          src: resolve("./src"),
          api: resolve("./src/api"),
          common: resolve("./src/common"),
          components: resolve("./src/components"),
          router: resolve("./src/router"),
          store: resolve("./src/store"),
          views: resolve("./src/views")
        }
      }
    });
    config.plugins = [
      ...config.plugins,
      // 添加插件
      ...[]
    ];
  },
  // webpack链接操作，允许我们更细粒度的控制其内部配置
  chainWebpack: config => {
    // 配置svg图标
    config.module
      .rule("svg")
      .exclude.add(resolve("src/assets/icons"))
      .end();

    config.module
      .rule("icons")
      .test(/\.svg$/)
      .include.add(resolve("src/assets/icons"))
      .end()
      .use("svg-sprite-loader")
      .loader("svg-sprite-loader")
      .options({
        symbolId: "icon-[name]"
      });
  },
  // 对于 CSS 相关 loader 来说，我们推荐使用 css.loaderOptions 而不是直接链式指定 loader
  // 配置高于chainWebpack中关于css loader的配置
  css: {
    modules: true, // 是否开启支持‘foo.module.css’样式
    extract: true, // 是否使用css分离插件 ExtractTextPlugin，采用独立样式文件载入，不采用<style>方式内联至html文件中
    sourceMap: false, // 是否在构建样式地图，false将提高构建速度
    // css预设器配置项
    loaderOptions: {
      css: {
        localIdentName: "[name]-[hash]",
        camelCase: "only"
      },
      stylus: {}
    }
  },
  // 前端API接口跨域代理
  devServer: {
    proxy: {
      "/api": {
        target: "http://192.168.0.18:8080/api/",
        changeOrigin: true, //是否跨域
        pathRewrite: {
          "^/api": ""
        }
      }
    },
    before: app => {
      // 模拟后端接口
      // ...
    }
  }
};
```

#### 环境变量和模式

在项目根目录创建在所有的环境中被载入 `.env` 文件或者创建只在指定的模式中被载入的 `.env[moe]` 文件，如：`.env.development` 或者 `.env.production`

##### `.env`

```shell
# api接口基本路径
VUE_APP_BASE_API = "/api"
```

##### `.env.development`

```shell
# URL基本路径
BASE_URL = "/"
```

##### `.env.production`

```shell
# URL基本路径
BASE_URL = "/app"
```

> PS: 环境变量文件中不能出现分号 `;`
