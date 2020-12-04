#### brew 相关命令

1. 升级 `brew` 工具包，需要使用代理进行升级。打开 shadowsocks 工具，点击“复制终端代理命令”，在 `terminal` 中粘贴输入，再输入 `brew update` 升级 `brew` 。

2. 升级 `brew` 成功以后， 输入 `brew outdated` 查看哪些软件可以升级。

3. 根据列表，输入 `brew upgrade` 可以升级所有过时软件，也可输入 `brew upgrade softwareName` 单独升级某一软件。

4. 软件升级完成之后，输入 `brew cleanup` 清除所有过时安装包。

5. 其他命令。 `brew list` 显示已经安装的软件；`brew uninstall softwareName` 卸载某些软件。
