### npm 包升级命令

- 使用 `shell` 脚本升级 `npm` 包，首先应该找到需要升级的包和版本号，再使用 `npm install` 完成升级。

- `npm -g` 是管理本地全局包的命令，通过 `npm -g outdated` 可以查看那些包有更新。

- 执行 `npm -g outdated` 后，Terminal 终端会列出可升级包的 **当前的版本** 及 **最后的版本**，得到所有需要升级的包名和版本号就可以使用 `npm -g install <name>` 直接升级了。

### npm 更改为淘宝镜像的方法

命令行临时使用指定镜像（淘宝）

```shell
npm install -g express --registry https://registry.npm.taobao.org
```

命令行永久更改使用指定镜像（淘宝）

```shell
npm config set registry https://registry.npm.taobao.org

npm config get registry
```

以后 `npm install express` 默认使用指定（淘宝）镜像
