# hello-git

how to install valgrind in linux system

download valgrind，and Decompression source code

1.解压源代码到/usr/local/src下；
2.configure --prefix=/usr/local/valgrind；
3.make && make install；

-*-------*---------------*-----------------------*------------------*------------------------*--------------------------
2.下载boost安装包并解压缩
到http://www.boost.org/下载boost的安装包，以boost_1_58_0.tar.gz为例 
下载完成后进行解压缩：

tar zxvf boost_1_58_0.tar.gz
2.设置编译器和所选库
先进入解压缩后的目录：
cd boost_1_58_0
然后运行bootstrap.sh脚本并设置相关参数：
./bootstrap.sh --with-libraries=all --with-toolset=gcc
--with-libraries指定编译哪些boost库，all的话就是全部编译，只想编译部分库的话就把库的名称写上，之间用 , 号分隔即可，可指定的库有以下几种：
库名	说明
atomic	
chrono	
context	
coroutine	
date_time	
exception	
filesystem	
graph	图组件和算法
graph_parallel	
iostreams	
locale	
log	
math	
mpi	用模板实现的元编程框架
program_options	
python	把C++类和函数映射到Python之中
random	
regex	正则表达式库
serialization	
signals	
system	
test	
thread	可移植的C++多线程库
timer	
wave	
--with-toolset指定编译时使用哪种编译器，Linux下使用gcc即可，如果系统中安装了多个版本的gcc，在这里可以指定gcc的版本，比如--with-toolset=gcc-4.4

命令执行完成后看到显示如下即为成功：

Building Boost.Build engine with toolset gcc... tools/build/src/engine/bin.linuxx86_64/b2
Detecting Python version... 2.6
Detecting Python root... /usr
Unicode/ICU support for Boost.Regex?... not found.
Generating Boost.Build configuration in project-config.jam...

Bootstrapping is done. To build, run:

    ./b2

To adjust configuration, edit 'project-config.jam'.
Further information:

   - Command line help:
     ./b2 --help

   - Getting started guide: 
     http://www.boost.org/more/getting_started/unix-variants.html

   - Boost.Build documentation:
     http://www.boost.org/build/doc/html/index.html

3.编译boost
执行以下命令开始进行boost的编译：

./b2 toolset=gcc
1
编译的时间大概要10多分钟，耐心等待，结束后会有以下提示：

...failed updating 60 targets...
...skipped 21 targets...
...updated 663 targets...
1
2
3
4.安装boost
最后执行以下命令开始安装boost：

./b2 install --prefix=/usr
1
--prefix=/usr用来指定boost的安装目录，不加此参数的话默认的头文件在/usr/local/include/boost目录下，库文件在/usr/local/lib/目录下。这里把安装目录指定为--prefix=/usr则boost会直接安装到系统头文件目录和库文件目录下，可以省略配置环境变量。

安装完毕后会有以下提示：

...failed updating 60 targets...
...skipped 21 targets...
...updated 11593 targets...
1
2
3
最后需要注意，如果安装后想马上使用boost库进行编译，还需要执行一下这个命令：

ldconfig
1
更新一下系统的动态链接库

5.boost使用测试
以boost_thread为例，测试刚安装完的boost库是否能正确使用，测试代码如下：

#include <boost/thread/thread.hpp> //包含boost头文件
#include <iostream>
#include <cstdlib>
using namespace std;

volatile bool isRuning = true;

void func1()
{
    static int cnt1 = 0;
    while(isRuning)
    {
        cout << "func1:" << cnt1++ << endl;
        sleep(1);
    }
}

void func2()
{
    static int cnt2 = 0;
    while(isRuning)
    {
        cout << "\tfunc2:" << cnt2++ << endl;
        sleep(2);
    }
}

int main()
{
    boost::thread thread1(&func1);
    boost::thread thread2(&func2);

    system("read");
    isRuning = false;

    thread2.join();
    thread1.join();
    cout << "exit" << endl;
    return 0;
}

在编译程序时，需要加入对boost_thread库的引用：

g++ main.cpp -g -o main -lboost_thread
1
如果boost库的安装位置不是在系统目录下，则还需要在编译时加上-I和-L指定boost头文件和库文件的位置

编译成功后运行程序，利用boost实现的多线程任务正确运行：

func1:  func2:00

func1:1
        func2:1
func1:2
func1:3
        func2:2
func1:4
func1:5
        func2:3
func1:6
func1:7
        func2:4
func1:8
func1:9
        func2:5
func1:10
func1:11
        func2:6
func1:12
func1:13
        func2:7
func1:14
func1:15
        func2:8
func1:16

exit

-*-------*---------------*-----------------------*------------------*------------------------*--------------------------
安装及简单使用libevent
接下来我们直接进入正题,安装libevent-1.4。 
1. 在官网上下载对应版本的包 
2. tar -zxvf /your path/libevent-1.4.14b-stable.tar.gz解压到当前目录 
3. cd libevent-1.4.14b-stable 
4. ./configure 
5. make && make install 
6. 在/usr/local/lib目录下应该可以看见大量的动态链接库了,这时运行ln -s /usr/local/lib/libevent-1.4.so.2 
#include <stdio.h>
#include <stdio.h>
//使用libevent库所需头文件  
#include <event.h>  

