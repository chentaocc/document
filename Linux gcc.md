## Linux gcc

### 安装gcc

```shell
sudo apt update
sudo apt install gcc g++
gcc --version
```

### gcc工作流程

1. 预处理：预处理器操作，展开头文件、宏替换、去掉注释行，得到源文件.i
2. 编译：编译器编译，得到汇编文件.s
3. 汇编：汇编器汇编，得到二进制文件.o
4. 链接：链接器对程序需要用到的库进行链接，得到一个可执行的二进制文件

| 文件名后缀 |  gcc参数   |           参数意义            |
| :--------: | :--------: | :---------------------------: |
|     .c     |     无     |              无               |
|     .i     |     -E     |            预处理             |
|     .s     |     -S     |             编译              |
|     .o     |  -c(小写)  |             汇编              |
|     无     |     -I     | 指定include包含文件的搜索目录 |
|     无     |     -g     |   编译的时候，生成调试信息    |
|     无     | -l（小写） |   编译的时候，指定使用的库    |
|     无     |     -L     | 编译的时候，指定搜索库的路径  |
|     无     |  -shared   |       生成共享目标文件        |
|     无     |     -D     |            定义宏             |

例：编译test.c

```shell
gcc -E test.c -o test.i
gcc -S test.i -o test.s
gcc -c test.s -o test.o
gcc test.o -o test
./test

//实际操作
gcc test.c -o test 或者 gcc -o test test.c
./test

//不加-o test
gcc test.c //直接生成可执行程序a.out

```

### 搜索头文件（-I）

```shell
//多文件编译
gcc add.c sub.c...
//或者
gcc *.c

//包含头文件
gcc *.c -Iinclude
//或者
gcc *.c -I include

//定义宏
gcc *.c -D DEBUG
```

### gcc和g++的区别

```makefile
g++ == gcc -xc++ -lstdc++ -shared-libgcc
```

|          gcc          |  g++(是对gcc的封装)   |
| :-------------------: | :-------------------: |
|      编译.c文件       |     编译.cpp文件      |
|   把.c文件当成c程序   |  把.c文件当成c++程序  |
| 把.cpp文件当成c++程序 | 把.cpp文件当成c++程序 |
|  手动加参数-lstdc++   |  自动链接到标准C++库  |

### 静态库

- Linux静态库以lib为前缀，以.a为后缀，即：libxxx.a
- Window静态库以lib为前缀，以.lib为后缀，即：libxxx.lib

#### 生成静态链接库

1. 将源文件使用参数-c进行汇编，得到.o文件

   ```shell
   //只添加接口实现相关的.c
   //-c参数前后位置都可以
   gcc -c *.c -I ./include/
   ```

2. 打包.o文件，得到静态库

   ```shell
   ar rcs libxxx.a *.o
   ```

3. 发布静态库

   - 提供头文件.h
   - 提供静态库libxxx.a文件

4. 静态库的使用

   ```shell
   //查看源文件目录结构
   tree
   //文件结构
   head.h libxxx.a main.c
   //生成可执行程序calc
   gcc main.c -o calc -L ./ -lxxx
   ```

### 动态库

- Linux静态库以lib为前缀，以.so为后缀，即：libxxx.so
- Window静态库以lib为前缀，以.dll为后缀，即：libxxx.dll

#### 生成动态链接库

使用gcc命令添加参数-fPIC(-fpic)和-shared生成

- -fPIC或-fpic：使gcc生成的代码与位置无关，也就是使用相对位置
- -shared：告诉编译器生成一个动态链接库

1. 将源文件使用gcc加参数-c和-fPIC进行汇编操作，得到.o文件

   ```shell
   gcc *.c -c -fpic -I ./include/
   ```

2. 使用gcc加参数-shared，将.o文件打包成指定的动态链接库

   ```shell
   gcc *.o -o libxxx.so -shared
   ```

3. 发布动态链接库

   - 提供头文件.h
   - 提供动态库libxxx.so文件

4. 动态库的使用

   ```shell
   //文件结构
   head.h libxxx.a main.c
   //生成可执行程序calc
   gcc main.c -o calc -L ./ -lxxx
   ```

### 库的工作原理

- 静态库如何被加载
  - 链接阶段，静态库整个都会被打包到可执行程序中，执行程序时，静态库整个代码都会被加载到内存中，因此不会出现静态库无法找到情况
- 动态库如何被加载
  - 链接阶段，可执行程序只是记录了动态链接库的路径和名字，并没有将整个动态链接库添加到可执行程序中
  - 执行程序时，会先检测动态链接库是否可以被加载，加载不到就会提示错误
  - 当动态链接库的函数被程序调用时，整个动态链接库才会被加载到内存
- 动态链接器（独立于应用程序的进程，属于操作系统）
  - 可执行程序内部的DT_RPATH段
  - 系统的环境变量LD_LIBRARY_PATH
  - 系统动态库的缓存文件/etc/ld.so.cache（这是一个二进制文件）---->修改/etc/ld.so.conf文件
  - 存储动态库和静态库的系统目录/lib/，/usr/lib/，或者创建库的软链接

### 环境变量添加

- 将库路径添加到环境变量LD_LIBRARY_PATH中去

  ```shell
  //查看应用程序执行时需要加载的动态库
  ldd app
  //查看系统动态库的环境变量
  echo $LD_LIBRARY_PATH
  //添加新的库路径到环境变量(只对当前终端有效)
  LD_LIBRARY_PATH=/home/share:$LD_LIBRARY_PATH
  ```

  1. 找到相关配置文件

     - 用户级别：~/.bashrc ----->当前用户生效
     - 系统级别：/etc/profile ----->所有用户生效

  2. vim打开配置文件，末尾添加一句话

     ```shell
     export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/share
     ```

  3. 让修改的配置文件生效

     ```shell
     . ~/.bashrc
     或者
     source ~/.bashrc
     
     . /etc/profile
     或者
     source /etc/profile
     ```

### 静态库和动态库的优缺点

|  类别  |                             优点                             |                             缺点                             |
| :----: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| 静态库 | 1、被打包到应用程序中，加载速度快；2、发布程序无需提供静态库，移植方便； | 1、相同的库文件数据可能在内存中被加载多份，浪费系统资源和内存；2、可执行程序包比较大；3、库文件更新需要重新编译项目文件，浪费时间； |
| 动态库 | 1、实现不同进程间的资源共享；2、库文件更新只需要替换库文件，无需重新编译项目文件；3、可以动态加载，调用库函数的时候才去加载库； | 1、加载速度比静态库慢，不过以现在计算机的性能可以忽略；2、发布程序需要提供依赖的动态库文件； |

