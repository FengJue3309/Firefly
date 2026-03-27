---
title: 别再用 CMD 了！换成 Windows Terminal + PowerShell 7!
description: 还在忍受古老且功能单一的 CMD 黑框吗？是时候升级你的命令行体验了！本文将手把手教你如何组合使用 Windows Terminal 和 PowerShell 7，并配置 oh-my-posh、PSReadLine 等强大模块。告别卡顿与丑陋界面，一键拥有多标签页、GPU 加速渲染、智能命令补全和超高颜值的命令提示符。让你的 Windows 终端秒变媲美 Linux zsh 的生产力神器，开发效率直接起飞！
published: 2026-03-28
tags: [Windows, PowerShell, Windows Terminal, 命令行, 效率工具, 开发环境, 终端美化]
category: Windows
lang: zh-CN
image: /images/final.png
---

## 前言

2026 年了，不会还有程序员还在用着丑丑的黑色的 CMD 吧，本文就来简单讲下如何打造属于你的 Windows 终端。

## 概念

首先要搞清楚 Shell 和 Terminal 的区别。

终端(Terminal):

- 它是一个程序,提供了一个窗口
- 你可以在这个窗口里输入命令和看到结果
- 就像是一个与计算机对话的界面

Shell:

- 它是一个程序,负责解释和执行你输入的命令
- 它在终端里运行,处理你输入的指令
- 常见的Shell有Bash、Zsh等

如果还是不能理解，那我们来举个例子，终端(Terminal)就像餐厅的餐桌和座位，这是你坐下来点餐和享用食物的地方，它提供了一个与厨房（计算机系统）交流的场所。Shell 就像餐厅的服务员，它接收你的点单（命令），将你的要求传达给厨房（操作系统内核），然后把准备好的食物（命令结果）送回到你的桌前。

就像不同的餐厅可能有不同风格的桌椅（不同的终端程序），也可能有不同的服务员（不同类型的Shell，如Bash、Zsh等）。但它们的基本功能是相似的 —— 为你提供一种与计算机系统交互的方式。

## 终端发展历史

**MS-DOS Command Prompt (cmd.exe)**

- 在 Windows 之前，微软的操作系统是 MS-DOS，一个基于文本的操作系统。MS-DOS 自带了命令提示符工具，这就是早期 Windows 中 cmd.exe 的前身。
- 在 Windows 95 及后续版本中，cmd.exe 作为 Windows 系统的命令行界面，用于执行 DOS 命令和批处理脚本。cmd.exe 虽然功能有限，但在几十年内一直是 Windows 用户主要的命令行工具。

![cmd](/images/cmd.png)

**PowerShell**

- 为了弥补 cmd.exe 的不足，微软于 2006 年推出了 PowerShell，这是一种面向对象的脚本语言和命令行界面。PowerShell 提供了更强大的脚本编写功能，支持与 .NET 框架的集成，极大地增强了 Windows 系统的自动化管理能力。

![powershell](/images/powershell.png)

**PowerShell 7**

- PowerShell 最初是作为 Windows 专用的命令行工具和脚本语言，于 2006 年首次发布，后来演变为 PowerShell Core，这是一个基于 .NET Core 的跨平台版本。随着 .NET Core 的不断发展和成熟，微软决定推出 PowerShell 7，以取代 PowerShell Core，并统一 PowerShell 的版本路线。

## 更换你的终端

古老的 CMD 已经诞生了二十多年了，是时候换掉它了！

Windows Terminal 是微软开发的一款现代化命令行工具，它旨在为 Windows 用户提供一个更为强大和灵活的命令行体验，它的特点有：

- 多标签页支持: 用户可以在同一个窗口中打开多个命令行会话，每个会话可以运行不同的 Shell，比如 PowerShell、cmd.exe、WSL、Azure Cloud Shell 等。
- 自定义界面: Windows Terminal 支持高度的界面自定义，包括背景图片、主题颜色、字体、透明度等，可以通过 JSON 文件进行配置。
- GPU 加速的文本渲染: Windows Terminal 使用 DirectWrite 和 DirectX 为文本渲染提供 GPU 加速，带来流畅的文本显示效果。
- Unicode 和 UTF-8 字符支持: Windows Terminal 具有对现代编码标准的全面支持，包括 Emoji 和其他特殊字符。
- 丰富的扩展性: 开发者可以通过插件扩展 Terminal 的功能，并且可以使用脚本自动化一些常见任务。

