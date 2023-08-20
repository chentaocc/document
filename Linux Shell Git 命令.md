## Linux Shell Git 命令

### 获取字符串长度

```shell
a="chuan"
echo ${#a}
```

### 字符串拼接

```shell
a="chuan"
b="chuan"
c="$a$b"
echo $c
```

### 字符串提取

```shell
a="chuan"
echo ${a:1:3}->hua
```

### Shell 数组

```shell
a=(A B "C" D)
echo ${a[0]}
echo ${a[1]}
echo ${a[2]}
echo ${a[3]}
echo "數組元素依次如下：${a[@]}"-> A B C D
echo "數組元素依次如下：${a[*]}"-> A B C D
echo "數組元素個數爲：${#a[@]}"-> 4
echo "數組元素個數爲：${#a[*]}"-> 4
```

### Shell算术运算符

```shell
# 使用反引号``或者$()都可以取得命令的执行结果
a=10
b=20

val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"

if [ $a == $b ]
then
   echo "a 等于 b"
fi

if [ $a != $b ]
then
   echo "a 不等于 b"
fi

#表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2。
#完整的表达式要被 两个反引号 包含，注意这个字符不是常用的单引号，在 Esc 键下边。
#乘号(*)前边必须加反斜杠(\)才能实现乘法运算
```

### Shell echo命令

```shell
echo "hello world"
echo hello world #双引号可以省略

#显示转义字符
echo "\"hello world\""->"hello world"

#显示换行
echo -e "hello\n"

#显示不换行（-e 开启转义 \c 不换行）
echo -e "chuan! \c"

#显示结果重定向至文件
echo "hello world" > test7.sh

#显示执行时间
echo `date`
```

### Shell test 数值

```shell
# -eq 等于则为真
# -ne 不等于则为真
# -gt 大于则为真
# -ge 大于等于则为真
# -lt 小于则为真
# -le 小于等于则为真
# test 和 [] 都是命令，可以等价替换

#三种写法都可以，建议都使用"$num1"
$num1
$[num1]
"$num1"

num1=200
num2=200
if test $[num1] -eq $[num2] #也可以写成 if [ $[num1] -eq $[num2] ]
then
    echo '两个数相等！'
fi

#[]执行基本的算数运算
a=10
b=10
result=$[a+b] #注意等号两边不能有空格
echo "$result"
```

### Shell test 字符串

```shell
# =   等于则为真
# !=  不相等则为真
# -z 字符串  字符串的长度为零则为真
# -n 字符串  字符串的长度不为零则为真
# -o 使用 -o 取代||
# -a 使用 -a 取代&&
# test 和 [] 都是命令，可以等价替换

str1="chuan"
str2="chuan"
#检测字符串是否为空
if [ -z "$str1" ] || [ -z "$str2" ] #if [ -z "$str1" -o -z "$str2" ]
then
    echo "字符串长度为0"
fi
#比较字符串
if [ $str1 = $str2 ]
then
    echo "两个字符串相等"
fi
```

### Shell test 文件

```shell
# -e 文件名  如果文件存在则为真
# -w 文件名  如果文件存在且可写则为真
# -s 文件名  如果文件存在且至少有一个字符则为真
# -d 文件名  如果文件存在且为目录则为真

if test -e ./test1.sh
then
    echo '文件已存在!'
else
    echo '文件不存在!'
fi
```

### Shell [[]]

```shell
# [[ ]] 是 Shell 内置关键字，不是命令，支持逻辑运算符；[]是命令，不支持逻辑运算符，但是支持-o -a
# 不需要把变量名用双引号""包围起来，即使变量是空值，也不会出错
# 不需要、也不能对 >、< 进行转义，转义后会出错
# 剔除了 test 命令的-o和-a选项，你只能使用 || 和 &&

 []和[[ ]]
[ -z "$str1" ] || [ -z "$str2" ]	√	 [[ -z $str1 ]] || [[ -z $str2 ]]	√
[ -z "$str1" -o -z "$str2" ]	√	 [[ -z $str1 -o -z $str2 ]]	×
[ -z $str1 || -z $str2 ]	×	 [[ -z $str1 || -z $str2 ]]	√
```

### if 语句

```shell
if  condition
then
    statement(s)
fi

#注意 condition 后边的分号;，当 if 和 then 位于同一行的时候，这个分号是必须的
if  condition;  then
    statement(s)
fi

#if else 语句
if  condition
then
   statement1
else
   statement2
fi

#if elif else 语句
if  condition1
then
   statement1
elif condition2
then
    statement2
elif condition3
then
    statement3
……
else
   statementn
fi

```

### While语句

```shell
while condition
do
    statements
done
```

### for语句

```shell
sum=0
for ((i=1; i<=100; i++))
do
    ((sum += i))
done
echo "The sum is: $sum"

# 数字
sum=0
for n in 1 2 3 4 5 6
do
    echo $n
     ((sum+=n))
done
echo "The sum is "$sum

# 字符串
for str in "C语言中文网" "http://c.biancheng.net/" "成立7年了" "日IP数万"
do
    echo $str
done

# 给出一个取值范围
sum=0
for n in {1..100}
do
    ((sum+=n))
done
```

### Shell特殊变量

```shell
$0	当前脚本的文件名。
$n（n≥1）	传递给脚本或函数的参数，n 是一个数字，表示第几个参数，例如，第一个参数是 $1，第二个参数是 $2。
$#	传递给脚本或函数的参数个数。
$*	传递给脚本或函数的所有参数。
$@	传递给脚本或函数的所有参数。当被双引号" "包含时，$@ 与 $* 稍有不同。
$$	当前 Shell 进程 ID。
```

