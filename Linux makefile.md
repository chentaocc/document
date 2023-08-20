## Linux Makefile

make是一个命令工具，是一个解释makefile中指令的命令工具，makefile带来的好处就是——“自动化编译”，一旦写好，只需要一个make命令，整个工程完全自动编译，极大的提高了软件开发的效率。

### 命名

- 所有字母都小写：makefile
- 首字母大写，其余都小写：Makefile

### 规则

Makefile的框架是由规则构成的，每条规则由三个部分组成分别是目标(target)，依赖(depend)和命令(command)。

- 目标(target)：可以有多个目标
- 依赖(depend)：可以有多个依赖
- 命令(command)：一般情况下这个动作就是一个 shell 命令，每个命令前必须有一个***Tab缩进***并且独占占一行

```makefile
# 举例: 有源文件 a.c b.c c.c head.h, 需要生成可执行程序 app
################# 例1 #################
app:a.c b.c c.c
	gcc a.c b.c c.c -o app

################# 例2 #################
# 有多个目标, 多个依赖, 多个命令
app,app1:a.c b.c c.c d.c
	gcc a.c b.c -o app
	gcc c.c d.c -o app1
	
################# 例3 #################	
# 规则之间的嵌套
app:a.o b.o c.o
	gcc a.o b.o c.o -o app
# a.o 是第一条规则中的依赖
a.o:a.c
	gcc -c a.c
# b.o 是第一条规则中的依赖
b.o:b.c
	gcc -c b.c
# c.o 是第一条规则中的依赖
c.o:c.c
	gcc -c c.c
```

### 工作原理

#### 规则的执行

- 在调用 make 命令编译程序的时候，make 会首先找到 Makefile 文件中的第 1 个规则，分析并执行相关的动作，直到makefile中的第一条规则中的所有的依赖全部被生成，第一条规则中的命令就可以基于这些依赖生成对应的目标，make 的任务也就完成了。
- 如果想要执行 makefile 中非第一条规则对应的命令, 那么就不能直接 make，需要将那条规则的目标也写到 make的后边，比如只需要执行规则3中的命令，就需要：make b.o（make + target）

```makefile
# makefile
# 规则之间的嵌套
# 规则1
app:a.o b.o c.o
	gcc a.o b.o c.o -o app
# 规则2
a.o:a.c
	gcc -c a.c
# 规则3
b.o:b.c
	gcc -c b.c
# 规则4
c.o:c.c
	gcc -c c.c
```

#### 文件的时间戳

make 命令执行的时候会根据文件的时间戳判定是否执行makefile文件中相关规则中的命令。

- 目标是通过依赖生成的，因此正常情况下：目标时间戳 > 所有依赖的时间戳，如果执行 make 命令的时候检测到规则中的目标和依赖满足这个条件，那么规则中的命令就不会被执行。

- 当依赖文件被更新了，文件时间戳也会随之被更新，这时候 目标时间戳 < 某些依赖的时间戳, 在这种情况下目标文件会通过规则中的命令被重新生成。
- 如果规则中的目标对应的文件根本就不存在， 那么规则中的命令肯定会被执行。

### 自动推导

 make 进行编译的时候会使用一个默认的编译规则，按照默认规则完成对.c文件的编译，生成对应的.o 文件。它使用命令cc -c来编译.c 源文件。在 Makefile 中只要给出需要构建的目标文件名（一个.o 文件），make 会自动为这个.o 文件寻找合适的依赖文件（对应的.c 文件），并且使用默认的命令来构建这个目标文件。

```makefile
# 这是一个完整的 makefile 文件，缺点是修改一个文件都需要全部重新编译，效率低
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
```

```makefile
# 优点是哪个文件修改，只重新编译对应的目标，效率高
$ make
cc    -c -o add.o add.c
cc    -c -o div.o div.c
cc    -c -o main.o main.c
cc    -c -o mult.o mult.c
cc    -c -o sub.o sub.c
gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
```

### 变量

#### 自定义变量

makefile 中的变量是没有类型的，直接创建变量然后给其赋值就可以了。

```makefile
# 错误, 只创建了变量名, 没有赋值
变量名 
# 正确, 创建一个变量名并且给其赋值
变量名=变量值
```

```makefile
# 如果将变量的值取出?
$(变量的名字)

# 举例 add.o  div.o  main.o  mult.o  sub.o
# 定义变量并赋值
obj=add.o  div.o  main.o  mult.o  sub.o
# 取变量的值
$(obj)
```

```makefile
# 这是一个规则，普通写法
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
        
# 这是一个规则，里边使用了自定义变量
obj=add.o  div.o  main.o  mult.o  sub.o
target=calc
$(target):$(obj)
        gcc  $(obj) -o $(target)
```

#### 预定义变量

![image-20230820142613131](/Users/chentao/Library/Application Support/typora-user-images/image-20230820142613131.png)

