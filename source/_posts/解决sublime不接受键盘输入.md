---
title: 解决sublime不接受键盘键入
date: 2017-08-08
tags: [sublime，PackageResourceViewer]
---

插件修改神器PackageResourceViewer
### 安装
使用package control安装，快捷键按下ctrl+shift+p-->install package-->输入PackageResourceViewer 回车安装

### 使用
ctrl+shift+p-->输入open Resource-->即可看到所有的sublime插件列表-->继续打开可以看到所有文件内容

### 实例
sublime功能十分完善，但是我使用此软件学习python时，接触到他的控制台不能接受键盘输入，这点还是比较难受的。下面贴出解决办法

使用PackageResourceViewer插件打开sublime内置python下的Python.sublime-build文件
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
		}
	]
}
```

新建file，复制Python.sublime-build内容，替换variants

```bash
{
	"shell_cmd": "python -u \"$file\"",
	"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
	"selector": "source.python",

	"env": {"PYTHONIOENCODING": "utf-8"},

	"variants":
	[
       {
            "name": "Run in cmd",
            "shell_cmd": "start cmd /c \"python -u \"${file}\" & pause\"",
        }
	]
}
```

保存至Python.sublime-build同级目录下，取名为Python3.sublime-build

再次打开要运行的python文件，按下快捷键crtl+shift+b选择build方式，选择我们刚刚创建的Python3-Run in cmd

回车后打开控制台，这时我们就可以在控制台自由输入了。