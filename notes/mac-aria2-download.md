网络存储中我们会在所难免的使用到百度网盘，但是越来越多的限制让它越来越难被使用，比如说非会员用户下载限速等等。特别是对于 **Mac** 用户，没有客户端，所以在下载大型文件的时候非常痛苦，不过既然有了 **Aria2** ，这些就都不是问题了。

#### 安装 Aria2

[下载 Aria2](https://github.com/aria2/aria2/releases)，点击 ```aria2.pkg``` ，按照步骤一步步安装，中间需要填写一次用户密码。

#### 配置

1、[下载配置文件](https://yalv.me/download/aria2.conf)

2、用文本编辑器或者```SublimeText```等打开配置文件 ```aria2.conf```修改第二行为 ```dir=/Users/*****/Downloads```，其中将 ```*****``` 更换成你的Mac用户名

3、打开 ```Terminal``` （终端），输入： ```mkdir ~/.aria2```， 这时用户根目录下，即： ```/Users/用户名``` 路径下回产生一个名为 ```.aria2```的文件夹 ，但是它是隐藏的，利用命令将它显示出来
```
defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder  // 显示隐藏路径的Terminal 命令
defaults write com.apple.finder AppleShowAllFiles -bool false ; killall Finder  //  取消隐藏路径的 Terminal 命令
```
在 ```macOS Sierra``` 中，我们可以使用快捷键：```Command + Shift + .``` 来快速（在 Finder 中）显示和隐藏隐藏文件了。然后将刚刚配置好的文件 ```aria2.conf``` 拖入其中

4、下载 [Aria2 应用文件](https://yalv.me/download/aria2c.zip)，将下载好的 ```Aria2c 文件夹```（如未解压先解压）拖到 ```Applications``` 文件夹中（即：应用程序）

5、运行 ```Aria2``` ，在 ```Terminal``` 中输入：```aria2c``` 若提示 ```command not found``` ,在终端输入 ```cd  /usr/local/bin``` ,然后再输入 ```sudo ln -s ../aria2/bin/aria2c aria2c``` 即可

6、检查 ```Aria2c``` 是否运行，在 ```Terminal``` 中输入：```ps aux|grep aria2c```

7、文件下载时，会生成与文件同名的 ```.aria2``` 后缀文件。针对这个问题，添加一条配置可以解决。在 ```~/.aria2/aria2.conf``` 配置文件下面，加上一条命令 ```on-download-complete="rm -f "$3.aria2"``` 并将```force-save=true```更改 ```force-save=false```然后使用命令： ```aria2c restart``` 重启 ```aria2``` 。如果还存在该问题，重启电脑即可

#### Aria2 UI 配置

本教程以 [Webui-Aria2](http://ziahamza.github.io/webui-aria2) 为参考。此 ```UI``` 面板采用了 ```https``` ，需要稍作更改。在 ```设置-连接设置-主机``` 更改为 ```localhost``` ，端口改为 ```6800``` ，取消勾选 ```SSL/TLS``` 加密。另外，建议使用谷歌浏览器操作。

#### 百度网盘下载任务导入到 ```Aria2```

1、安装 ```Chrome``` 插件：[BaiduExporter](https://chrome.google.com/webstore/detail/baiduexporter/jgebcefbdjhkhapijgbhkidaegoocbjj)

2、安装之后，在进入 **百度云盘** ，选择 **导出下载－ARIA2 PRC** 即可

3、进入 [UI 界面](https://ziahamza.github.io/webui-aria2/)检查下载进度即可

4、**百度云登录帐号使用 ```aria2``` ， 帐号会被拉黑名单限速。如果您的下载速度在 100-200 KB/S,很有可能您的百度云账户被限制了，请尝试不登陆百度云帐号导出下载，即：将要下载的文件免密分享，并退出百度账号。速度提速显著。**

#### 默认启动 ```Aria2```

为了能让 ```Aria2``` 更好的使用，我们需要将它设置为默认的启动项，最好的方法如下：

1、[下载配置文件](https://yalv.me/download/aria2/so.repo.magitwit.aria2c.plist)，将下载好的文件放置到路径： ```/Library/LaunchAgents/``` （可直接在访达中使用快捷键 ```Command＋Shift＋G``` 进入）

2、因为在使用的过程中，页面插件会启动介入，所以需要临时关闭：```Adblock Plus``` 等插件，如果没有，请无视即可。