void on_time(int sock,short event,void *arg)  
{  
    printf("hello world\n");  

    struct timeval tv;  
    tv.tv_sec = 1;  
    tv.tv_usec = 0;  

    // 事件执行后,默认就被删除,所以需要重新添加  
    event_add((struct event*)arg, &tv);  
}  

int main()  
{  
    //  初始化事件  
    event_init();  

    //  设置定时器回调函数  
    struct event ev_time;  
    evtimer_set(&ev_time, on_time, &ev_time);  

    //1s运行一次func函数
    struct timeval tv;  
    tv.tv_sec = 1;  
    tv.tv_usec = 0;  

    //添加到事件循环中
    event_add(&ev_time, &tv);  

    //程序等待就绪事件并执行事件处理
    event_dispatch();  

    return 0;  
}  
gcc demo.c -o demo -levent编译 

-*-------*---------------*-----------------------*------------------*------------------------*--------------------------
这份文档描述了如何在Ubuntu Linux机器上安装Python 3.6。

想要获取已安装的Python 3版本号，可以通过终端运行命令：

$ python3 --version
如果您使用的是Ubuntu 16.10或更新，可以通过以下命令简单地安装Python 3.6:

$ sudo apt-get update
$ sudo apt-get install python3.6
如果您使用的是其他版本的Ubuntu（比如LTS发行版），我们推荐使用 deadsnakes PPA 来安装 Python 3.6:

$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:deadsnakes/ppa
$ sudo apt-get update
$ sudo apt-get install python3.6
如果您使用的是其他Linux发行版，有可能已经预装了Python 3。如果没有，使用发行版的包管理器。 比如，在Fedora上您可以使用 dnf：

$ sudo dnf install python3
注意，如果 python3 包的版本不够新，还有其他方式安装更新的版本，这取决于所在的发行版。 比如在Fedora 25上安装 python36 包来获取Python 3.6。如果您是Fedora用户，您可能想 阅读 Fedora中可用的多Python版本 。

使用Python 3
这个时候，在您系统上可能Python 2.7也是可用的。

$ python
将打开Python 2解释器。

$ python3
将打开Python 3解释器。

Setuptools & Pip
setuptools 和 pip 是最重要的两个Python第三方软件包。一旦安装了它们，就可以通过一条指令下载、安装和卸载可获取到的 Python应用包，还可以轻松地将这种网络安装的方式加入到自己开发的Python应用中。

Python 2.7.9 以及之后版本(Python2 系列)，和Python 3.4以及之后版本均默认包含pip。

运行以下命令行代码检查pip是否已经安装：

$ command -v pip
参考官方pip安装指南 获取pip工具，并自动安装最新版本的setuptools。

注意，在某些Linux发行版（包括Ubuntu和Fedora）上， pip 用于Python 2的，而 pip3 用于Python 3。

$ command -v pip3
不过，使用虚拟环境（下面描述）就无需担心这个问题。

Pipenv & 虚拟环境
下一步安装 Pipenv，然后就可以安装依赖关系并管理虚拟环境。

虚拟环境工具通过为不同项目创建专属的 Python 虚拟环境，以实现其依赖的库独立保存在不同的路径。 这解决了“项目X依赖于 1.x 版本，但项目 Y 需要 4.x”的难题，并且维持全局的 site-packages 目录干净、易管理。

举个例子，通过这个工具可以实现依赖 Django 1.10 的项目与依赖 Django 1.8 的项目共存。

所以，向前！进入到 Pipenv & 虚拟环境 文档中！

-*-------*---------------*-----------------------*------------------*------------------------*--------------------------
如何在Linux中安装Lua
Lua软件包可以在主要Linux发行版的官方存储库中找到，您可以使用系统上相应的软件包管理器安装最新版本。

$ sudo apt install lua5.3	                #Debian/Ubuntu systems 
# yum install epel-release && yum install lua	#RHEL/CentOS systems 
# dnf install lua		                #Fedora 22+
注意： EPEL存储库中当前版本的Lua包是5.1.4; 因此，要安装当前版本，您需要从源代码构建和安装它，如下所述。

从Sources安装Lua
首先，确保在系统上安装了开发工具，否则请运行以下命令进行安装。

$ sudo apt install build-essential libreadline-dev      #Debian/Ubuntu systems 
# yum groupinstall "Development Tools" readline		#RHEL/CentOS systems 
# dnf groupinstall "Development Tools" readline		#Fedora 22+
然后，要构建并安装Lua的最新版本（撰写本文时为5.3.4版），请运行以下命令以下载包tar球，提取，构建和安装它。

$ mkdir lua_build
$ cd lua_build
$ curl -R -O http://www.lua.org/ftp/lua-5.3.4.tar.gz
$ tar -zxf lua-5.3.4.tar.gz
$ cd lua-5.3.4
$ make linux test
$ sudo make install
-*-------*---------------*-----------------------*------------------*------------------------*--------------------------
安装valgrand

1.下载安装valgrand

2../configure --prefix=/usr/local/valgrind

3.make
4.sudo make install
-****************----------------------------------
https://github.com/yogykwan/design-patterns-cpp  大话设计模式C++实现
https://github.com/threerocks/studyFiles/blob/master/%E7%AE%97%E6%B3%95/%E5%A4%A7%E8%AF%9D%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84.pdf

https://github.com/W2B1/book 电子书籍库
