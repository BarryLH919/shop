-**问题现象**
磁盘文件为`README.md`，Git跟踪的却是`readme.md`,`git status`显示`clean`,但在大小写敏感的系统中拉取或构建会报错。
-**根本原因**
Windows文件系统默认不区分大小写，且Git默认的`core.ignorecase`为`ture`,导致忽略了文件名大小写的变更。
-**解决方案**
1.设置`git config core.ignorecase false`强制Git识别大小写。
2.使用`git mv`规范地将历史追踪项统一修正为`README.md`。
3.提交更改后的修复配置。