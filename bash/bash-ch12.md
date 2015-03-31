title: 高级Bash编程（012）- 常用linux命令
date: 2014-11-28 18:34:26
categories:
- 高级Bash编程
tags:
---
####单横杠（-）和双横杠（--）区别
    命令行选项可以分为短命令行选项和长命令行选项两种。短命令行选项是由字母组成，长命令行选项是由单词组成。短命令行选项在选项前使用单横杠“-”，长命令行选项前使用双横杠“--”。如果选项后面需要输入选项的参数，短命令行选项和参数之间使用空格分隔，而长命令行选项使用等号“=”连接选项和参数。

---

####CPU相关的命令
```
uptime  ;系统运行时间，平均负载
ps      ;Pocess Status每个进程所占用CPU百分比
top/atop/htop   ;和ps功能相同，但可以了解CPU消耗，根据用户指定的时间来更新
mpstat          ;可以查看所有的CPU，也可以查看单个CPU的消耗
pidstat         ;显示某个进程的状态，消耗时间等
```
<!--more-->
####内存相关的命令
```
free        ;查看内存的总数，已使用，空闲内存数，swap使用
            ;[Mem][total] 总内存大小
            ;[Mem][used] OS已使用内存（从OS的角度）
            ;[Mem][free] OS剩余可用的内存（从OS的角度）
            ;[Mem][shared] 共享的内存大小（从OS的角度）
            ;[Mem][buffered] 内存中作为Buffer的大小（从OS的角度，并且该内存已经计入used了）
            ;[Mem][cached] 内存中作为Cache的大小（从OS的角度，并且该内存已经计入used了）
            ;[Mem][total] = [Mem][used] + [Mem][free]
            ;为何free显示的小，内存都用到cached和buffers上了，但是可以随时拿过来
            ;[+/-/b-c][used] 已经用掉多少内存了（从应用程序的角度），
            ;[+/-/b/c][used] = [Mem][used] - [Mem][Buffered] - [Mem][Cached]
            ;[+/-/b/c][free]可用内存（从应用程序的角度） = [Mem][free] + [Mem][Buffer] + [Mem][Cache]
```
http://www.cnblogs.com/coldplayerest/archive/2010/02/20/1669949.html
```
vmstat      ;监控虚拟内存的使用情况，空闲内存，空闲内存，缓冲，cache等指标
```

####磁盘IO相关
```
iostat      ;获取每秒读写的数据块数，所有读写块对磁盘读写性能有个大致了解
fstat       ;io压力测试工具
swapon      ;查看swap设备的使用情况
```

####网络IO相关
```
netstat     ;是一个监控TCP/IP网络的非常有用的工具，它可以显示路由表、实际的网络连接以及每一个网络接口设备的状态信息
tcpdump     ;用于监视TCP/IP连接并直接读取数据链路层的数据包头。您可以指定哪些数据包被监视、哪些控制要显示格式
route       ;可以为ifconfig命令配置的网卡设置静态路由，在本地 IP 路由表中显示和修改条目网络命令
iptarf      ;用于查看本机网络的吞吐量，获得网络传输速率；
netperf     ;可以模拟服务器和客户端网络收发，测试网络吞吐量大小
iperf       ;模拟服务器和客户端网络收发，测试最大TCP和UDP带宽性能，能够提供网络吞吐率信息, 以及震动、丢包率、最大段和最大传输单元大小等统计
picstat     ;监控网络接口的状态，如吞吐量
ping/traceroute;查看网络是否畅通

```
####开发测试相关
```
readelf     ;以可读方式展示elf文件格式，包括（目标文件/可执行文件/共享库）
hexdump/xxd ;文件16进制显示
od          ;可选进制打印文件
objdump     ;将机器指令反汇编
ldd         ;列出目标所有的依赖
nm          ;列出目标文件的symbols
gprof       ;每个函数的调用次数，每个函数消耗的处理器时间。也可以显示“调用图”，包括函数的调用关系，每个函数调用花费了多少时间
oprofile    ;进入内核进行相关的性能分析
telnet/nc   ;测试网络连接客户端

```

