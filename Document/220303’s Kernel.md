# 220303's Kernel Rust

![logo](./Images_Videos/项目封面.jpg)

#### 说明：这篇文档是对我编写 220303's Kernel R 这个项目过程的一个总结，假设我能未卜先知，我会按照这个操作顺序完成整个项目。因此本文档可以用作教程。

#### 目标：以Risc-V为目标平台，基于Rust SBI，用Rust编写一个操作系统，基本对标2025年NSCSCC内核实现赛道，但主要是根据我个人喜好进行编写。



## 历史

在很早之前，我读 \<CSAPP>，\<Operating System: Three Easy Pieces>，< Computer Organization and Design: The Hardware/Software Interface, RISC-V Edition > 等书籍的时候，就想到要实现一个自己的操作系统，并且我比较青睐于Rust 和 Risc-V。

2025年4月3日，我创建了一个名为`220303's Kernel C`的项目 (Github仓库: [220303/220303-Kernel-C: An operating system kernel by C .](https://github.com/220303/220303-Kernel-C))，该项目基于x86架构用C语言实现了一个简单的内核 (与其说是内核不如说是裸机程序，仅支持VGA输出)，该项目仅耗时一天 (4月3日，大约15小时)。

在该项目完成后的几天内，我创建了本项目，此时我正高二，17周岁。



## 硬件环境

### 工作环境

* PC个人电脑

```
设备名称	DESKTOP-UI7A9RJ
处理器	Intel(R) Core(TM) i5-10400F CPU @ 2.90GHz   2.90 GHz
机带 RAM	24.0 GB
设备 ID	BD0764DC-1EB5-4F6F-8592-8E9DB4F6C81B
产品 ID	00391-70000-00000-AA040
系统类型	64 位操作系统, 基于 x64 的处理器
笔和触控	笔支持
```

### StarFive-VisionFive2 [塞昉-星光二代]开发板

[若您不打算将操作系统在实体开发板上运行，那么可以不准备这部分]

我买了带有 WiFi 的版本，在后续的内容中也确实需要联网，如果不买WiFi模块，也可考虑用RJ45网口连接双绞线上网(以太网)

* 开发板型号、参数

  ```
  
  VisionFive2 8GB WiFi 
  SKU:23874
  
  _________________________________________________________________________________________________________
  
  CPU  赛昉科技昉·惊鸿-7110 RISC-V 四核64位RV64GC ISA SoC搭载2MB L2缓存和协处理器，工作频率最高可达1.5 GHz
  GPU  Imagination GPU IMG BXE-4-32 MC1，工作频率最高可达600 MHz
  内存	2 GB/4 GB/8 GB	LPDDR4 SDRAM，传输速度最高可达2,800 Mbps
  存储	板载TF卡插槽	昉·星光 2可从TF卡启动
  闪存	存储U-Boot和Bootloader的固件
  
  _________________________________________________________________________________________________________
  
  视频输出:
  1 × 2-lane MIPI DSI显示接口（最高1080p@30fps）
  1 × 4-lane MIPI DSI显示接口，在单屏显示和双屏显示模式下支持最高2K@30fps
  1 × HDMI 2.0，支持最高4K@30fps或2K@60fps
  
  摄像头	1 × 2-lane MIPI CSI摄像头接口，支持最高1080P@30fps
  
  编解码:
  视频解码（H264/H265）最高达4K@60fps，支持多路解码
  视频编码（H265）最高达1080p@30fps，支持多路编码
  JPEG编解码
  
  音频	4极立体声音频插孔
  
  连接:
  以太网	2 × RJ45千兆以太网接口
  USB Host	4 × USB 3.0接口（通过PCIe 2.0 1 × lanes复用）
  USB Device	1 x USB device接口（和USB-C接口复用）
  M.2连接器	M.2 M-Key
  eMMC插槽	用于eMMC模块，如操作系统和数据存储
  2-Pin风扇接口
  
  电源：
  USB-C接口
  通过USB-C PD快充端口输入9 V~12 V DC，最高30W（最低2 A）
  GPIO header 电源输入5 V DC （最低3A）
  PoE（以太网供电）使用此功能需要另行购买PoE拓展版
  
  40-Pin GPIO Header:
  3.3 V（2 pins）
  5 V（2 pins）
  接地接口（8 pins）
  GPIO
  CAN总线
  DMIC
  I2C
  I2S
  PWM
  SPI
  UART
  
  启动模式：
  1-bit QSPI Nor Flash
  SDIO3.0
  eMMC
  UART
  
  _________________________________________________________________________________________________________
  
  按钮  Reset键
  尺寸	100 × 74 mm
  合规性	RoHS，FCC，CE	
  环境	推荐运行温度为	0-50 ℃
  调试	40-pin GPIO header提供UART TX和UART RX功能
  
  ```

* 重要网页

  [赛昉科技-开发板](https://www.starfivetech.com/site/boards)

  [昉·星光 2](https://doc.rvspace.org/Doc_Center/visionfive_2.html)

  [Pin图](https://doc.rvspace.org/VisionFive2/Quick_Start_Guide/VisionFive2_QSG/pinout_diagram - vf2.html)

  [Windows系统](https://doc.rvspace.org/VisionFive2/Quick_Start_Guide/VisionFive2_QSG/for_windows2 - vf2.html)

### 开发板配套工具

[若您不打算将操作系统在实体开发板上运行，那么可以不准备这部分]

* USB To 串口 转换板

  我买的是 `ALL IN ONE 多功能 USB 转 I2C/SPI/UART 适配器`，型号`CH341A YSUMA01-341A` 。

  驱动：[CH341SER.EXE - 南京沁恒微电子股份有限公司](https://www.wch.cn/downloads/CH341SER_EXE.html)

* 杜邦线

  母对母，长度够用就行，用于连接`USB To 串口 转换板`和`塞昉-星光二代开发板`，至少三根。



## 软件环境

### 工作平台

* Windows 10 专业工作站版

  ```
  版本	Windows 10 专业工作站版
  版本号	22H2
  安装日期	‎2024/‎10/‎26
  操作系统内部版本	19045.5737
  体验	Windows Feature Experience Pack 1000.19061.1000.0
  ```

### 安装MinGW

* 到 MinGW 官网的下载页面下载安装包 ：[Pre-built Toolchains - mingw-w64](https://www.mingw-w64.org/downloads/)

  选择：

  ![MinGW](T:\ELCO\Computer Science\Operating System\220303's Kernel R\Document\MinGW.png)

  不选择MSYS2的原因是它太大了，包含了一些我不需要的内容。

### 安装Rust

* 设置环境变量：

  [用于指定安装位置，我安装到了`T:\Environment\Rust`]

  [添加在用户/系统环境变量中，注意不是Path里面，是和Path并列的]

  ```
  RUSTUP_HOME	T:\Environment\Rust\.rustup
  
  CARGO_HOME	T:\Environment\Rust\.cargo
  ```

* 到Rust官网下载安装包：[Rust 程序设计语言](https://www.rust-lang.org/zh-CN/)

* 在 T:\Environment\Rust\.cargo 下创建名为 config.toml 的文件。

  内容如下：

  ```
  [source.crates-io]
  registry = "https://github.com/rust-lang/crates.io-index"
  
  # 替换成你偏好的镜像源
  replace-with = 'ustc'
  
  # 清华大学
  [source.tuna]
  registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"
  
  # 中国科学技术大学
  [source.ustc]
  registry = "git://mirrors.ustc.edu.cn/crates.io-index"
  
  # 上海交通大学
  [source.sjtu]
  registry = "https://mirrors.sjtug.sjtu.edu.cn/git/crates.io-index"
  
  # rustcc社区
  [source.rustcc]
  registry = "git://crates.rustcc.cn/crates.io-index"
  ```

* 双击`rustup-init.exe`安装程序进行安装，并选择MinGW作为依赖的C[++]编译工具链。

  安装过程如下：[输入内容，非回车的内容输入后需要回车]

  ```
  y
  2
  x86_64-pc-windows-gnu
  回车
  回车
  回车
  回车
  回车(结束安装)
  ```

  

* 检查环境变量的Path是否存在 ` %CARGO_HOME%/bin ` 这一条，如果没有需要手动添加。



* 检验安装：
  输入命令：`rustc --version`
  输出版本号即为安装正常。

### 安装QEMU

* 

### 安装Ubuntu 24.04 LTS：[采用Virtual Box]

* 在Ubuntu官网下载镜像
* 安装到虚拟机
* 改变启动顺序(硬盘最优先)
* [可选]：软件源选择华为(自动测试最快)，可以在”软件与更新“程序中更改(在它检查更新时候暂停 -> 设置 -> Ubuntu软件)
* 将系统及软件更到最新
* 在虚拟机中为Virtual Box 增强扩展 安装前置
* 安装Virtual Box 增强扩展
* 配置共享文件夹 T/ELCO/Computer Science/Operation System
* 挂载点 /home/g220303/kernel
* 添加快照

### 安装 Tera Term

[若您不打算将操作系统在实体开发板上运行，那么可以不准备这部分]

用于与连接开发板进行环境配置和调试操作系统

* Github主页：[TeraTermProject/teraterm](https://github.com/TeraTermProject/teraterm)

  撰写这一行的时候最新版为5.4.0