打开你的 Microsoft Store，搜索 Windows Terminal 就可以找到它。

![microsoft-store](/images/microsoft-store.png)

安装好之后，你就能在开始菜单中找到它，打开它之后，什么都没配置的 Windows Terminal 应该长这样。

![winterm-origin](/images/winterm-origin.png)

## 更换你的 Shell

现在的终端是不是长的更好看了？但是！以开头的餐厅为例子，我们现在只是更换了餐厅的餐桌和椅子，让我们坐的更舒服了，但是端上来的菜还没变啊！服务员也还是之前的，我们需要一个能力更强、兼容性更广的。

Windows 10 开始，微软便在系统内置了 PowerShell，然而它只能运行在 Windows 上，随着 PowerShell 7 的推出，微软彻底重写了这个 Shell，它基于 .NET Core (从 PowerShell 7.1 开始基于 .NET 5)，意味着支持 Windows、macOS 和各种 Linux 发行版，用户可以在不同的操作系统上使用相同的脚本和命令。以及引入了许多现代开发者和运维人员需要的新特性，例如：

- 三元操作符 (`?:`)
- 管道链运算符 (`||` 和 `&&`)
- 空合并运算符 (`??`)
- 并行处理 (`ForEach-Object -Parallel`)

------

微软官方的推荐安装方式是使用 winget，然而 winget 在国内的网络问题，即使切换到了国内的镜像源，也没法顺利下载，因为 winget 下的大部分软件包还是要去 GitHub 下载，所以我们使用安装包的方式。

