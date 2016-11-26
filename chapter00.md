# 0. 开发环境搭建

参考资料：官方的文档，主要是

 1. [Setup Toolchain](http://esp-idf.readthedocs.io/en/latest/)

## 0.1 介绍：为什么需要这些工具

同学们最大的疑问可能就是：不就是开发一个esp32么，怎么需要劳师动众安装这么多东西呀，不能跟keil一样双击就可以么？以后呢，肯定会有大牛或者热心群众制作“一键式的开发环境”。但现在，只能手动搭建开发环境，步骤很繁琐但并不困难。

**注意**：esp32开发环境中用到的很多工具，除了`esp-idf`和`xtensa-`交叉编译工具之外，诸如bash或者make都是任何一个linux发行版系统自带的。所以说，linux可能更适合于进行esp32的开发（或者，linux更适合做嵌入式开发）。

为了照顾到国内大多数的教学环境，下面以windows为例说明。

已经熟悉的读者可以安全的跳过本节。

### 0.1.1 交叉编译工具

首先，esp32集成了xtensa双核处理器。因此，在esp32上运行的程序，需要是该处理器支持的格式。“交叉编译”这一名称的来源就是指：在主机上（例如x86或x64），生成其他CPU的可执行文件。

为了生成特定CPU的可执行文件，在编译c程序时需要特定CPU的gcc。例如，编译xtensa上的elf（可执行程序），就需要`xtensa-*-gcc`。幸运的是，espressif公司（乐鑫）针对windows及linux，已经做好了现成可用的`xtensa-gcc`交叉编译工具。我们所需要做的就是下载、解压，然后告诉操作系统，交叉编译的可执行文件在哪里？而操作系统寻找可执行文件，都可以通过修改`PATH`变量进行配置。

### 0.1.2 make

esp32的程序包含一个`esp-idf`（系统）以及用户程序，为了编译这么多文件，需要使用`make`。这里，`esp-idf`是espressif的集成开发环境，包括：esp32相关API，FreeRTOS操作系统，以及其它功能组件。至今为止（2016-11-26），`esp-idf`仍旧在开发中，很多新的功能还正在添加，已有功能还没稳定下来。

对用任何一个用户程序，对于esp32系统组件的需求都不一样。比如，有的人想用无线，有的人想用蓝牙，有的或许想降低处理器时钟频率。浙西都需要对`esp-idf`进行“定制”。而定制，则需要通过修改`makefile`来实现（通过执行`make menuconfig`）。因此，我们需要建立跟Linux下类似的make编译环境。而在windows下，就需要安装`msys32`这个工具。

这样，在make安装完毕之后，如果想定制组件或者参数，只需在终端下输入`make menuconfig`就行（没错，跟配置linux内核一个步骤）。针对一个项目，只需要定制并且编译一次内核（编译文件较多，速度较慢）。以后，我们可以专注于修改、完善用户自己的应用程序。而每次修改应用程序后，进行增量编译就可以，速度很快。

### 0.1.3 git

乐鑫的集成开发环境`esp-idf`存放在github上。对于github不了解或者不经常使用的同学，首先需要了解一下基于git的协作开发流程。简而言之，我们需要将`esp-idf`从服务器“拖拽”到本地路径上（这里，强烈建议放置的目录**不要包含空格以及中文字符**）。

在拖拽之前，你需要在[github.com](github.com)上注册一个账号。随后，下载安装git。

### 0.1.4 esp-idf

利用git执行拖拽的命令就是`git clone`，具体命令是
```bash
git clone git@github.com:espressif/esp-idf.git
```

另外，在`esp-idf`中，还有好多必备的组件是以“子模块”（链接）的形式存在的。在执行完clone之后，我们还需要进入`esp-idf`目录内，将其余子模块一起拖拽下来。

```bash
cd esp-idf/
git submodule init
git submodule update --recursive
```

在执行完这一步之后，我们就需要告诉操作系统，`esp-idf`放在了哪里？方法就是设置`IDF_PATH`变量。

### 0.1.5 esp-idf-template

`esp-idf-template`提供了一个最基本的样例（工程）。在`esp-idf/examples`路径下，也有好多（激动人心的不断添加中）样例文件。

跟`esp-idf`类似，我们需要将`esp-idf-template`拖拽到本地目录

```bash
git clone git@github.com:espressif/esp-idf-template.git
```

### 0.1.6 python

espressif官方提供了烧录esp程序的脚本。只不过，脚本文件是用python语言写的（当然，你也可以从官方网站下载GUI烧写工具）。因此，我们需要安装python。我们推荐安装anaconda这个python开发环境。需要注意，当前（2016-11-27）esptool只支持python2。因此，如果你只想用python辅助esp开发，只需下载python2版本的anaconda就行。

安装完毕之后，`python.exe`可执行文件的路径会自动添加到系统的`PATH`变量中。

### 0.1.7 汇总

可以看出，linux开发环境省事省心。我们将后面内容需要的东西列在下面：

 1. **xtensa-gcc交叉编译环境**。需设置`PATH`变量指向可执行文件目录。
 2. **msys32开发工具**，make环境需要安装并配置msys32这个工具。在安装之后，需要设置`PATH`变量。`msys32`包含了一个可执行文件，叫做`msys2_shell.cmd`，双击打开即可。
 3. **git**。从网站上下载git即可。git会自动提供一个叫git bash的工具，打开就可以进入到设置好git路径的终端。
 4. **esp-idf文件**。假设esp-idf放在了`c:/esp32/esp-idf`下。**特别注意**，路径的分割必须是（slash）`/`而不要用（backslash）`\`。随后，我们要设置好`IDF_PATH`路径。
 5. **esp-idf-template**。假设放于`c:/esp32/esp-idf-template`下。无需其他设置。
 6. **python**。安装python2版本的anaconda即可。

### 0.1.8 开发板

当然，在学习esp32的时候，你手边最好能有一个`esp32`的开发板。现在国外的爱好者们都很羡慕国内的（开源硬件）开发环境了（除了github访问有时候比较诡异之外）。因为espressif（乐鑫）是上海的公司，而且很多第一手的开发板，比如我要推荐的`ebox`的`esp32`开发套件，以及`noduino`开发套件，`widora air`等，包括官方的`esp32s`开发板，都可方便的在淘宝上买到。

这个教程中，我们主要使用的是`ebox`的开发板，有时候也用乐鑫自己的，或者`widora air`。

下面，我们一步一步来设置。默认操作系统是windows。linux下类似并且更加简单。

## 0.2 交叉编译工具（windows）

### 0.2.1 安装交叉编译工具和make环境

windows下最简单的办法，是按照官方说明的，下载

[https://dl.espressif.com/dl/esp32_win32_msys2_environment_and_toolchain-20160816.zip](https://dl.espressif.com/dl/esp32_win32_msys2_environment_and_toolchain-20160816.zip)

将这个文件解压到`C:/msys32`目录下。

在该目录下，有一个`C:/msys32/msys32_shell.cmd`可执行文件。运行它就会打开一个bash窗口，里面所有的编译工具、make环境都具备了。这个目录中进入C盘是通过

```bash
cd /c/path/to/your/folder
cd /etc
```

后面这个命令是进入bash的配置目录，我们需要在里面做一些设置。

### 0.2.2 配置`esp-idf`路径

首先，在`C:/msys32/etc/profile.d/` 目录中，创建一个文件`esp-path.sh`。（这里给大家讲一下，`profile.d`目录中的文件，在每次启动用户终端的时候，都会**自动执行一次**。而终端模仿的是linux开发环境，因此里面的bash脚本使用linux中的配置命令就行）。该文件的内容是：

```bash
export IDF_PATH="C:/esp32/esp-idf"
```

**再次强调**，要用（slash）`/`而不要用（backslash）`\`。

然后我们进入系统设置（win 10 以前的版本可以在我的电脑上按右键 -> 选择属性 -> 高级设置即可），在高级 -> 环境变量中，添加一个`IDF_PATH`变量（变量名），值（变量值）就填写`esp-idf`目录所在的位置即可。

通过这样设置之后，交叉编译器（`xtensa-gcc`之类的）以及make就知道在哪里去寻找`esp-idf`了。

## 0.3 例程

运行`msys32_shell.cmd`，然后进入`esp-idf-template`目录：

```bash
cd /c/esp32/esp-idf-template
make menuconfig
make all
make flash
```

以上几个命令中，`make menuconfig`是配置`esp-idf`；`make all`是分别生成各种可执行文件（印象中是三个文件，具体文件的名称、功能，我们后续慢慢了解）；`make flash`是烧写文件（需要开发板）。

在`make all`之后，如果一切顺利，最后一行会输出一个`python`命令。如果你不想用`make flash`，直接复制、粘贴这个命令就可以。但是，在windows中，串口（esp32烧写通过串口，并且你需要先安装一个cp201x驱动）设备一般都叫做`/COM3`或者`/COM4`之类的；在linux下（以及在windows中的`msys32`环境里），串口设备位于`/dev`目录中，一般叫做`/dev/ttyUSB0`或者`/dev/ttyS2`，后面的数字根据系统串口的多少而不同。

windows中的烧写必须使用windows格式的串口设备。因此，我们需要将`make all`最后一行输出命令中的`/dev/ttyUSB0`改写为`/COM3`。而在linux下，直接运行最后一行就可以。

一直到`make flash`为止，如果一步一步按上面的命令做下来，一般不会出错。这个template的程序如果不加修改，直接烧写进去后，板子不会有什么反应。我们可以进入`main`目录，查看代码，看看它主要干了些什么。（提示：可以试着修改下AP和Station设置，这样你就能看到一些有意思的打印信息了）

## 0.4 交叉编译工具（linux）

linux下主要的工作是安装交叉编译工具。我们使用的是archlinux，可以用现成的`x64`版本已经编译好的工具压缩包。假设我们将工具解压到了`/opt`目录下。对于64位系统，可以安装（随着时间推移版本号可能不同）。

[ttps://dl.espressif.com/dl/xtensa-esp32-elf-linux64-1.22.0-59.tar.gz](ttps://dl.espressif.com/dl/xtensa-esp32-elf-linux64-1.22.0-59.tar.gz)

一般来说，我们没有必要自己去编译交叉工具链。在archlinux下，为了能够编译、烧写esp32，我们还需要安装一些必须的软件

```bash
sudo pacman -S --needed gcc git make ncurses flex bison gperf python2-pyserial
```

最后，修改`~/.profile.sh`，里面需要分别指定交叉编译工具可执行文件的路径，以及`esp-idf`的路径。

```bash
export PATH=$PATH:/opt/xtensa-esp32-elf/bin
export IDF_PATH=~/esp32/esp-idf
```

**注意事项**，我们需要`python2-serial`用来烧写flash程序，由于`esptool.py`当前只支持`python2`，因此对于arch使用者来说，还需要在`make menuconfig`中指定python的版本（版本2）。具体做法是，在`SDK tool configuration`里，将`Python 2 interpreter`从`python`修改为`python2`。

## 0.5 集成开发环境eclipse

这里，我们科使用`eclipse`开发`esp32`程序。eclipse的配置难度（繁琐程度）其实不比直接编译小。

### 0.5.1 安装eclipse CDT

直接下载安装。

### 0.5.2 先利用msys32配置工程

我们需要首先使用`make menuconfig`配置工程，再用eclipse开发。

先将例程`esp-idf-template`复制到`c:/esp32/workspace`下，重命名为`myapp`。进入`myapp`，运行

```bash
make menuconfig
```

### 0.5.3 配置eclipse

打开eclipse，设置工作目录为`C:/esp32/workspace`，选择`Use this as the default and do not ask again`。

然后，我们新建一个c项目，在`project explorer`中选择`new` -> `c project`。在项目类别中，选择`Makefile project`中的空项目（empty project）。**重要**，项目名称`Project name`必须跟例程目录名称一样。这里，我们选择`myapp`。

 1. **修改make脚本**。在项目选项中（properties, C/C++ build），在Build Settings，取消选中`Use default build command`，使用`bash ${IDF_PATH}/tools/windows/eclipse_make.sh`。随后，我们需要告诉eclipse，`bash`、`make`和`xtensa-esp32-elf-gcc`都在哪里。
 2. **指定IDF路径**。在(properties, C/C++ build, Environment)中，新增一个变量，设置`IDF_PATH`指向`c:/esp32/esp-idf`。
 3. **指定bash, make及交叉编译工具路径**。在(properties, C/C++ build, Environment)中，新增一个变量`PATH`，指向`C:/msys32/usr/bin;C:/msys32/mingw32/bin;C:/msys32/opt/xtensa-esp32-elf/bin`。
 4. **设定编译方法1**。在设置了编译文件后，我们还要告诉项目用哪个gcc进行编译。在`C/C++ General` -> `Preprocessor Include Paths`中，点击`Providers`，选中`CDT Cross GCC Built-in Compiler Settings`，然后将里面的`${COMMAND}`修改为`xtensa-esp32-elf-gcc`。
 5. **设定编译方法2**。同样在之前的providers中，选中`CDT GCC Build Output Parser`，在已有字符串之前加入`xtensa-esp32-elf-`。最终看上去是这样子的：`xtensa-esp32-elf-(g?cc)|([gc]\+\+)|(clang)`

就这么多。在linux下，只需要设置好2、4、5即可。eclipse会自动读取系统环境变量，找到交叉编译工具。

### 0.5.4 新建build选项。编译、测试

好了，下面我们就可以添加编译项目了，右键点击项目，选择`Build Targets` -> `Create`，创建makefile中已有的即可。主要有`all`、`clean`和`flash`。我们创建一个`all`，然后在`all`上选择编译即可。在all上按右键，选择编译，在console输出中就能看到log输出。

### 0.5.5 关于index

在例程中，我们打开`main.c`会发现很多函数和定义前面有红色的警告标示。原因是有些Symbol我们没有找到。我们参考kolban的方法，视频在这里：

[ESP32 Technical Tutorial: Building with Eclipse](https://www.youtube.com/watch?v=bYh2w0HzS7s&t=2s)。

解决办法如下。右键，项目设置，找到`C/C++ General` -> `Path and Symbols`，在`GNU C`中，分别添加

```bash
${IDF_PATH}/components/esp32/include
${IDF_PATH}/components/newlib/include
${IDF_PATH}/components/freertos/include
${IDF_PATH}/components/nvs_flash/include
${IDF_PATH}/components/driver/include
${IDF_PATH}/components/log/include
/myapp/build/include
```

最后一个需要添加该项目（编译结果`build/`中）的include内容，在点击添加随后弹出的对话框中，要选中`Is a workspace path`。

右键点击项目，选择 `Index` -> `Rebuild` 即可。

### 0.5.6 烧写flash

我们将build all后的最后一行复制出来，将`/dev/ttyUSB0`修改为`/COM3`（选中在你电脑上，插入esp32s开发板后自动枚举出来的设备）。

```bash
python C:/esp32/esp-idf/components/esptool_py/esptool/esptool.py --chip esp32 --port /dev/ttyUSB0 --baud 115200 write_flash --flash_mode dio --flash_freq 40m --flash_size 2MB 0x1000 c:/esp32/workspace/myapp/build/bootloader/bootloader.bin 0x10000 /c/esp32/workspace/myapp/build/app-template.bin 0x8000 /c/esp32/workspace/myapp/build/partitions_singleapp.bin
```

**再次提示**，需要的python版本是`python2`，若你的电脑上没有，espressif提供的官方开发工具里，打开`msys32_shell.cmd`，里面就自带`python2`。

## 0.6 小结

按照官方教程一步一步做下来，一般都不会出错。本文作为笔记，可以快速浏览一遍，参考以备以后使用。

PS：强烈建议大家采用linux开发esp32程序。
