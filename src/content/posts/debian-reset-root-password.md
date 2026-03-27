---
title: Debian系统重置忘记的 root 密码
description: 忘记root密码怎么办？教你重置忘记的 root 密码
published: 2026-03-27
tags: [Linux, 系统运维]
category: 系统运维
lang: zh-CN
image: /images/debian-reset-root-password.png
---

**1. 打开 Debian Grub 启动菜单**

重新启动系统，同时按住键盘上的Shift键，这将使您进入 Debian 的 Grub 菜单。远程服务器则需要用控制台或者VNC连接。

**2. 编辑 Gurb 菜单以进入命令行模式**

选中 Grub 菜单上的启动菜单后，请直接按键盘上的" e"键，这将允许您编辑 Grub 的引导提示符。请不要触摸或删除此处的任何内容。

在 Grub 编辑屏幕上，使用箭头键移动到以" Linux "开头的这一行文字的末尾。在此行类型的末尾加上：

```bash
rw init=/bin/bash
```

添加给定的语法后，接下来，使用此配置启动您的系统。为此，请使用 Ctrl+X 或 F10 进入命令行模式。

**3. 重置root用户密码**

现在，让我们像在 Linux 系统上一样使用命令终端更改密码。

输入命令：

```bash
passwd
```

系统会两次提示您输入新密码，输入完成后回车。

**4. 重启系统**

完成重置 Linux 密码后，重新启动系统以使用更改后的密码登录。要重新启动，请键入：

```bash
exec /sbin/init
```

然后按下Enter键重启。
