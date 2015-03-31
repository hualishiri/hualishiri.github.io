title: 高级Bash编程（001）- 基础
date: 2014-11-17 18:34:26
categories:
- 高级Bash编程
tags:
---
实例：
```
#!/bin/bash
echo "Hello World !"
```
"#!" 是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种Shell。

<!--more-->

运行Shell脚本的方法：
1.作为可执行程序：
```
chmod +x ./test.sh  #使脚本具有执行权限
./test.sh  #执行脚本
```
2.作为解释器参数
```
/bin/sh test.sh
/bin/php test.php
```
这种方式运行的脚本，不需要在第一行指定解释器信息，写了也没用。
