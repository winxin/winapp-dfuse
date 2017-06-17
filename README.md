﻿
# DfuSe-v3.0.5说明文档

### 为什么做这个？

上段时间做了个[STM32串口IAP的开源项目](http://www.jianshu.com/p/1d6eda886b65),在实际项目中如果采用uart或者485等进行数据通信那么采用串口IAP进行固件升级将会是个很好的选择，但是有的时候我们的产品或者项目不是用的串口而是用的USB和上位机进行通信，那么这时候就没必要用USB转串口工具进行协议转换了，这样不仅存在风险而且增加成本。所以很多时候会采用USB的HID、CDC等子类进行数据通信，在这种情况下进行固件升级就会优选DFU方式了。什么是dfu? 准确的说它也是IAP的子集，只不过我们常认为的IAP是通过串口，而dfu是通过USB而已。

通过dfu进行升级需要使用.dfu文件，ST官方提供了将.hex文件或者.bin文件转成.dfu文件的工具，最新的版本是DfuSe—v3.0.4（可以从官方下载）,我下载安装之后发现并不能将.hex转.dfu 总是提示我文件错误。还有一个问题就是.bin生成.dfu之后保存的位置总是不对。这真的不能忍呀，于是就直接看官方MFC源码开始跳入了解BUG的坑。。。


### 我做了些什么？

> 基于ST官方所提供的DfuSe_v3.0.4版本。

> 特色：

- 1.解决ST官方所提供的DfuSe_v3.0.4版本无法将.hex格式文件直接转换成.dfu格式文件的痛点。

- 2.解决ST官方所提供的DfuSe_v3.0.4版本保存生成的目标文件之后文件找不到的BUG。

> 官方v3.0.4使用的是低版本vs，我这里使用的vs2017最新版，你需要安装sdk8.1和基于C++的MFC环境。

### 目录结构

- "Bin": 这里是生成好的最终可以直接使用的目标文件，如果你对源码不感兴趣可以丢弃源码只使用这里的可执行文件就够了。包括：
	+ "DfuFileMgr.exe": 对，这个就是能把hex文件直接转换成dfu的神器了。

	+ "DfuSeDemo.exe": 这个可执行文件就是对下位机进行固件升级的上位机工具了。

	+ "DfuSeCommand.exe": 这个是脚本啦，就是给你提供命令行的方式去dfu升级固件，其实在真正的项目中不会使用DfuSeDemo.exe工具进行固件升级的，你想呀，你要是把DfuSeDemo.exe丢给客户，哪个客户能忍受得了？都是直接把这个脚本嵌入到你的客户端应用中去神不知鬼不觉的升级。

	+ "STDFUTester.exe": 这个跟你没有半毛钱的关系，不要去操心。

	+ ".dll": 这个就是上面4个exe文件运行所需要的动态链接库，不能少了。

	+ "Doc": 这是ST官方提供的说明文档，反正我是懒得看，也看不懂。

	+ "Driver": 这个东西很重要，有的电脑自带DFU驱动，但有很多就不行，所有这里提供了DFU的驱动。根据的你电脑选择不同版本不同处理器架构的驱动安装就好了。如果你是win10,并且没有自带驱动，那么你就试试win8的驱动吧。

	+ "version.txt": 你懂得

- "Sources": 看名字就知道，这是源码了。可以提前告诉你这是用C++的MFC程序，如果你没玩过MFC那就尽量远离吧，避免溅你一身血。。。

- "keilkill.bat": 这是dos的脚本程序，因为编译代码后会生成很多链接库或者中间文件之类的东西，很占用存储空间。所以就写了个脚本，当你编译完成之后推出vs然后双击执行这个脚本就会把那些垃圾清除了。

- "test.bin和test.hex": 没啥用，就是用来给你做测试的。


### 代码工程打开的正确方式

> 用VS打开Sources文件夹下的DfuSe.sln，这个就不用我教你了吧。
（前4个要按照顺序）

1. 重新生成STTubeDevice30
2. 重新生成STDFU
3. 重新生成STDFUFiles
4. 重新生成STFUPRT
5. 之后就可以将DfuFileMgr、DfuSeDemo、DfuSeCommand中的任意一个设置为启动项进行启动了。
6. 根据你的期望去实现你的小目标吧。

### 接下来有什么打算

1. 将DfuFileMgr.exe做成脚本的形式，这样当Keil编译完成生成hex文件之后自动的去调用脚本生成dfu文件。就不用再用鼠标在那点呀点呀的了。

2. 这一项还没想到。。。。。。。

### 更新日期

2017/06/17