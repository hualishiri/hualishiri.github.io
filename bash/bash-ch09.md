title: 高级Bash编程（009）- 函数
date: 2014-11-25 18:34:26
categories:
- 高级Bash编程
tags:
---
##函数定义：
语法：
```
function_name () {
    list of commands
    [ return value ]
}
```
或
<!--more-->
```
function function_name () {
    list of commands
    [ return value ]
}
```
> 1. 函数返回值，可以显式增加return语句；如果不加，会将最后一条命令运行结果作为返回值。
> 1. Shell 函数返回值只能是整数，一般用来表示函数执行成功与否，0表示成功，其他值表示失败。如果 return 其他数据，比如一个字符串，往往会得到错误提示：“numeric argument required”。
> 1. 如果一定要让函数返回字符串，那么可以先定义一个变量，用来接收函数的计算结果，脚本在需要的时候访问这个变量来获得函数返回值。

---
实例：
```
#!/bin/bash
# Define your function here
Hello () {
   echo "Url is http://see.xidian.edu.cn/cpp/shell/"
}
# Invoke your function
Hello
```

实例：
```
#!/bin/bash
funWithReturn(){
    echo "The function is to get the sum of two numbers..."
    echo -n "Input first number: "
    read aNum
    echo -n "Input another number: "
    read anotherNum
    echo "The two numbers are $aNum and $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
# Capture value returnd by last command
ret=$?
echo "The sum of two numbers is $ret !"
```
结果：
```
The function is to get the sum of two numbers...
Input first number: 25
Input another number: 50
The two numbers are 25 and 50 !
The sum of two numbers is 75 !
```
---
实例：
```
#!/bin/bash
# Calling one function from another
number_one () {
   echo "Url_1 is http://see.xidian.edu.cn/cpp/shell/"
   number_two
}
number_two () {
   echo "Url_2 is http://see.xidian.edu.cn/cpp/u/xitong/"
}
number_one
```
结果：
```
Url_1 is http://see.xidian.edu.cn/cpp/shell/
Url_2 is http://see.xidian.edu.cn/cpp/u/xitong/
```
---
##删除函数：
```
$unset .f function_name
```
像删除变量一样，删除函数也可以使用 unset 命令，不过要加上 .f 选项

        如果你希望直接从终端调用函数，可以将函数定义在主目录下的 .profile 文件，这样每次登录后，在命令提示符后面输入函数名字就可以立即调用。

---
##函数参数：
实例：
```
#!/bin/bash
funWithParam(){
    echo "The value of the first parameter is $1 !"
    echo "The value of the second parameter is $2 !"
    echo "The value of the tenth parameter is $10 !"
    echo "The value of the tenth parameter is ${10} !"
    echo "The value of the eleventh parameter is ${11} !"
    echo "The amount of the parameters is $# !"  # 参数个数
    echo "The string of the parameters is $* !"  # 传递给函数的所有参数
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```
结果：
```
The value of the first parameter is 1 !
The value of the second parameter is 2 !
The value of the tenth parameter is 10 !
The value of the tenth parameter is 34 !
The value of the eleventh parameter is 73 !
The amount of the parameters is 12 !
The string of the parameters is 1 2 3 4 5 6 7 8 9 34 73 !"
```
>注意，`\$10` 不能获取第十个参数，获取第十个参数需要`\${10}`。当n>=10时，需要使用`\${n}`来获取参数。
特殊变量	 说明
>> * `\$#`	 传递给函数的参数个数。
>> * `\$*`	 显示所有传递给函数的参数。
>> * `\$@`	 与\$*相同，但是略有区别，请查看Shell特殊变量。
>> * `\$?`	 函数的返回值。

