---
layout:     post
title:      shell攻略
date:       2015-04-08 18:40:00
author:     "lemon"
categories: 编程
---

在linux系统下面编程，慢慢发现了shell scripts的重要性。使用shell脚本编程，可以让很多问题自动化，无需自己去敲繁琐的命令，大大的解放了生产力。所以对于一个linux程序员来说，脚本编程非常重要。下面将介绍一些我的学习经验，也作为自己的笔记用来时常查看。

## echo利器

最简单的使用就是用echo打印信息：

```shell
echo "Hello World!"
num=10
echo $num
echo ${#num} #返回num的长度
```

如果echo后面的内容中有转义字符，则必须加上-e选项，如：

```shell
echo -e "1\t2\t3"   #必须加上-e选项转义字符才有效
##下面是echo带颜色玩法，格式是`\e[背景色;前景色;高亮格式m`
echo -e '\e[0;33;1mHello\e[0m World'   # 带颜色的玩法
echo -e '\e[0;33;4mHello\e[0m World'   # 带颜色+下划线
echo -e '\e[0;33;5mHello\e[0m World'   # 带颜色+闪烁
```

另一个打印的命令是printf，和C语言中的printf很像，也可以带格式，但不如echo常用。

## 变量

变量让脚本有了活力，基本格式如下：

```shell
num=10
echo ${num}
```

在linux系统中，有些变量是系统启动时定义好的，它们是常用的环境变量，比如说PATH,SHELL,HOME等。

### 整数运算

```shell
echo $(( 1+1 ))        # 最简单的1+1
echo $(( (1+2)*3/4 ))  # 表达式中还可以带括号
echo $(( 1<<32 ))      # 左移右移也支持，但仅限于-4294967296~4294967296之间的数值
echo $(( 1&3 ))        # &、^、|、~ 这样的位操作亦支持
(( i=1+2 ))            # 将1+2计算出结果后赋值给i，后续若`echo ${i}`会得到3
((i++ ))              # 变量i自增1
(( i+=3 ))             # 变量i自增3
#进行整数运算的方法还有：`expr`、`$[]`、`let`等`shell`等内置命令，也可调用`bc`、`python`等外部工具进行更复杂的数学运算
```

### 字符串

替换

|操作                 |含义                       |
|---------------------|---------------------------|
|${string/old/new}    | string中第一个old替换为new|
|${string//old/new}   | string中所有old替换为new  |

示例如下：

	[lemon@ubuntu:~]# s="i hate hate you"
	[lemon@ubuntu:~]# echo ${s/hate/love}
	i love hate you
	[lemon@ubuntu:~]# echo ${s//hate/love}
	i love love you

#### 截取子串

|操作          |含义                           |
|--------------|-------------------------------|
|${string:n}   | string从下标n到结尾的子串     |
|${string:n:m} | string从下标n开始长度为m的子串|
|${string::m}  | string从下标0开始长度为m的子串|

示例如下：

	[lemon@ubuntu:~]# s="0123456789"
	[lemon@ubuntu:~]# echo ${s:3}
	3456789
	[lemon@ubuntu:~]# echo ${s::3}
	012
	[lemon@ubuntu:~]# echo ${s:0:3}
	012
	[lemon@ubuntu:~]# echo ${s:2:5}
	23456

#### 通配删除

通配删除，即按通配符，删除掉字符串中符合条件的一部分

|操作               |含义                                |
|-------------------|------------------------------------|
|${string#pattern}  | string从左到右删除pattern的最小通配|
|${string##pattern} | string从左到右删除pattern的最大通配|
|${string%pattern}  | string从右到左删除pattern的最小通配|
|${string%%pattern} | string从右到左删除pattern的最大通配|

示例如下：

	[lemon@ubuntu:~]# s="/00/01/02/dir"
	[lemon@ubuntu:~]# echo ${s#/*/}
	01/02/dir
	[lemon@ubuntu:~]# echo ${s##/*/}
	dir
	[lemon@ubuntu:~]#
	[lemon@ubuntu:~]# s="abc/cde/efg"
	[lemon@ubuntu:~]# echo ${s%/*}
	abc/cde
	[lemon@ubuntu:~]# echo ${s%%/*}
	abc
	[lemon@ubuntu:~]#
	[lemon@ubuntu:~]# s="/00/01/02/dir"
	[lemon@ubuntu:~]# echo ${s#/*/}
	01/02/dir
	[lemon@ubuntu:~]# echo ${s##/*/}
	dir
	[lemon@ubuntu:~]# s="abc/cde/efg"
	[lemon@ubuntu:~]# echo ${s%/*}
	abc/cde
	[lemon@ubuntu:~]# echo ${s%%/*}
	abc

**小技巧**

 -	获取文件名：`${path##*/}` (相当于`basename`命令的功能)
 -	获取目录名：`${path%/*}` (相当于`dirname`命令的功能)
 -	获取后缀名：`${path##*.}`

 示例如下：

	[lemon@ubuntu:~]# s="/root/test/dir/subdir/abc.txt"
	[lemon@ubuntu:~]# echo ${s##*/}
	abc.txt
	[lemon@ubuntu:~]# echo ${s%/*}
	/root/test/dir/subdir
	[lemon@ubuntu:~]# echo ${s##*.}
	txt

### 数组

普通数组：

```shell
a=()         # 空数组
a=(1 2 3)    # 元素为1,2,3的数组
echo ${#a[*]}  # 数组长度
echo ${a[2]}   # 下标为2的元素值（下标从0开始）
a[1]=0         # 给下标为1的元素赋值
```

遍历数组：

```shell
	for i in ${a[*]}
	do
		echo ${i}
	done

	unset a        # 清空数组
```

关联数组：关联数组可以用于存储`key-value`型的数据，其功能相当于`C++`中的`map`或`python`中的`dict`。

```shell
declare -A a        # 声明关联数组（必须有此句）
a=(["apple"]="a1" ["banana"]="b2" ["carrot"]="c3")   # 初始化关联数组
echo ${#a[*]}       # 获取元素个数
echo ${a["carrot"]} # 获取元素值
a["durian"]="d4"    # 插入或修改元素
echo ${!a[*]}       # 获取所有的key
unset a["banana"]   # 删除元素
```

遍历数组(仅value):

```shell
for i in ${a[*]}
do
	echo ${i}
done
```shell

遍历数组(key和value):

```shell
for key in ${!a[*]}
do
	echo "${key} ==> ${a[${key}]}"
done

unset a             # 清空数组
```

将命令执行结果存入变量:

```shell
TEMPPATH=`pwd`
```

## if语句

if语句的格式：

	#`if`、`elif`会执行它后面跟着的命令，然后看返回值是否为`0`，如果为`0`则执行`then`下面的语句块，否则执行`else`下面的语句块。
	if 判断条件
	then
		语句
	elif 判断条件
	then
		语句
	else
		语句
	fi

判断条件：true(0)，false(1)。`test`、`[ ]`、`[[ ]]`也都是`shell`中的命令，执行之后会返回`1`或`0`，而这几个命令与`if`相结合可以达到我们所需要的许多判断功能。下面将列举一下常用的判断条件。

### 文件测试

|运算符                 |描述                                    |示例                                            |
|-----------------------|----------------------------------------|------------------------------------------------|
|-e filename            | 如果 filename 存在，则为真             | [ -e /var/log/syslog ]                         |
|-d filename            | 如果 filename 为目录，则为真           | [ -d /tmp/mydir ]                              |
|-f filename            | 如果 filename 为常规文件，则为真       | [ -f /usr/bin/grep ]                           |
|-L filename            | 如果 filename 为符号链接，则为真       | [ -L /usr/bin/grep ]                           |
|-r filename            | 如果 filename 可读，则为真             | [ -r /var/log/syslog ]                         |
|-w filename            | 如果 filename 可写，则为真             | [ -w /var/mytmp.txt ]                          |
|-x filename            | 如果 filename 可执行，则为真           | [ -x /usr/bin/grep ]                           |
|filename1 -nt filename2| 如果 filename1 比 filename2 新，则为真 | [ /tmp/install/etc/services -nt /etc/services ]|
|filename1 -ot filename2| 如果 filename1 比 filename2 旧，则为真 | [ /boot/bzImage -ot arch/i386/boot/bzImage ]   |


### 字符串比较

|运算符|描述|示例|
|----|----|----|
|-z string | 如果 string 长度为零，则为真| [ -z "${myvar}" ]|
|-n string | 如果 string 长度非零，则为真| [ -n "${myvar}" ]|
|string1 = string2 | 如果 string1 与 string2 相同，则为真 | [ "${myvar}" = "abc" ]|
|string1 != string2 | 如果 string1 与 string2 不同，则为真 | [ "${myvar}" != "abc" ]|
|string1 < string | 如果 string1 小于 string2，则为真 | [ "${myvar}" < "abc" ] && [[ "${myvar}" < "abc" ]]|
|string1 > string | 如果 string1 大于 string2，则为真 | [ "${myvar}" > "abc" ] && [[ "${myvar}" > "abc" ]]|


### 整数比较

|运算符|描述|示例|
|----|----|----|
|num1 -eq num2 | 等于 |[ 3 -eq $mynum ]|
|num1 -ne num2 | 不等于 |[ 3 -ne $mynum ]|
|num1 -lt num2 | 小于 |[ 3 -lt $mynum ]|
|num1 -le num2 | 小于或等于 |[ 3 -le $mynum ]|
|num1 -ge num2 | 大于或等于 |[ 3 -ge $mynum ]|


### &&运算符

&&可以用来对两个判断语句求与

|与运算|
|------|
|if [ -n "abc" ] && [ -n "aa" ]|
|if [[ -n "abc" ]] && [[ -n "aa" ]]|
|if test -n "abc" && test -n "aa"|
|if [[ -n "abc" && -n "aa" ]]|

### ||运算符

||可以用来对两个判断语句求或

|或运算|
|------|
|if [ -n "abc" ] || [ -n "aa" ]|
|if [[ -n "abc" ]] || [[ -n "aa" ]]|
|if test -n "abc" || test -n "aa"|
|if [[ -n "abc" || -n "aa" ]]|

## 循环语句

for循环语句的句式：

```shell
for i in {1..100}
do
	echo ${i}
done

for ((i = 0; i < 100; i++))
do
	echo ${i}
done

for ((i = 0; i < 100; i+= 2))
do
	echo ${i}
done
```

while、until语句句式：

```shell
i=0
while [[ ${i} -lt 100 ]]
do
	echo ${i}
	((i++))
done

i=0
until [[ ${i} -ge 100 ]]
do
	echo ${i}
	((i++))
done
```

## 换行符处理

如果命令执行结果有多行内容，存入变量并打印时换行符会丢失：

	[lemon@ubuntu:~]# cat test.txt
	a
	b
	c
	[lemon@ubuntu:~]# CONTENT=`cat test.txt`
	[lemon@ubuntu:~]# echo ${CONTENT}
	a b c

若需要保留换行符，则在打印时必须加上""：

	[lemon@ubuntu:~]# CONTENT=`cat test.txt`
	[lemon@ubuntu:~]# echo "${CONTENT}"
	a
	b
	c

## 重定向

|名称|英文缩写|内容|默认绑定位置|文件路径|Shell中代号|
|---|---|---|---|---|---|
|标准输入流|`stdin`|程序读取的用户输入|键盘输入|`/dev/stdin`|`0`|
|标准输出流|`stdout`|程序的打印的正常信息|终端(`terminal`), 即显示器|`/dev/stdin`|`1`|
|标准错误流|`stderr`|程序的错误信息|终端(`terminal`)，, 即显示器|`/dev/stderr`|`2`|


|操作|含义|
|---|---|
|cmd > file |把 stdout 重定向到 file|
|cmd >> file|把 stdout 追加到 file|
|cmd 2> file|把 stderr 重定向到 file|
|cmd 2>> file|把 stderr 追加到 file|
| cmd &> file|把 stdout 和 stderr 重定向到 file|
|cmd > file 2>&1|把 stdout 和 stderr 重定向到 file|
|cmd >> file 2>&1|把 stdout 和 stderr 追加到 file|
|cmd <file >file2 cmd| cmd 以 file 作为 stdin，以 file2 作为 stdout|
|cat <>file|以读写的方式打开 file|
|cmd < file cmd|cmd 命令以 file 文件作为 stdin|
|cmd << delimiter Here document|从 stdin 中读入，直至遇到 delimiter 分界符|

## 管道

管道的写法为 cmd1 | cmd2，功能是依次执行cmd1和cmd2，并将cmd1的标准输出作为cmd2的标准输入，例如：

```shell
echo "1+1" | bc
```

管道可以多级拼接：cmd1 | cmd2 | cmd3 | ...，管道的返回值为最后一级命令的返回值

```shell
LINE_NO=0
cat test.txt |
while read LINE
do
	(( LINE_NO++ ))
	echo "${LINE_NO} ${LINE}"
done

# echo "${LINE_NO}"
```

## 通配符

|字符|含义|实例|
|---|---|---|
|*|匹配 0 或多个字符|`a*b` a与b之间可以有任意长度的任意字符, 也可以一个也没有, 如aabcb, axyzb, a012b, ab。|
|?|匹配任意一个字符|`a?b` a与b之间必须也只能有一个字符, 可以是任意字符, 如aab, abb, acb, a0b。|
|`[list]` |匹配 list 中的任意单一字符|`a[xyz]b` a与b之间必须也只能有一个字符, 但只能是 x 或 y 或 z, 如: axb, ayb, azb。|
|`[!list]`<br/>`[^list]` |匹配 除list 中的任意单一字符|`a[!0-9]b` a与b之间必须也只能有一个字符, 但不能是阿拉伯数字, 如axb, aab, a-b。|
|`[c1-c2]`|匹配 c1-c2 中的任意单一字符 如：[0-9] [a-z]|`a[0-9]b` 0与9之间必须也只能有一个字符 如a0b, a1b... a9b。|
|`{string1,string2,...}`|枚举sring1或string2(或更多)其一字符串|`a{abc,xyz,123}b` 展开成`aabcb axyzb a123b`|
|`{c1..c2}`<br/>`{n1..n2}`|枚举c1-c2中所有字符或n1-n2中所有数字|`{a..f}`展开成`a b c d e f`<br/>`a{1..5}` 展开成`a1 a2 a3 a4 a5`|

找网络中所有可以ping的通的服务器的IP：

```shell
for ip in 192.168.234.{1..255}
do
	ping ${ip} -w 1 &> /dev/null && echo ${ip} is Alive
done
```

