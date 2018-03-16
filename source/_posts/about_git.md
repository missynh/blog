---
title: git默认对大小写不敏感
date: 2018-03-07  
tags: git
---
 readme.md-->push-->修改为Readme.md-->push-->远程代码仓库无任何变化。因为git默认是不区分大小写的，就是意味着你的队友pull下你的代码时还是小写的首字母。
#### 解决办法
1.你将类名改成其他的名称-->push-->再修改成正确的类名-->push
2.修改git配置
查看默认配置：git config --get core.ignorecase
设置为区分大小写：git config core.ignorecase false
查看状态：git status
push至远程服务器：git push