### Shell 输入重定向

```shell
# Linux 中一切皆文件，包括标准输入设备（键盘）和标准输出设备（显示器）在内的所有计算机硬件都是文件。
# 为了表示和区分已经打开的文件，Linux 会给每个文件分配一个 ID，这个 ID 就是一个整数，被称为文件描述符（File Descriptor）。
# stdin、stdout、stderr 默认都是打开的，在重定向的过程中，0、1、2 这三个文件描述符可以直接使用
# 符号前面的数字不能加空格，如command 2 >file（错误）

#标准输出重定向
command >file	以覆盖的方式，把 command 的正确输出结果输出到 file 文件中。
command >>file	以追加的方式，把 command 的正确输出结果输出到 file 文件中。

# 标准错误输出重定向
command 2>file	以覆盖的方式，把 command 的错误信息输出到 file 文件中。
command 2>>file	以追加的方式，把 command 的错误信息输出到 file 文件中。

# 正确输出和错误信息同时保存
command >file 2>&1	以覆盖的方式，把正确输出和错误信息同时保存到同一个文件（file）中。
command >>file 2>&1	以追加的方式，把正确输出和错误信息同时保存到同一个文件（file）中。
command >file1 2>file2	以覆盖的方式，把正确的输出结果输出到 file1 文件中，把错误信息输出到 file2 文件中。
command >>file1 2>>file2	以追加的方式，把正确的输出结果输出到 file1 文件中，把错误信息输出到 file2 文件中。

for str in "C语言中文网" "http://c.biancheng.net/" "成立7年了" "日IP数万"
do
    echo $str >>demo.txt  #将输入结果以追加的方式重定向到文件
done
```

### Shell 输入重定向

```shell
# command <file	将 file 文件中的内容作为 command 的输入。
# command <<END	从标准输入（键盘）中读取数据，直到遇见分界符 END 才停止。

# 逐行读取文件内容
while read str; do
    echo $str
done <readme.txt

```

### ls 命令

```shell
# 用于显示指定工作目录下之内容
ls -l                    # 以长格式显示当前目录中的文件和目录
ls -a                    # 显示当前目录中的所有文件和目录，包括隐藏文件

# 当文件名包含空格、特殊字符或者开始字符为破折号时，可以使用反斜杠（\）进行转义
ls my\ file.txt     # 列出文件名为"my file.txt"的文件

# 使用通配符进行模式匹配，例如 * 表示匹配任意字符，? 表示匹配一个字符，[...] 表示匹配指定范围内的字符
ls *.txt         # 列出所有扩展名为.txt的文件
ls file?.txt     # 列出文件名为file?.txt的文件，其中?表示任意一个字符
ls [abc]*.txt    # 列出以a、b或c开头、扩展名为.txt的文件
```

### head 命令

```shell
# head 命令可用于查看文件的开头部分的内容，有一个常用的参数 -n 用于显示行数，默认为 10
head -n 5 runoob_notes.log
```

### tail 命令

```shell
# -n<行数> 显示文件的尾部 n 行内容，默认显示最后 10 行
# -f 循环读取
# tail -f filename 会把 filename 文件里的最尾部的内容显示在屏幕上，并且不断刷新，只要 filename 更新就可以看到最新的文件内容
tail -f notes.log
```

### du 命令

```shell
# du 会显示指定的目录或文件所占用的磁盘空间
# -a或-all 显示目录中个别文件的大小
# -h 以K，M，G为单位，提高信息的可读性
# du -h test
608K    test/test6
308K    test/test4
4.0K    test/scf/lib
4.0K    test/scf/service/deploy/product
4.0K    test/scf/service/deploy/info
12K     test/scf/service/deploy
16K     test/scf/service
4.0K    test/scf/doc
4.0K    test/scf/bin
32K     test/scf
8.0K    test/test3
1.3M    test
```

### df 命令

```shell
# Linux df（英文全拼：disk free） 命令用于显示目前在 Linux 系统上的文件系统磁盘使用情况统计
# 文件-h, 使用人类可读的格式
# df -h 
Filesystem      Size  Used   Avail Use% Mounted on 
/dev/sda6       29G   4.2G   23G   16%     / 
udev            1.5G  4.0K   1.5G   1%     /dev 
tmpfs           604M  892K   603M   1%     /run 
none            5.0M     0   5.0M   0%     /run/lock 
none            1.5G  156K   1.5G   1%     /run/shm 
```

### mount 命令

```shell
# 将 /dev/hda1 挂在 /mnt 之下
mount /dev/hda1 /mnt

# 将 /dev/hda1 用可读写模式挂在 /mnt 之下
mount -o rw /dev/hda1 /mnt
```

### export 命令

```shell
# 用于设置或显示环境变量
# 语法：export [-fnp][变量名称]=[变量设置值]
# -p 　列出所有的shell赋予程序的环境变量
export -p
# 定义环境变量赋值
export MYENV=7
```

### wc 命令

```shell
# Linux wc命令用于计算字数
# 显示行数
wc -l file
```

### Git 命令

![img](https://www.runoob.com/wp-content/uploads/2015/02/011500266295799.jpg)

### vim 命令

三种模式：

- 命令模式（Command Mode）
- 输入模式（Insert Mode）
- 命令行模式（Command-Line Mode）：输入：:! + 命令（比如:!cat test.sh）

![img](https://www.runoob.com/wp-content/uploads/2015/10/vi-vim-cheat-sheet-sch.gif)