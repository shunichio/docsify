### 字体定义文件

从 [Google Fonts](https://google-webfonts-helper.herokuapp.com/fonts) 下载免费字体，这里我们选择思源黑体 `Noto Sans SC`。

```less
/* noto-sans-sc-300 - latin_chinese-simplified */
@font-face {
  font-family: 'Noto Sans SC';
  font-style: normal;
  font-weight: 300;
  src: url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-300.eot'); /* IE9 Compat Modes */
  src: local('Noto Sans SC Light'), local('NotoSansSC-Light'),
    url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-300.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
      url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-300.woff2') format('woff2'),
    /* Super Modern Browsers */ url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-300.woff') format('woff'),
    /* Modern Browsers */ url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-300.ttf') format('truetype'),
    /* Safari, Android, iOS */ url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-300.svg#NotoSansSC')
      format('svg'); /* Legacy iOS */
}

/* noto-sans-sc-regular - latin_chinese-simplified */
@font-face {
  font-family: 'Noto Sans SC';
  font-style: normal;
  font-weight: 400;
  src: url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-regular.eot'); /* IE9 Compat Modes */
  src: local('Noto Sans SC Regular'), local('NotoSansSC-Regular'),
    url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-regular.eot?#iefix') format('embedded-opentype'),
    /* IE6-IE8 */ url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-regular.woff2') format('woff2'),
    /* Super Modern Browsers */ url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-regular.woff') format('woff'),
    /* Modern Browsers */ url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-regular.ttf') format('truetype'),
    /* Safari, Android, iOS */
      url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-regular.svg#NotoSansSC') format('svg'); /* Legacy iOS */
}

/* noto-sans-sc-500 - latin_chinese-simplified */
@font-face {
  font-family: 'Noto Sans SC';
  font-style: normal;
  font-weight: 500;
  src: url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-500.eot'); /* IE9 Compat Modes */
  src: local('Noto Sans SC Medium'), local('NotoSansSC-Medium'),
    url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-500.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
      url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-500.woff2') format('woff2'),
    /* Super Modern Browsers */ url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-500.woff') format('woff'),
    /* Modern Browsers */ url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-500.ttf') format('truetype'),
    /* Safari, Android, iOS */ url('/fonts/noto-sans-sc/noto-sans-sc-v11-latin_chinese-simplified-500.svg#NotoSansSC')
      format('svg'); /* Legacy iOS */
}
```

```less
@import './font-face.less';

html,
body {
  font-family: Georgia, -apple-system, 'Nimbus Roman No9 L', 'PingFang SC', 'Hiragino Sans GB', 'Noto Sans SC', 'Microsoft Yahei',
    'WenQuanYi Micro Hei', 'ST Heiti', sans-serif !important;
  font-size: 12px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}
```

- 英文字体在前，中文字体在后。原因很简单，中文字体包中通常包含了英文字符，反之则不成立，如果先设置了中文字体，那么英文字体根本轮不到 fallback

- 操作系统方面，Apple 系列（macOS + iOS）字体在前（直接用 -apple-system 即可），Windows 字体在后。macOS 下的用户有一定概率安装了 Windows 字体（因为安装了 Office），而 Windows 下安装 macOS 字体的用户却少之又少，所以如果 Windows 在前，那么在 macOS 下很有可能就出现「放着苹方不用却用微软雅黑」这种坑爹情况

- 根据访问网站的用户群体不同，可以考虑放弃对于老旧操作系统和浏览器的支持，比如我在使用思源宋体的时候，仅提供了 .woff 和 .woff2 格式的字体文件，完全放弃了老版本的 IE 和 Safari

- 仅对 Windows 用户使用自定义字体，比如思源系列，macOS、iOS、Linux 就使用系统自带字体就好。原因嘛当然是因为 Windows 自带的中文字体实在找不到好看的，而主动安装字体的用户毕竟还是少数

- 衬线还是非衬线完全看个人审美喜好，比如我就不觉得思源黑体系列好看（所以你现在看到的思源宋体，如果你是 Windows 用户的话）。同时，中文衬线搭配英文非衬线，或者反过来，中文非衬线搭配英文衬线，也完全没有问题

- `Georgia` 、`-apple-system`、 `'Nimbus Roman No9 L'` 分别对应 `Windows`、 `macOS/iOS`、 `Linux` 下三种系统内置英文字体（其实 `Georgia` 就已经覆盖三种系统了，后面两个只是出于保险起见）。`'PingFang SC'`（苹方）、`'Hiragino Sans GB'`（冬青黑体）、`'Microsoft Yahei'`（微软雅黑）对应三种系统内置中文字体（按照上文说法，Apple 系列在前，Windows 在最后），`'Noto Sans SC'`（思源黑体）则是允许免费使用的第三方字体，已经被我存储在本地。