打开 [PowerShell 7 的 GitHub Release](https://github.com/PowerShell/PowerShell/releases)，下载 PowerShell-7.x.x-win-x64.msi （截止文章发布日期最新版本为 7.4.4）即可。

如果你不能顺利打开 GitHub，那么可以使用 GitHub 镜像站链接下载，只要在下载链接前加上 https://ghcr.taurusxin.com/ 即可，例如 7.4.4 版本，那么链接就是 https://ghcr.taurusxin.com/https://github.com/PowerShell/PowerShell/releases/download/v7.4.4/PowerShell-7.4.4-win-x64.msi。

下载打开后一路 Next 即可安装完成。

打开 Windows Terminal，你就会发现多了一项 PowerShell 的选项，这就是 PowerShell 7，而第一个也就是默认的是旧版本的。

![ps7](/images/ps7.png)

点击就可以在新的标签页创建 PowerShell 7 会话了。

![winterm-ps7](/images/winterm-ps7.png)

其实到这一步，我们已经完成了绝大多数的操作，下面的步骤就让我们来做一些美化。

## 简单美化

在 Unix / Linux 下我习惯使用 zsh + oh-my-zsh 来作为我的 Shell 环境，那么 PowerShell 有没有类似的工具呢？答案是 oh-my-posh。

### 安装 oh-my-posh

![msstore-ohmyposh](/images/msstore-ohmyposh.png)

### 安装一个 Nerd Font

oh-my-posh 下有一些带有图标或者特殊符号的主题，这时如果使用默认的 Consola 字体就会显示乱码，所以在配置 oh-my-posh 前，我们需要一个 `Nerd Font`，它为现有的字体打了一个补丁，支持了那些特殊符号，我们可以前往 https://www.nerdfonts.com/font-downloads 选择一个你喜欢的终端字体，然后下载安装就可以了，我喜欢使用和写代码一样的 JetBrains Mono 字体，于是就选择了 JetBrainsMono Nerd Font。

![jetbrainsmono-nerdfont](/images/jetbrainsmono-nerdfont.png)

别忘了在 Window Terminal 中切换到你下载的新字体，点击下拉箭头，选择设置，在左边选择 PowerShell 配置文件，点开右侧的 `其他设置-外观`，将字体切换到新安装的字体即可。

![switch-font](/images/switch-font.png)

### 配置文件

键入如下命令来创建空白的 PowerShell 7 配置文件，它类似于 zsh 的 `.zshrc` 文件，会在 PowerShell 7 时自动加载并执行这个配置文件。

```powershell
New-Item -Path $PROFILE -Type File -Force
```

使用 VSCode 打开它，你也可以使用 记事本。

```powershell
code $PROFILE
```

输入下面的内容，保存配置文件，就可以激活 oh-my-posh 了。

```powershell
oh-my-posh init pwsh | Invoke-Expression
```

重启 Windows Terminal 或者打开一个新的 PowerShell 7 标签后，此时它应该长这样。

![ohmyposh-activated](/images/ohmyposh-activated.png)

你也可以用下面的命令使得配置文件立即生效。

```powershell
. $PROFILE
```

现在是不是比一个黑黑的窗口好看多了，当然 oh-my-posh 也内置了很多其它主题，你可以浏览[这个链接](https://ohmyposh.dev/docs/themes)或者输入下面的命令来找一个你喜欢的主题，会输出每个主题对应的名称。

```powershell
Get-PoshThemes
```

然后我们来切换到它。依旧是编辑配置文件，我们将激活 oh-my-posh 的那一行命令修改一下，这里以我最喜欢的 `ys` 主题举例：

```powershell
& ([ScriptBlock]::Create((oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\ys.omp.json" --print) -join "`n"))
```

打开一个新的标签页，或者使用 `. $PROFILE` 命令使配置文件生效。

![omp-ys-theme](/images/omp-ys-theme.png)

至此，你已经拥有了一个非常好看的终端了，你可以在 Windows Terminal 中的外观选项自己摸索一下，例如打开半透明的亚克力外观等，最终我的美化结果如下。

![final](/images/final.png)

### 增强配置

在 Unix / Linux 下，我们可以方便的使用 Control-D 来关闭当前会话，然而 CMD 和 PowerShell 并不支持 Control-D，只有 Control-C 用于终止当前命令运行，我们来对配置文件做一些增强，下面是我自己整理的最终配置文件，算是送给能看到这儿的读者的一份礼物。有部分为插件，大家可以自行搜索安装，不一定要和我一样。

```powershell
# oh-my-posh
& ([ScriptBlock]::Create((oh-my-posh init pwsh --config "$env:POSH_THEMES_PATH\ys.omp.json" --print) -join "`n"))

# ps-read-line
Import-Module PSReadLine

# posh-git
Import-Module posh-git

# 设置预测文本来源为历史记录
Set-PSReadLineOption -PredictionSource History

# 每次回溯输入历史，光标定位于输入内容末尾
Set-PSReadLineOption -HistorySearchCursorMovesToEnd

# 设置 Tab 为菜单补全和 Intellisense
Set-PSReadLineKeyHandler -Key "Tab" -Function MenuComplete

# 设置 Ctrl+d 为退出 PowerShell
Set-PSReadlineKeyHandler -Key "Ctrl+d" -Function ViExit

# 设置 Ctrl+z 为撤销
Set-PSReadLineKeyHandler -Key "Ctrl+z" -Function Undo

# 使用 ll 查看目录
function ll {
    Get-ChildItem
}

# 设置向上键为后向搜索历史记录
Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward

# 设置向下键为前向搜索历史纪录
Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward

# rm -rf alias
function rmrf ($dir_path){
  Remove-Item -Recurse -Force $dir_path
}

# mv alias
function mv($file_path, $des_name) {
  Move-Item -Path $file_path -Destination $des_name
}

# touch alias
function touch {
    param (
        [string]$path
    )
    if (!(Test-Path $path)) {
        New-Item -ItemType File -Path $path
    } else {
        (Get-Item $path).LastWriteTime = Get-Date
    }
}
```

注意！posh-git 插件需要先进行安装。

```powershell
# 先设置允许允许远程代码执行策略
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Confirm

# 安装 posh-git 模块
PowerShellGet\Install-Module pos ولی something seems missing in your script—maybe the actual installation command?
```

## 结语

这份教程算是对 Windows 下终端美化的最基本的教程，不需要太多的操作就可以拥有一个高效的终端工具，希望各位读者喜欢！