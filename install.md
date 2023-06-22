# Read YCM reference

[ycm](https://www.url.com)

# Windows 

## YCM Plugin install 

使用官网推荐的quick-start

* Install YCM plugin via Vundle
* Install Visual Studio Build Tools 2019
* Install CMake, Vim and Python
* Install go, node and npm
* Compile YCM

ok, 根据上面 的步骤一步一步构建
1. Install YCM plugin via Vundle 

这里用的Vundle,不过我一直用的vim-plug,所以没用vundle,我认为插件只是下载 而已。 不过ycm 工程较大， 没法PlugInstall, 只好手动git clone 放在Plugged下面

```
##git clone --recurse-submodules --
# 国内这网还是分开clone
git clone https://github.com/ycm-core/YouCompleteMe.git
cd YouCompleteMe
git submodule update --init --recursive

```

2. 安装build的工具

这里要安装VS2019 build tools,不过已经安装 了vs2022，这步就没必要 了 

3. Install CMake , Vim , and Python

[CMake](https://www.url.com)
[Vim](https://www.url.com)
[Python](https://www.url.com)

直接找下最新版,我安装的cmake3.26,vim9,python311-64

4. Install go , node and npm

win10 自带node,npm
go安装的1.18

[go](https://www.url.com)

5. Compiling YCM

***主要的坑就在这。。。。***

+ 启动DevelperPowershell For VS2022 执行下面的命令
```
cd YouCompleteMe
python3 install.py --all

```
ok 报错了,国内这网就不说了
![clanglib]( "opt title")
 ok手动下载 libclang-15.0.1-win64.tar.bz2，放在环境变量path,使用python install.py -h 发现有--system-libclang 这个选项， 这个应该就是控制lbiclang的，重新执行还是不好使。没办法 只好查看 CMakeLists.txt, 发现，这个选项的LLVM_EXECUTABLE,也就是要下载llvm整套东西，感觉挺麻烦，继续看有无其他，发布EXTERNAL_LIBCLANG_PATH 这个选项可以制定库路径，不过暂时没发现可以指定路径传给install.py的参数。
我敢肯定只有库是可以用的，沿着这个思路看看自动下载之后是cmake是怎么处理的，ok，首先是开启LIBCLANG_DOWNLOAD选项， 然后把下载的文件路径直接赋值给LIBCLANG_LOCAL_FILE，索性我这直接关闭LIBCLANG_DOWNLOAD，再把本地下载好的文件路径赋值给LIBCLANG_LOCAL_FILE
ok，重新编译，已经开始编译ycm_core, 这一步成了。
紧接着又报如下错误：
Building regex module...FAILED with exception UnicodeDecodeError: 'utf-8' codec can't decode byte 0xd5 in position 6258: invalid continuation byte
感觉错误信息太短， 加上-verbose选项
然而发现错误并非这个unicodeu问题，而是python_c函数undefine的链接问题
```
_regex.obj : error LNK2001: 无法解析的外部符号 __imp__Py_GenericAlias
_regex.obj : error LNK2001: 无法解析的外部符号 __imp__PySlice_AdjustIndices
_regex.obj : error LNK2001: 无法解析的外部符号 __imp___Py_BuildValue_SizeT
_regex.obj : error LNK2001: 无法解析的外部符号 __imp__PyLong_AsUnsignedLong
....
...
```
发现原来是此时构建的是x86的版本而我用的python是x64 ok换成 x86python 问题解决。对应的，此时libclang也要用32位。修改cmake后终于构建完成 。

下载rust有时会超时多试几次即可。

启动gvim a.cpp
又报
YouCompleteMe unavailable : No module named ' _socket'

查了一圈发现,设置pythonthreehome和pythonthreedll可以解决这个问题

终于，cpp/c/go/rust/java/可以补全了！！！

# Linux














