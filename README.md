#                                                        220303's Kernel Rust



![logo](./Document/Images_Videos/项目封面.jpg)





## 摘要

An operating system kernel written by Rust.

以Risc-V为目标平台，基于Rust SBI，用Rust编写一个操作系统，基本对标2025年NSCSCC内核实现赛道，但主要是根据我个人喜好进行编写。



## 技术概览

* #### Rust SBI

* #### sbi-rt

  对 Rust SBI 进行进一步的封装，避免编写汇编代码。

* #### SV39 分页



## 文档

#### 说明：这篇文档是对我编写 220303's Kernel R 这个项目过程的一个总结，假设我能未卜先知，我会按照这个操作顺序完成整个项目。因此本文档可以用作教程。

* #### [环境配置](./Document/环境配置.md)

* #### [上板运行](Document\上板运行.md) 



## 历史

#### 时间顺序：最新的在最下面

* 在很早之前，我读 \<CSAPP>，\<Operating System: Three Easy Pieces>，< Computer Organization and Design: The Hardware/Software Interface, RISC-V Edition > 等书籍的时候，就想到要实现一个自己的操作系统，并且我比较青睐于Rust 和 Risc-V。

* 2025年4月3日，我创建了一个名为`220303's Kernel C`的项目 (Github仓库: [220303/220303-Kernel-C: An operating system kernel by C .](https://github.com/220303/220303-Kernel-C))，该项目基于x86架构用C语言实现了一个简单的内核 (与其说是内核不如说是裸机程序，仅支持VGA输出)，该项目仅耗时一天 (4月3日，大约15小时)。

* 在该项目完成后的几天内，我创建了本项目，此时我正高二，17周岁。