####跟踪调试相关
```
strace      ;(system)跟踪运行进程的系统调用耗费时间、出错信息、参数传递等
ltrace      ;(library)跟踪运行进程的函数库调用耗费时间、出错信息、参数传递等
dtrack/ftrace      ;上述两个工具的综合
blktrace    ;IO事件跟踪
valgrind     ;内存泄漏检测（动态）
beam         ;内存泄漏检测（静态）
cat /proc/pid/ ;查询某个进程的详细信息
```

```
ls	[option] [path]
	-R,递归选项
	-S,按照文件大小列出所有文件
	-t,按照修改时间列出文件
	-i,显示文件的inode
```
```
cat,tac	显示文件的内容
	-n,前边加入行号显示
	-b,前边加入行号显示，不计空行
	-v,将不可打印使用^标记出来
```
```
rev	把每一行的内容反转，输出到stdout
```
```
cp 拷贝文件
	-r，递归
	-R,递归

mv 移动文件
	-f,强制移动文件
	
rm 删除文件
	-f,强制删除文件
	-r,递归删除文件或文件夹
```
```
rmdir	删除文件夹
mkdir	创佳一个目录
```
```
chmod	修改文件的属性

ln	为文件创建链接
	-s,创建符号链接
```
```
find path -option [ -print ] [ -exec -ok commond ] {} \;
	path:find所查找的当前目录,.表示当前目录，/表示根目录
	-print	;将查找到的文件输出到标注输出
	-exec	;对匹配的文件执行该参数所给出的shell命令
	-ok		;和-exec相同，不过每次都需要用户来确认
	
	-name filename	;查找名为filename的文件，必须加引号
	-user username	;按照属主来查找文件
	-group groupname;按照群组来查找
	-type b/d/c/p/l/f 	;查找快设备，目录，字符设备，管道，符号链接和普通文件
	-perm	;按执行权限来查找
	
	;-n指n天以内，+n指n天之前
	-mtime -n|+n 	;按文件修改（内容修改）的时间来查找文件（单位：天）
	-atime -n|+n 	;按文件访问的时间来查找文件（单位：天）
	-ctime -n|+n 	;按文件变更（权限，属主修改）的时间来查找文件（单位：天）
	-amin -n|+n     ;同上，单位：分钟
	-cmin -n|+n     ;同上，单位：分钟
	-mmin -n|+n     ;同上，单位：分钟
	-nouser 		;无效属主的文件
	-nogroup 		;无效属组的文件
	-size  n[c] 	;查找长度为n块或者n字节的文件
	-prune 			;忽略某个目录
	-follow 		;如果遇到符号链接文件，则跟踪
	-mount 			;查找文件时不跨越文件系统的mount点

find . -mtime -10   ;10天之内修改的文件
find . -mtime +10   ;10天之前修改的文件
find . -mtime +6 -mtime -10     ;6天前到10天内修改的文件,第二个mtime不能省略
```
```
w 显示所有登录用户的详细信息
	-h	;忽略头文件信息

who	查看用户名和所启动的进程
users	打印所有的用户名（类似whoami)
whoami	打印当前用户名
last [username]	显示所有登录过的用户名

```
```
xargs	读入 stdin 的资料以空白字元或断行字元作为分辨，将 stdin 的资料分隔成为 arguments 
	-a file 	;从文件读入作为stdin
	-e flag 	;xargs解析到flag则停止
	-p 			;每执行一次则询问用户一次
	-n num 		;命令执行一次用的arguments的个数，默认是所有
	-i 			;命令替换将xargs的每项名称，一般是一行一行赋值给{}，可以用{}代替

```
```
date	查看日期
zdump 	查看特定时区的当前时间
time [commond]	统计命令执行的时间
touch	更改文件被存取或修改的时间工具
```

