#                             220303's Kernel Rust

![logo](./Images_Videos/项目封面.jpg)



#### 说明：这篇文档是对我编写 220303's Kernel R 这个项目过程的一个总结，假设我能未卜先知，我会按照这个操作顺序完成整个项目。因此本文档可以用作教程。

#### 目标：以Risc-V为目标平台，基于Rust SBI，用Rust编写一个操作系统，基本对标2025年NSCSCC内核实现赛道，但主要是根据我个人喜好进行编写。



## 历史

在很早之前，我读 \<CSAPP>，\<Operating System: Three Easy Pieces>，< Computer Organization and Design: The Hardware/Software Interface, RISC-V Edition > 等书籍的时候，就想到要实现一个自己的操作系统，并且我比较青睐于Rust 和 Risc-V。

2025年4月3日，我创建了一个名为`220303's Kernel C`的项目 (Github仓库: [220303/220303-Kernel-C: An operating system kernel by C .](https://github.com/220303/220303-Kernel-C))，该项目基于x86架构用C语言实现了一个简单的内核 (与其说是内核不如说是裸机程序，仅支持VGA输出)，该项目仅耗时一天 (4月3日，大约15小时)。

在该项目完成后的几天内，我创建了本项目，此时我正高二，17周岁。



## 硬件环境

### 工作平台

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

我买了带有 WiFi 的版本，在后续的内容中也确实需要联网，如果不买WiFi模块，也可考虑用RJ45网口连接双绞线上网 (以太网)。

![logo](.\Images_Videos\开发板正面.jpg)

![开发板侧面](.\Images_Videos\开发板侧面.jpg)

![logo](.\Images_Videos\开发板背面.jpg)

![logo](.\Images_Videos\wifi模块.jpg)



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

  驱动下载地址：[CH341SER.EXE - 南京沁恒微电子股份有限公司](https://www.wch.cn/downloads/CH341SER_EXE.html)

  ![转换器正面](.\Images_Videos\转换器正面.jpg)

  ![logo](.\Images_Videos\转换器反面.jpg)

* 杜邦线

  母对母，长度够用就行，用于连接`USB To 串口 转换板`和`塞昉-星光二代开发板`，至少三根。
  
  ![logo](.\Images_Videos\杜邦线.jpg)
  
  ![logo](.\Images_Videos\杜邦线母头.jpg)
  
  

## 网络环境

### 为了保证您在接下来的软件环境配置中一帆风顺，您需要检查自己的网络环境。如果您的网络环境无法正常访问`Github`等境外网站，那么您需要`VPN`。

* 若您的网络环境连`Github`都无法正常访问，那么`VPN`就无从谈起，所以您需要先使用`Watt Toolkit`使得您能够正常访问`Github`，因为`Watt Toolkit`是一款主要给`Steam`加速的加速器，因此它的官网在墙内，本身也是完全合法的。

  官网：[瓦特工具箱(Steam++官网) - Watt Toolkit](https://steampp.net/)

* 客户端：现在您已经能正常访问`Github`了，在您选取自己的节点组之前，您需要先有能够利用节点组代理您的网络的工具，我个人使用了`clash-verge`，体验感不错。

  官网：[Clash下载：Clash for Windows / Android / Mac 最新官方中文版 - Clash下载站](https://clash.download/)

  Github：[clash-verge-rev/clash-verge-rev at v2.0.1](https://github.com/clash-verge-rev/clash-verge-rev/tree/v2.0.1)

  其他的客户端 (包括不同平台)可以参考这篇博客：[Windows安卓Mac和ios代理客户端软件整理推荐 - 机场推荐与机场评测](https://jichangtuijian.com/proxyclient.html)

* 免费节点：机场的购买官网都在墙外，所以你需要先能翻墙，这看似是个死循环，但好在`Github`上有一些免费节点，您可以搜索`freenode`之类的关键词，可以找到一些结果，下面是我曾经用过的 (现在不一定好用了)。

  [ripaojiedian/freenode: 永久免费订阅/白嫖/节点/vpn/白嫖/订阅/机场/翻墙/加速器/科学上网/教程/破解/软件/资源/网站/ss/ssr/vmess/vless/v2ray/trojan/clash](https://github.com/ripaojiedian/freenode)

  [OpenRunner/clash-freenode: 订阅地址🚀 免费共享♻️ 定期更新✨ 科学上网🌈 请勿滥用🚫一键订阅📪SSR/CLASH/V2RAY](https://github.com/OpenRunner/clash-freenode?tab=readme-ov-file)

  [ID-10086/freenode: V2Ray | Clash 免费节点分享](https://github.com/ID-10086/freenode)

* 机场 (节点组)：现在您已经可以通过免费节点浏览外网了，如果您需要一组稳定、长期可用的节点，那么您可以购买俗称的”机场“，这其中的选择就因人而异了，可以参考这几篇博客：

  [2025机场推荐与机场评测SSR/V2ray/Trojan订阅 - 机场推荐与机场评测](https://jichangtuijian.com/ssr-v2ray专线机场推荐.html)

  [KaWaIDeSuNe/xingjiabijichang: 2025年最新性价比机场推荐VPN、翻墙🚀推荐稳定性高，适合各类人需求的机场](https://github.com/KaWaIDeSuNe/xingjiabijichang?tab=readme-ov-file)

  [🔥 2025年稳定便宜好用的机场推荐（长期更新） - 飞哥的小站](https://feige.blog/archives/e46bc873-ebd7-473c-b5e0-a3f557b769f6)

  我自己用的是悠兔的，不见得有多好，但我觉得算是满足了我的需求，`courseforge`、`Github`等平台都能正常访问，价格也可以接受。



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

### 安装C工具链

* 到 MinGW 官网的下载页面下载安装包 ：[Pre-built Toolchains - mingw-w64](https://www.mingw-w64.org/downloads/)

  选择`LLVM-MinGW`：

  ![MinGW](.\Images_Videos\MinGW.png)

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
  
  # 替换成你偏好的镜像源，这里使用ustc
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

* 官网下载页面：[Download QEMU - QEMU](https://www.qemu.org/download/)

### 安装Virtual Box

* 官网下载页面：[Downloads – Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads)

  下载软件本体和扩展包，并安装扩展包。

* 从7.0版本开始，如果要把Virtual Box安装在非系统盘，需要以管理员身份在`cmd`中执行以下内容 (注意不能在`powershell`中执行，只能在`cmd`中执行)，实例中是安装在 `V:\Virtual Box`

  ```
  icacls "V:\Virtual Box" /reset /t /c
  icacls "V:\Virtual Box" /inheritance:d /t /c
  icacls "V:\Virtual Box" /grant *S-1-5-32-545:(OI)(CI)(RX) /t /c
  icacls "V:\Virtual Box" /deny *S-1-5-32-545:(DE,WD,AD,WEA,WA) /t /c
  icacls "V:\Virtual Box" /grant *S-1-5-11:(OI)(CI)(RX) /t /c
  icacls "V:\Virtual Box" /deny *S-1-5-11:(DE,WD,AD,WEA,WA) /t /c
  ```

### 安装Ubuntu 24.04 LTS：[采用Virtual Box]

* 在Ubuntu官网下载镜像

* 安装到虚拟机

* 改变启动顺序(硬盘最优先)

* [可选]：软件源选择华为(自动测试最快)，可以在”软件与更新“程序中更改(在它检查更新时候暂停 -> 设置 -> Ubuntu软件)

* 将系统及软件更到最新

* 在虚拟机中为Virtual Box 增强扩展 安装前置

  ```
  sudo apt install build-essential dkms linux-headers-$(uname -r)
  ```

* 安装Virtual Box 增强扩展

* 配置共享文件夹 T/ELCO/Computer Science/Operation System

* 挂载点 /home/g220303/kernel

* 添加快照

### 安装 Tera Term

[若您不打算将操作系统在实体开发板上运行，那么可以不准备这部分]

用于与连接开发板进行环境配置和调试操作系统

* Github主页：[TeraTermProject/teraterm](https://github.com/TeraTermProject/teraterm)
