## Mac10.12 以上系统安装软件提示文件损坏处理方法

### Mac 打开任何来源软件安装选项

```shell
sudo spctl --master-disable
```

### 关闭 Mac 系统的 SIP

- 如何查看 SIP 状态

  在终端中输入`csrutil status`，就可以看到是 `enabled` 还是 `disabled` 。（只要显示 `disabled` 说明已经禁用 SIP ）

- 如何关闭 SIP

  1. 重启你的 Mac，当听到第一声响后（或者说在开机的时候），按住 Command + R 键，按住不动，稍待片刻，电脑会进入到恢复模式。
  2. 进入后打开顶部实用工具--终端
  3. 输入命令 csrutil disable 关闭 SIP。 (同样的步骤输入命令 csrutil enable，即可重新打开 SIP)
  4. 关闭重启进入系统即可