```makefile
# 这是一个规则，普通写法
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
        
# 这是一个规则，里边使用了自定义变量和预定义变量
obj=add.o  div.o  main.o  mult.o  sub.o
target=calc
CFLAGS=-O3 # 代码优化
$(target):$(obj)
        $(CC)  $(obj) -o $(target) $(CFLAGS)
```

#### 自动变量

Makefile 中的规则语句中经常会出现目标文件和依赖文件，自动变量用来代表这些规则中的目标文件和依赖文件，并且它们只能在***规则的命令中***使用。

![image-20230820143639513](/Users/chentao/Library/Application Support/typora-user-images/image-20230820143639513.png)

```makefile
# 这是一个规则，普通写法
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
        
# 这是一个规则，里边使用了自定义变量
# 使用自动变量, 替换相关的内容
calc:add.o  div.o  main.o  mult.o  sub.o
	gcc $^ -o $@ 			# 自动变量只能在规则的命令中使用
```

### 模式匹配

```makefile
calc:add.o  div.o  main.o  mult.o  sub.o
        gcc  add.o  div.o  main.o  mult.o  sub.o -o calc
# 语法格式重复的规则, 将 .c -> .o, 使用的命令都是一样的 gcc *.c -c
add.o:add.c
        gcc add.c -c

div.o:div.c
        gcc div.c -c

main.o:main.c
        gcc main.c -c

sub.o:sub.c
        gcc sub.c -c

mult.o:mult.c
        gcc mult.c -c
```

```makefile
# 模式匹配 -> 通过一个公式, 代表若干个满足条件的规则
# 依赖有一个, 后缀为.c, 生成的目标是一个 .o 的文件, % 是一个通配符, 匹配的是文件名
%.o:%.c
	gcc $< -c
```

### 函数

$(函数名 参数1, 参数2, 参数3, ...)，主要目的是让我们能够快速方便的得到函数的返回值。

#### wildcard

这个函数的主要作用是获取指定目录下指定类型的文件名，其返回值是以空格分割的、指定目录下的所有符合条件的文件名列表。函数原型如下：

```makefile
# 该函数的参数只有一个, 但是这个参数可以分成若干个部分, 通过空格间隔
$(wildcard PATTERN...)
	参数:	指定某个目录, 搜索这个路径下指定类型的文件，比如： *.c
```

- 参数功能:

  1. PATTERN 指的是某个或多个目录下的对应的某种类型的文件, 比如当前目录下的.c文件可以写成 *.c
  2. 可以指定多个目录，每个路径之间使用空格间隔

- 返回值：

  1. 得到的若干个文件的文件列表， 文件名之间使用空格间隔

- 示例

  ```makefile
  $(wildcard *.c ./sub/*.c)
  返回值格式: a.c b.c c.c d.c e.c f.c ./sub/aa.c ./sub/bb.c
  ```

#### patsubst

这个函数的功能是按照指定的模式替换指定的文件名的后缀，函数原型如下:

```makefile
# 有三个参数, 参数之间使用 逗号间隔
$(patsubst <pattern>,<replacement>,<text>)
```

- 参数功能:

  1. pattern: 这是一个模式字符串, 需要指定出要被替换的文件名中的后缀是什么
     - 文件名和路径不需要关心, 因此使用 % 表示即可 [通配符是 %]
     - 在通配符后边指定出要被替换的后缀, 比如: %.c, 意味着 .c的后缀要被替换掉
  2. replacement: 这是一个模式字符串, 指定参数pattern中的后缀最终要被替换为什么
     - 还是使用 % 来表示参数pattern 中文件的路径和名字
     - 在通配符 % 后边指定出新的后缀名, 比如: %.o 这表示原来的后缀被替换为 .o
  3. text: 该参数中存储这要被替换的原始数据
- 返回值：函数返回被替换过后的字符串。

```makefile
src = a.cpp b.cpp c.cpp e.cpp
# 把变量 src 中的所有文件名的后缀从 .cpp 替换为 .o
obj = $(patsubst %.cpp, %.o, $(src)) 
# obj 的值为: a.o b.o c.o e.o
```

### 最终版makefile

```makefile
# 添加自定义变量 -> makefile中注释前 使用 # 
# 使用函数搜索当前目录下的源文件 .c
src=$(wildcard *.c)
# 将源文件的后缀替换为 .o
obj=$(patsubst %.c, %.o, $(src))
target=calc

$(target):$(obj)
        gcc $(obj)  -o $(target)

%.o:%.c
        gcc $< -c

# 添加规则, 删除生成文件 *.o 可执行程序
# 声明clean为伪文件，否则如果有clean文件存在，make clean会失败
.PHONY:clean
clean:
        # shell命令前的 - 表示强制这个指令执行, 如果执行失败也不会终止
        -rm $(obj) $(target) 
        echo "hello, 我是测试字符串"
```

