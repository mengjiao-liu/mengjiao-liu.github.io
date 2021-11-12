---
layout: post
read_time: true
show_date: true
title: "Sublime Text Markdown 插件安装"
date: 2021-11-12
img: posts/20211112/sublimetext-markdown.png
tags: [text editor, markdown]
author: Mengjiao Liu
description: "在 Sublime Text 上 安装插件以支持 Markdown 文件显示"
---
## 背景
平时会经常写 Markdown 文件，会需要用到 Markdown 语法高高亮和预览，辅助编辑。

以前我都是直接在 Goland 上安装的 Markdown插件上进行预览，或者使用在线 Markdown编辑器，
但是 Goland 应用启动比较慢，只是看个文件没必要；在线编辑器的话，总有时候网络不太好，没法及时看到文件预览结果。

因此，我想到了我的电脑上安装了 Sublime Text，以前都是用它打开一些小文件，因为它本身不带 markdown功能，需要下载插件，我嫌麻烦就一直没有安装。

这次就开始安装下，也不用下载新的应用了，Sublime Text 打开速度还是挺快的。

## 安装要求
Sublime Text 4 已经发布，以及对操作系统的要求。
```
Sublime Text 4 is the current version of Sublime Text. For bleeding-edge releases, see the dev builds.

Version: Build 4121

macOS (10.9 or later is required)
Windows - also available as a portable version
Linux repos - also available as a x86-64 or ARM64 tarball
```
我的电脑是 MacOS 版，我安装的是最新的 Sublime Text 4。

Sublime Text 3 应该也是支持这些 Markdown插件的。

## 安装插件基本步骤
- 组合键 Ctrl+Shift+P 调出命令面板
- 输入 Package Control: Install Package，回车
- 在搜索框中输入要安装的包名（一个一个，不能同时安多个）
- 静待几秒即可安装成功（看左下角的“=”是否已经消失）

### Package Control 
Package Control 是 Sublime Text 的插件管理器，或者说包管理器，有了这个插件，你才能安装其他插件。

如果 你的 Sublime Text上 没有 Package Control的话，需要安装下。
- 组合键 Ctrl+Shift+P 调出命令面板
- 输入 ipc ，选择 Install Package Control
会出现下面的框，选择 Install Package Control 这项即可。
![ipc-install](assets/img/posts/20211112/ipc-install.png)
- 耐心等待几秒钟，直到出现下面的文字，表示安装成功。
```
  Package Control was successfully installed
  Use the Command Palette and type "Install Package" to get started
```
可参考 [Package Control 官方文档地址](https://packagecontrol.io/installation)

## 安装 Markdown 插件
现在我们开始安装插件。下面是它们实现的功能：
1. MarkdownEditing： 实现语法高亮
2. MarkdownPreview：在浏览器中预览 Markdown文件
3. MarkdownLivePreview： 实时预览 Markdown文件

下面我们开始详细介绍怎么安装这些插件以及它们的优缺点。

### MarkdownEditing
- 为 Sublime Text 实现的非常有用的 Markdown 编辑功能
- 为书写而优化的配色方案
- 健壮的语法定义，支持多种语言高亮
  - 支持标准 `Markdown` 语法
  - `Github` 风味的 Markdown
  - 支持 `MultiMarkdown`

效果图：
![markdown-editing.png](assets/img/posts/20211112/markdown-editing.png)

更多详细介绍可以查看官方文档和源码：
  - [官方文档](https://sublimetext-markdown.github.io/MarkdownEditing/)
  - [源码地址](https://github.com/SublimeText-Markdown/MarkdownEditing)

安装插件过程参考上面的 「安装插件基本步骤」部分, 详情如下：
- 组合键 `Ctrl+Shift+P` 调出命令面板
- 输入 ipc ，选中 `Package Control: Install Package`
![Install Package](assets/img/posts/20211112/install-package.png)
![Install MarkdownEditing](assets/img/posts/20211112/marksownediting-install.png)
安装的时候注意看左下角，有安装进度，是个“=”号，没有弹出报错就说明安装成功啦~~

![Install Process](assets/img/posts/20211112/install-process.png)
我们可以看到，现在测试用的 Markdown文件还没有高亮，别着急
这时候我们需要切换下文件类型到 `markdown`
![Change file type](assets/img/posts/20211112/change-file-type.png)
这时候我们就可以看到 语法已经高亮了。这代表已经安装成功啦~~~
![MarkdownEditing show](assets/img/posts/20211112/markdownediting-show.png)

### MarkdownPreview
功能：
- 使用 Sublime Text 在 Web 浏览器中快速预览和构建 Markdown 文件。
- 可以使用内置的 Python Markdown 解析器（离线）或使用 GitHub Markdown API 或 GitLab Markdown API（在线）进行转换。

更多详情可见[介绍页](介绍页：https://packagecontrol.io/packages/MarkdownPreview) 以及 [github](https://github.com/facelessuser/MarkdownPreview)
安装过程跟`MarkdownEditing`安装类似，只是安装插件的名称变成了 `MarkdownPreview`，此处不再赘述。

### 在`command` 打开 MarkdownPreview
安装成功后，可以打开 `command`，输入 `MarkdownPreview`，选择 `Preview in Browser`
![MarkdownPreview in browser](assets/img/posts/20211112/markdownpreview-browser.png)
这时会让我们选择解析器，我们选择 `markdown` 即可
- github
- gitlab
- markdown

![MarkdownPreview in parser](assets/img/posts/20211112/parser.png)
即可在浏览器打开预览文件，预览效果如下：
![MarkdownPreview in browser show](assets/img/posts/20211112/markdownediting-show.png)

### 用快捷键打开 MarkdownPreview
如果你不想每次都要在`command`上输入命令才能预览，可以在 `MarkdownPreview` 设置中配置快捷键。
方法是在 `Preferences -> Key Bindings`打开的文件的右侧栏的中括号中添加一行代码：
```json
{ "keys": ["alt+m"], "command": "markdown_preview", "args": {"target": "browser", "parser":"markdown"}  }
```
![MarkdownPreview set key](assets/img/posts/20211112/key.png)
然后，我们就可以直接使用快捷键 `alt+m`进行快速预览了。

现在 `MarkdownPreview` 可以和 `LiveReload`配合使用实时自动刷新预览：MarkdownPreview + LiveReload
我对此没有太大的需求，因为还是要开浏览器，我只需要写好看下效果就行了，需要这个功能的小伙伴可以进行安装哦~

[LiveReload 详情](https://packagecontrol.io/packages/LiveReload)

### MarkdownLivePreview
MarkdownLivePreview 可以实现实时预览
安装方式跟上面类似，也不需要配置什么，直接就可以使用。
但是这个插件的预览效果很丑，不推荐使用~

实时预览效果如下：
![MarkdownLivePreview display](assets/img/posts/20211112/markdownlivepreview-display.png)

## 结语
总之，在 Sublime Text 上，我比较推荐 MarkdownEditing 和 MarkdownPreview 搭配使用，来编辑 Markdown 文件。
