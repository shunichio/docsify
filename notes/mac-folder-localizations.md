### 前言
```macOS``` 在安装好以后，```Home``` 目录下的几个文件夹使用 ```Finder``` 去看都是中文，在终端下，则都显示的是英文。而如果我们自己新建一个英文的文件夹，在 ```Finder``` 里面也是英文，如何根据我们的需要，也让其在终端下显示为英文而 ```Finder``` 中显示中文呢？

其实网络上有很多方法，都是针对 ```EI Capitan``` 以下系统的，```EI Capitan``` 在转换系统文件时，会提示没有权限。所以研究了一下，具体的操作步骤如下：

### 关闭系统文件保护

+ 重启你的电脑，并同时按住 ```⌘ R``` 进入恢复模式

+ 点击打开左上角菜单中的 ```实用工具 -> 终端```

+ 输入命令 ```csrutil disable``` 关闭掉系统的 SIP 保护机制

如上三步结束后重启系统进入正常模式，此时你便可以修改系统文件了。当你修改完系统文件时，建议重新开启 SIP，否则可能会有一些恶意软件肆意修改系统文件导致系统出现问题。开启的方法与上面类似，只需要把第三步的命令换为 ```csrutil enable``` 即可。

### 添加自定义本地化名称

系统中有一个文件保存了本地化文件的对应名称的，比如你选择了中文语言，那么 ```Home``` 目录下的文件夹名字就是中文，如果你选择了其他国家那么就是其他国家的语言，这些语言配置文件在 ```/System/Library/CoreServices/SystemFolderLocalizations``` 目录。我们要修改的就是 ```zh_cn.lproj``` （中国本地化目录）下面的 ```SystemFolderLocalizations.strings``` 文件。这个文件中就保存了各种英文对应中文的对应关系，这个文件保存是二进制格式的，在修改它之前，需要先将它转换为 ```xml``` 格式，然后添加你需要的名称，最后保存并把文件转回二进制。具体操作步骤如下：

+ 进入语言配置文件目录

```shell
cd /System/Library/CoreServices/SystemFolderLocalizations/zh_cn.lproj
```

+ 将语言配置文件转换成为 ```xml``` 格式

```shell
sudo plutil -convert xml1 SystemFolderLocalizations.strings
```

+ 修改语言配置文件，建议利用 ```SublimeText``` 或者 ```Visual Studio Code``` 等工具修改。如：添加了一个 ```Developer``` 的目录，让他在 ```Finder``` 中显示为 ```开发``` 二字

+ 将语言配置文件再次转回为二进制格式

```shell
sudo plutil -convert binary1 SystemFolderLocalizations.strings
```

+ 建立 ```.localized```。上面的步骤只是添加了一个对应关系，它还起不到任何作用。如果你想让你的 ```Developer``` 文件夹在 ```Finder``` 中显示为中文，你需要在 `Developer` 目录下使用 `touch .localized` 命令新建一个 `.localized` 文件。这样才通知系统，这个文件夹为要使用本地化的翻译，`Finder` 加载时会根据 `SystemFolderLocalizations.strings` 文件中的对应关系，翻译为对应的中文

```shell
// 进入 Developer 目录下
touch .localized
```

+ 在新建完这个文件后，是不会立即看到效果的，需要刷新一下 `Finder`，使用命令 `pkill Finder` 重启 `Finder` 即可看到效果