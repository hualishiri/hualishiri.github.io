title: 高级Bash编程（005）- 字符串和数组
date: 2014-11-21 18:34:26
categories:
- 高级Bash编程
tags:
---
##Shell字符串：
字符串可以用单引号，也可以用双引号，也可以不用引号。单双引号的区别跟PHP类似。

> 单引号字符串的限制：
> > * 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
> > * 单引号字串中不能出现单引号（对单引号使用转义符后也不行）。

> 双引号的优点：
> > * 双引号里可以有变量
> > * 双引号里可以出现转义字符
<!--more-->
拼接字符串：
```
your_name="qinjx"
greeting="hello, "$your_name" !"
greeting_1="hello, ${your_name} !"
echo $greeting $greeting_1
```

获取字符串长度：
```
string="abcd"
echo ${#string} #输出 4
```

提取子字符串
```
string="alibaba is a great company"
echo ${string:1:4} #输出liba
```

查找子字符串
```
string="alibaba is a great company"
echo \`expr index "$string" is\`
```

---

##定义数组
        bash支持一维数组（不支持多维数组），并且没有限定数组的大小。类似与C语言，数组元素的下标由0开始编号。获取数组中的元素要利用下标，下标可以是整数或算术表达式，其值应大于或等于0。

        在Shell中，用括号来表示数组，数组元素用“空格”符号分割开。定义数组的一般形式为：array_name=(value1 ... valuen)
定义数组：
```
array_name=(value0 value1 value2 value3)
```
或
```
array_name=(
value0
value1
value2
value3
)
```
或
```
array_name[0]=value0
array_name[1]=value1
array_name[2]=value2
```
读取数组：
```
 ${array_name[index]}

```
 实例：
```
 #!/bin/sh
NAME[0]="Zara"
NAME[1]="Qadir"
NAME[2]="Mahnaz"
NAME[3]="Ayan"
NAME[4]="Daisy"
echo "First Index: ${NAME[0]}"
echo "Second Index: ${NAME[1]}"

```
使用@ 或 * 可以获取数组中的所有元素，例如：
```
${array_name[*]}
${array_name[@]}
```

获取数组的长度：
```
${#array_name[*]}
${#array_name[@]}
${#array_name}
```