```
sleep num[s|m|h|d]	延迟num秒，分钟，小时，天
usleep num 	微妙级别的延迟
```
```
watch [option] commond 	周期的执行命令
	-n interval;	执行命令的间隔（默认s2)
	-d 	;			高亮显示变化的地方
```
```
at
	:at [option] [date]

	-m 	;当任务执行完之后，给用户发送email
	-I 	;atq的别名
	-d 	;atrm的别名
	-v 	;显示任务将被执行的时间
```
```
sort [option] [源文件] [-o 输出文件]
	-b 	;忽略每行前边的空格字符
	-c 	;检查文件是否已经按照顺序排序
	-f 	;忽略大小写
	-M 	;将前面3个字母依照月份的缩写进行排序
	-n 	;按照数值的大小进行排序；
	-o 	;将排序后的输入指定文件
	-r 	;以相反的顺序来排序
	-t 	;执行排序时所用的栏位分割字符
	-k 	;选择哪个区间进行排序
	-u 	;将重复的行去掉
```
```
uniq 	删除一个已排序的重复行
```
```
expand 	将每个tab转换成空格
unexpand 将每个空格转换成tab

cut [option] [file] 选取命令，将一段数据进行分析，取出我们想要的
	-b 	;将文件以字节分割
	-c 	;将文件以字符分割（中英差别）
	-d 	;自定义分割符，默认为制表符
	-f 	;指定显示那个域，与-d一起使用
	-n 	;取消分割多字节字符
```
```
paste [option] [file1 file2] 将按行将不同文件行信息放在一行，确保两个文件行数相同
	-d 	;指定不用于空格或tab的分隔符
	-f 	;将每个文件合并而不是按行进行粘贴

```
```
head [option] [file] 	显示文件的前10行
	-q 	;隐藏文件名
	-v	;显示文件名
	-c num 	;	显示每行的钱num个字符数
	-n num 	;	显示前num行

tail [option] [file] 	显示文件的后10行
	-f 	;循环读取
	-q 	;不显示处理信息
	-c num	;显示字节数
	-n num 	;显示最后num行
	-r 	;逆序显示
```
```
grep pattern [file] 在文件中搜索
	-i 	;搜索时忽略大小写
	-w 	;匹配整个单词
```
```
wc [option] [file] 	对文件进行技术
	-w 	;统计单词数量
	-l 	;统计行数量
	-c 	;统计字节数量
	-m 	;统计字符数量
	-L 	;给出文件最长行的长度
```
```
fold [-bs][-w num] [file] 限制文件列宽 
	-b 	;以字节计算列宽
	-s 	;以空格字符作为换列点
	-w num 	;每列的行数
```
```
iconv [option] [file]
	-f from-code 	;输入文件的编码
	-t to-code 		;输出文件的编码
	-l 				;列出所有支持的编码
	-o 	file ;指定输出文件

```
```
tar [option] [file | directory]
	主选项必须选一个
	-c 	;压缩
	-x 	;解压
	-t 	;查看
	-r 	;追加
	-u 	;更新
	辅选项
	-z 	;是否需要用gzip压缩或解压,一般格式为xx.tar.gz或xx.tgz
	-j 	;是否需要用bzip2压缩或解压，一般格式为xx.tar.bz2
	-v 	;压缩过程中显示文件！
	-f 	;使用档名，在f之后立即接档名，不要在加入其他参数
	--exclude FILE 	;在压缩过程中，不要将FILE打包

	tar -cvf xx.tar /dire 	对文件夹/dire仅打包
	tar -zcvf xx.tar.gz /dir 对文件/dire打包，并且以gzip进行压缩
	tar -jcvf xx.tar.bz2 /dire 对文件/dire打包，并且以bzip2进行压缩

	tar -tvf xx.tar	查看
	tar -ztvf xx.tar.gz 查看
	tar -jtvf xx.tar.bz2 查看

	tar -xvf xx.tar 解压
	tar -zxvf xx.tar.gz 解压
	tar -jxvf xx.tar.bz2 解压
```
```
rpm [option] [file]
	-i 	;install，安装
	-v 	;verbose,提供更多的安装信息
	-h 	;hash,打印包安装的hash码
	-e 	;earse,卸载包
	-a 	;all
	-U 	;Update
	--test 	;安装测试，并不实际安装

	-ivh 	;安装并显示进度
	-Uvh 	;升级软件包
	-qpl 	;列出RPM软件包内的文件信息
	-qpi 	;列出RPM软件包的描述信息
	-qf 	;查找指定文件属于哪个RPM软件包
	-Va 	;校验所有的RPM包，查找丢失的文件
	-e 		;删除包
	-q [name] 	;查找name程序是否安装
```
```
gzip 	压缩文件
bzip2 	压缩文件
zip/unzip 压缩/解压
```
```
which [commond] 	准确定位一个命令的完整路径
whereis [commond]	详细给出一个命令的详解
whatis [commond] 	相当于man
```
```
readlink [file] 	显示符号链接所指向文件
```
```
basename 	文件名中去掉路径信息
dirname 	带路径的文件名去掉文件名，只打印路径信息
```

