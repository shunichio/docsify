#### 使用 `brew` 包管理器安装 `Jenkins`

- 安装 `brew`(具体方法请 Google 之)
- 安装 `JDK` (具体方法请 Google 之，也可以使用 `brew` 进行安装)
- 安装 `Jenkins`

```shell
brew install jenkins
```

- 查看 `Jenkins` 版本号

```shell
jenkins -v

# 执行命令后的其中一条信息是 “ Running from: /usr/local/Cellar/jenkins/2.155/libexec/jenkins.war ”
# 其中 “2.155” 就是 Jenkins 的版本号
```

- 启动、执行 `Jenkins`

```shell
java -jar /usr/local/Cellar/jenkins/2.155/libexec/jenkins.war --httpPort=8080

# 其中 8080 为默认端口号
```

- 链接 `launchd` 配置文件

```shell
ln -sfv /usr/local/opt/jenkins/*.plist ~/Library/LaunchAgents
```

- 完成修改

```shell
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.jenkins.plist
```

- 再次命令行启动 `Jenkins`

```shell
# 直接启动 Jenkins
jenkins

# 使用 brew 服务启动 Jenkins
brew services start jenkins

# 使用 brew 服务停止 Jenkins
brew services stop jenkins
```

- 打开浏览器，输入 `localhost:8080` 就可看到 `Jenkins` 的 `Web` 界面

- 修改默认端口号。利用 `brew` 停止 `Jenkins` 服务，打开 `/usr/local/Cellar/jenkins/2.x.x/homebrew.mxcl.jenkins.plist` 。改成想要的端口号 `--httpPort=9090` ,最后利用 `brew` 重启 `Jenkins` 即可。

- `Jenkins` 的一些其他操作
  > http://localhost:8080/exit    //退出 Jenkins
  >
  > http://localhost:8080/restart   //重启 Jenkins
  >
  > http://localhost:8080/reload    //重新加载 Jenkins
