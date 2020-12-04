## Webpack Code Splitting 的 splitChunks 配置探索

**Webpack 4** 废弃了之前的不怎么好用的 `CommonsChunk` ，取而代之的是 `SplitChunks` 。既然是后起之秀，那肯定是有进步的。不过话说回来，虽然是进步了不少，但是如果刚接触的话配置起来同样还是一头雾水。

### Code Splitting

首先 webpack 总共提供了三种办法来实现 Code Splitting ，如下：

- 入口配置： `entry` 入口使用多个入口文件；
- 抽取公有代码：使用 `SplitChunks` 抽取公有代码；
- 动态加载：动态加载一些代码。

这里我们姑且只讨论使用 `SplitChunks` 抽取公有代码。

### SplitChunks

首先我们所说的 `SplitChunks` 是由 webpack 4 内置的 `SplitChunksPlugin` 插件提供的能力，可直接在 `optimization` 选项中配置，其默认配置如下：

```js
module.exports = {
  //...
  optimization: {
    splitChunks: {
      chunks: 'async',
      minSize: 30000,
      maxSize: 0,
      minChunks: 1,
      maxAsyncRequests: 5,
      maxInitialRequests: 3,
      automaticNameDelimiter: '~',
      name: true,
      cacheGroups: {
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true
        }
      }
    }
  }
};
```

参数说明如下：

- chunks：表示从哪些 chunks 里面抽取代码，除了三个可选字符串值 initial、async、all 之外，还可以通过函数来过滤所需的 chunks；
- minSize：表示抽取出来的文件在压缩前的最小大小，默认为 30000；
- maxSize：表示抽取出来的文件在压缩前的最大大小，默认为 0，表示不限制最大大小；
- minChunks：表示被引用次数，默认为 1；
- maxAsyncRequests：最大的按需(异步)加载次数，默认为 5；
- maxInitialRequests：最大的初始化加载次数，默认为 3；
- automaticNameDelimiter：抽取出来的文件的自动生成名字的分割符，默认为 ~；
- name：抽取出来文件的名字，默认为 true，表示自动生成文件名；
- cacheGroups: 缓存组。（这才是配置的关键）

#### cacheGroups

上面的那么多参数，其实都可以不用管， `cacheGroups` 才是我们配置的关键。它可以继承/覆盖上面 `splitChunks` 中所有的参数值，除此之外还额外提供了三个配置，分别为： `test` , `priority` 和 `reuseExistingChunk` 。

- `test` : 表示要过滤 `modules` ，默认为所有的 `modules` ，可匹配模块路径或 `chunk` 名字，当匹配的是 `chunk` 名字的时候，其里面的所有 `modules` 都会选中；
- `priority` ：表示抽取权重，数字越大表示优先级越高。因为一个 `module` 可能会满足多个 `cacheGroups` 的条件，那么抽取到哪个就由权重最高的说了算；
- `reuseExistingChunk` ：表示是否使用已有的 `chunk` ，如果为 `true` 则表示如果当前的 `chunk` 包含的模块已经被抽取出去了，那么将不会重新生成新的。

### 配置实战

一般来说我们常用的配置都是 `common` + `page` 的形式。而 `page` 在 `entry` 入口的时候就已经配置好了。那么现在就只剩下 `common` 的处理，这里讨论几种方案：

#### 一刀切

下面我们把所有 `node_modules` 的模块被不同的 `chunk` 引入超过 `1` 次的抽取为 `common` 。

```js
cacheGroups: {
  common: {
    test: /[\\/]node_modules[\\/]/,
    name: 'common',
    chunks: 'initial',
    priority: 2,
    minChunks: 2,
  },
}
```

或者干脆把所有模块被不同的 `chunk` 引入超过 `1` 次的抽取为 `common` 。

```js
cacheGroups: {
  common: {
    name: 'common',
    chunks: 'initial',
    priority: 2,
    minChunks: 2,
  },
}
```

#### 进一步抽取

比如我们想把一些基础的框架单独抽取如 `react` ，然后是业务的基础。

```js
cacheGroups: {
  reactBase: {
    name: 'reactBase',
    test: (module) => {
        return /react|redux|prop-types/.test(module.context);
    },
    chunks: 'initial',
    priority: 10,
  },
  common: {
    name: 'common',
    chunks: 'initial',
    priority: 2,
    minChunks: 2,
  },
}
```

#### 疑问

整个顺下来看起来好像是比较简单明了了，而且还提供了 `test` 和 `chunks` 来过滤筛选，一个字：很棒！但是如果要实现更精细的控制，就会发现还是有点问题。

以上面抽取 `react` 相关的基础库为例，简单的 `test` 匹配 `react` ，误杀的太多，比如有些 `react` 组件命名就有 `react` 关键词，结果把这个也打进去了，肯定不是我们想要的。

于是就想到从 `entry` 入口里面定义一个 `react` 的基础库，核心代码如下：

```js
const vendorPkg = [
  'react',
  'react-dom',
  'redux',
  'redux-thunk',
  'react-redux',
  'react-router-dom',
  'react-router-redux',
  'history',
  'prop-types',
  'react-loadable',
];

module.exports = {
  entry: {
    vendor: vendorPkg,
    ...
  }
}
```

然后开始配置 `cacheGroups` ，发现不论怎么配置都实现不了其他页面共用这个 `vendor` 。

### CSS 配置

同样对于通过 `MiniCssExtractPlugin` 生成的 `CSS` 文件也可以通过 `SplitChunks` 来进行抽取公有样式等。

如下表示将所有 `CSS` 文件打包为一个（注意将权重设置为最高，不然可能其他的 `cacheGroups` 会提前打包一部分样式文件）：

```js
module.exports = {
  optimization: {
    splitChunks: {
      cacheGroups: {
        styles: {
          name: 'styles',
          test: /\.css$/,
          chunks: 'all',
          enforce: true,
          priority: 20
        }
      }
    }
  }
};
```