```
cksum [file] 	产生ck哈希码
md5sum [file] 	产生md5哈希码
sha1sum [file]	产生sha1哈希码
```
```
more [-dlfpcsu] [-num] [+/ patter] [file] 	回显命令【仅能先前显示】
	+n 	从第n行开始显示
	-n 定义屏幕大小为n行
	+/pattern 	在每个档案显示搜索该字符串
	-c 	;从顶部清屏，然后显示
```

```
less [option] [file] 【可以前后显示文件】
	/字符串：向下搜索“字符串”的功能
	?字符串：向上搜索“字符串”的功能
	
	-e 	;当文件结束后，自动离开
	-i 	;搜索时忽略大小写
	-N 	;显示每行的行号

	操作命令
	d 	;向后翻半页
	u 	;想钱翻半页
	f 	;翻一页
	b 	;后翻一页
	e 	;翻一行
	y 	;后翻一行

```
```
nslookup [-option] [ip/host] [server] 	给出ip或host所指的主机的详细信息
dig DSN协议查询
	-4 	;	IPv4协议
	-6 	; 	IPv6协议
	-x 	; 	IP到域名的映射管理
	+trace 	;从根域名查询一直跟踪直到查询到最终结果，并将整个结果输出来。
	+short 	;精简输出记录
traceroute [option] [ip/hostname] 	跟踪包发送到远端主机过程中的路由信息.
	-m 设置检测数据包的最大存活数值TTL的大小。
	-n 直接使用IP地址而非主机名称
	-s 设置本地主机送出数据包的IP地址。
	-v 详细显示指令的执行过程。
	-w 设置等待远端主机回报的时间。
ping [hostname] 	广播一个 "ICMP ECHO_REQUEST" 包到其他主机上

```
```
ssh
	-l username ;指定用户名
	-p port 	;指定端口

ssh -t hostA ssh hostB	直接连接到只能通过主机B连接的主机A
ssh-copy-id
```

```
scp [option] src_file dst_file
	-v 	;显示进度，连接
	-P port	;选择端口，ssh的端口
	-r 	;复制时，包括文件夹本身
	-C 	;压缩选项
	-4 	;强制使用IPv4
	-6 	;
```

```
mail [option] email
	-s 	;邮件的标题
	echo "content" | mail -s title email
	mail -s title email < file
```
```
netstat [option]	显示本机的所有链接
	-a (all)显示所有选项，默认不显示LISTEN相关
	-t (tcp)仅显示tcp相关选项
	-u (udp)仅显示udp相关选项
	-n 拒绝显示别名，能显示数字的全部转化成数字。
	-l 仅列出有在 Listen (监听) 的服務状态
	-p 显示建立相关链接的程序名
	-r 显示路由信息，路由表
	-e 显示扩展信息，例如uid等
	-s 按各个协议进行统计
	-c 每隔一个固定时间，执行该netstat命令。
```
```
seq [num]	产生一系列数字
```
```
yes 	一直产生yes
```
```
mcookie	产生临时文件名
```
```
echo "nameserver 192.168.0.6" | /etc/resolv.conf  暂时修改DNS
service network restart 重启网卡

```
```
getopt 	参数处理命令
getopts 尤其在shell中使用
echo 回显
read 读取用户的输入
cd 	更改当目录
pwd 打印当前目录
dirs popd pushd 目录管理
let 可以使用C语言风格
source [script]	当前bash环境下读取并执行FileName中的命令,而不是像shell建一个新的进程执行
```
```
jobs	显示当前的作业
	-l 	;列出的当前的作业
commond & 	;后台运行程序，你关掉终端会停止运行
Ctrl+Z 暂停当前执行的任务
nohup commond & 	;后台运行，你关掉终端之后继续运行
fg %jobnumber 	将后台任务拿到前台处理
bg %jobnumber 	将任务放到后台中去处理（退出终端，程序死亡）
```

```
dc (desk calculator) 计算器
    c(clear):清空栈
    p（print）:打印栈顶元素
    f(full）：打印全栈元素
    d（double）：将栈顶元素复制，压栈
    r（）：交换栈顶两个元素
```
