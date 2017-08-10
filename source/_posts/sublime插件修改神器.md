---
title: sublime插件修改神器
date: 2017-08-08
tags: [sublime，PackageResourceViewer]
---

下述文章使用sublime的PackageResourceViewer解决问题有：
1. sublime控制台不支持键盘输入
2. 一台电脑上同时安装python2和python3

其实第二点跟这个插件也没啥关系，只是通过PackageResourceViewer让我知道第二点如何解决而已，懒得再写一篇，就统一放在这里了.

### 安装

使用package control安装(package control的安装比较简单，我就不说了)。快捷键按下ctrl+shift+p-->install package-->输入PackageResourceViewer 回车安装

### 使用

ctrl+shift+p-->输入open Resource-->即可看到所有的sublime插件列表-->继续打开可以看到所有文件内容

<!-- more -->

### 实例

sublime功能十分完善，但是我使用此软件学习python时，发现sublime的控制台不能接受键盘输入，这点还是比较难受的。下面贴出解决办法

使用PackageResourceViewer插件打开sublime内置python下的Python.sublime-build文件, 修改
```bash
{
	"shell_cmd": "python -u \"$file\"",
	"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
	"selector": "source.python",

	"env": {"PYTHONIOENCODING": "utf-8"},

	"variants":
	[
		{
			"name": "Syntax Check",
			"shell_cmd": "python -m py_compile \"${file}\"",
		}，
		{
            "name": "Run in cmd",
            "shell_cmd": "start cmd /c \"python -u \"${file}\" & pause\"",
        }
	]
}
```

再次打开要运行的python文件，按下快捷键crtl+shift+b选择build方式，选择我们刚刚创建的Python-Run in cmd

回车后打开控制台，这时我们就可以在控制台自由输入了。

### 番外

我们学会了修改Python.sublime-build文件之后，可以解决很多事情，例如你既想在你的电脑上安装python2也想安装python3，这时候学会修改Python.sublime-build就很有用了。

> 首先我们下载python2与python3。运行安装，安装好之后重命名安装目录下的python.exe为python2.exe和python3.exe方便区分。
> 配置path环境变量。在控制台分别输入python2 -v和python3 -v验证是否正确安装。
> 再按上述步骤安装好sublime的PackageResourceViewer插件。
> Tools-->Build System-->New Build System输入下面的命令:

python2配置：
```bash
{
	"shell_cmd": "python2 -u \"$file\"",
	"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
	"selector": "source.python",

	"env": {"PYTHONIOENCODING": "utf-8"},

	"variants":
	[
		{
			"name": "Syntax Check",
			"shell_cmd": "python2 -m py_compile \"${file}\"",
		}
	]
}
```

python3配置:
```bash
{
	"shell_cmd": "python3 -u \"$file\"",
	"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
	"selector": "source.python",

	"env": {"PYTHONIOENCODING": "utf-8"},

	"variants":
	[
		{
			"name": "Syntax Check",
			"shell_cmd": "python3 -m py_compile \"${file}\"",
		}
	]
}
```

> 新建python文件，保存后，按下快捷键ctrl+shift+B选择我们需要运行的python版本配置。

题外话：空闲的时候可以多学习一下shell命令，总有一天你会发现它有很多用途......