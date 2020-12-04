#### 特性
+ 支持在md文件中，嵌入Vue的代码

+ 支持PWA

+ 集成GA分析

+ 默认rwd佈局

+ 简单的UI组件

#### 实战步骤

新建一個文件夹，并快速初始化
```shell
mkdir vuepress-demo

npm init -y
```

全局安裝`VuePress`
```shell
npm install -g vuepress
```

项目安裝`VuePress`
```shell
npm install vuepress -D
```

在项目根目录中创建一个新的文件，用來存放`md`文章
```shell
// vuepress官方预设的命名是docs，为了避免踩到额外的坑，统一使用官方的文件命名

mkdir docs
```

配置`package.json`，添加下述兩行
```shell
{
  "scripts": {
    "docs:dev": "vuepress dev docs",
    "docs:build": "vuepress build docs"
  }
}
```

创建一个`md`文件，用來解析成首页
```shell
echo '# Hello VuePress' > docs/README.md
```

启动本地`server`服务进行预览
```shell
npm run docs:dev
```

先进行一次打包构建，让项目文件夹呈现`VuePress`官方预设的目录结构
```shell
npm run docs:build
```

项目结构

![VuePress项目文件结构](https://user-gold-cdn.xitu.io/2018/4/18/162d685881e9dcc8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

其中可以看到`.VuePress`这个文件夹，这是官方预设的命名文件夹。用于存放组件`components`文件夹及配置文件`config.js`

#### 基本配置
在`.vuepress`目录下新建一个`config.js`，它导出一个对象。一些配置可以参考[官方文档](https://vuepress.vuejs.org/zh/config/#%E5%9F%BA%E6%9C%AC%E9%85%8D%E7%BD%AE)，这里我配置一些常用且必须配置的选项。

```javascript
// 进入.vuepress文件夹
// 建立一個config.js文件

module.exports = {
    
}
```

网站信息
```javascript
module.exports = {
  title: '随手笔记',
  description: 'Document library',
  head: [
    ['link', { rel: 'icon', href: `/favicon.ico` }],
  ],
}
```

导航栏配置
```javascript
module.exports = {
  themeConfig: {
    nav: [
      { text: '主页', link: '/' },
      { text: '前端规范', link: '/frontEnd/' },
      { text: '开发环境', link: '/development/' },
      { text: '学习文档', link: '/notes/' },
      { text: '游魂博客', link: 'https://www.iyouhun.com' },
      // 下拉列表的配置
      {
        text: 'Languages',
        items: [
          { text: 'Chinese', link: '/language/chinese' },
          { text: 'English', link: '/language/English' }
        ]
      }
    ]
  }
}
```
侧边栏配置
```javascript
module.exports = {
  themeConfig: {
    sidebar: {
      '/note': [
        {
          title:'前端',
          collapsable: true,
          children:[
            '/notes/frontEnd/VueJS组件编码规范',
            '/notes/frontEnd/vue-cli脚手架快速搭建项目',
            '/notes/frontEnd/深入理解vue中的slot与slot-scope',
            '/notes/frontEnd/webpack入门',
            '/notes/frontEnd/PWA介绍及快速上手搭建一个PWA应用',
          ]
        },
        {
          title:'后端',
          collapsable: true,
          children:[
            'notes/backEnd/nginx入门',
            'notes/backEnd/CentOS如何挂载磁盘',
          ]
        },
      ]
    }
  }
}
```

#### 使用Vue組件

除了基本`md`文件，`VuePress`也提供在`md`文件中，嵌入`vue`文件。
>`vuepress`预设在`.vuepress/components`下的`.vue`文件，都会自动注册到全域。因此可以在任何`md`文件中自由引用，仅需將`components`名称，作为`div`名称引入。


#### 部署上线

设置基础路径

在`config.js`设置`base`。

例如：你想要部署在`https://foo.github.io`那么设置`base`为`/`，`base`默认就为`/`，所以可以不用设置。想要部署在`https://foo.github.io/bar/`,那么`base`应该被设置成`/bar`

```javascript
module.exports = {
  base: '/documents/',
}
```
`base`将会自动地作为前缀插入到所有以`/`开始的其他选项的链接中,所以你只需要指定一次。

构建与自动部署

用 **GitHub** 的 **pages** 或者 **coding** 的 **pages** 都可以，也可以搭建在开发者自己的服务器上。只需要将`dist`文件夹中的内容提交到`git`上或者上传到服务器就好。
```shell
npm run docs:build
```
