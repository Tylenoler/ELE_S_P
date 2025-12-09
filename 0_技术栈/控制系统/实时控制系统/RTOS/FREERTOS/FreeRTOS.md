***

| [内核](https://zh.wikipedia.org/wiki/%E5%86%85%E6%A0%B8 "内核")类别                                             | [微内核](https://zh.wikipedia.org/wiki/%E5%BE%AE%E5%85%A7%E6%A0%B8 "微内核")          |
| --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| [许可证](https://zh.wikipedia.org/wiki/%E8%BD%AF%E4%BB%B6%E8%AE%B8%E5%8F%AF%E8%AF%81 "软件许可证")                | [MIT许可证](https://zh.wikipedia.org/wiki/MIT%E8%AE%B8%E5%8F%AF%E8%AF%81 "MIT许可证") |
| 官方网站                                                                                                      | [www.freertos.org](https://www.freertos.org/)                                   |
| [仓库](https://zh.wikipedia.org/wiki/%E4%BB%93%E5%BA%93_(%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6) "仓库 (版本控制)") | www.github.com/FreeRTOS/FreeRTOS                                                |

***
**FreeRTOS**是一个热门的[[3]](https://zh.wikipedia.org/wiki/FreeRTOS#cite_note-EETimes2012-3)[嵌入式设备](https://zh.wikipedia.org/wiki/%E5%B5%8C%E5%85%A5%E5%BC%8F%E8%A3%9D%E7%BD%AE "嵌入式设备")用[即时操作系统](https://zh.wikipedia.org/wiki/%E5%8D%B3%E6%99%82%E4%BD%9C%E6%A5%AD%E7%B3%BB%E7%B5%B1 "即时操作系统")核心[[4]](https://zh.wikipedia.org/wiki/FreeRTOS#cite_note-4)[[5]](https://zh.wikipedia.org/wiki/FreeRTOS#cite_note-5)，它于2003年由Richard Barry设计，并已被经成功移植到35种不同的[微控制器](https://zh.wikipedia.org/wiki/%E5%BE%AE%E6%8E%A7%E5%88%B6%E5%99%A8 "微控制器")上[[6]](https://zh.wikipedia.org/wiki/FreeRTOS#cite_note-Official_Website-6)。FreeRTOS采用[MIT许可证](https://zh.wikipedia.org/wiki/MIT%E8%AE%B8%E5%8F%AF%E8%AF%81 "MIT许可证")许可。
# 实现
FreeRTOS的设计小巧且简易，整个核心代码只有3到4个C文件，为了让代码容易阅读、移植和维护，大部分的代码都是以[C语言](https://zh.wikipedia.org/wiki/C%E8%AA%9E%E8%A8%80 "C语言")编写，只有一些函数（多数是架构特定排班副程序）采用[汇编语言](https://zh.wikipedia.org/wiki/%E7%B5%84%E5%90%88%E8%AA%9E%E8%A8%80 "汇编语言")编写。
FreeRTOS提供许多方法以实现多[线程](https://zh.wikipedia.org/wiki/%E7%BA%BF%E7%A8%8B "线程")（threads）、多[作业](https://zh.wikipedia.org/wiki/%E4%BD%9C%E6%A5%AD_(%E9%9B%BB%E8%85%A6) "作业 (电脑)")（task）、[互斥锁](https://zh.wikipedia.org/wiki/%E4%BA%92%E6%96%A5%E9%94%81 "互斥锁")（mutex）、[信号量](https://zh.wikipedia.org/wiki/%E4%BF%A1%E8%99%9F%E6%A8%99 "信号标")（semaphore）和[软件计时器](https://zh.wikipedia.org/wiki/%E8%BB%9F%E9%AB%94%E8%A8%88%E6%99%82%E5%99%A8 "软件计时器")（software timer），有个为低耗电应用程序提供的[无嘀嗒](https://zh.wikipedia.org/wiki/%E6%97%A0%E5%98%80%E5%97%92%E5%86%85%E6%A0%B8 "无嘀嗒内核")（tick-less）模式，线程的优先权管理也有支持，此外，FreeRTOS提供了四种存储器配置的模式：
- 仅配置（allocate only）
- 以非常简易但快速的算法进行配置与释放
- 搭配[存储器合并](https://zh.wikipedia.org/w/index.php?title=%E8%A8%98%E6%86%B6%E9%AB%94%E5%90%88%E4%BD%B5&action=edit&redlink=1)，以较复杂但快速的算法进行配置与释放
- 搭配互斥保护，以 C 函数库配置进行配置与释放
FreeRTOS中没有一些像[Linux](https://zh.wikipedia.org/wiki/Linux "Linux")、[Microsoft Windows](https://zh.wikipedia.org/wiki/Microsoft_Windows "Microsoft Windows")等典型操作系统具有的先进特征，例如[设备驱动程序](https://zh.wikipedia.org/w/index.php?title=%E8%A3%9D%E7%BD%AE%E9%A9%85%E5%8B%95%E7%A8%8B%E5%BC%8F&action=edit&redlink=1)、先进[存储器管理](https://zh.wikipedia.org/wiki/%E8%A8%98%E6%86%B6%E9%AB%94%E7%AE%A1%E7%90%86 "存储器管理")机制、用户管理和网络管理，FreeRTOS着重在执行的简洁与速度，FreeRTOS有时会被视为是一个‘线程函数库’而非‘操作系统’，尽管可以找到[命令行接口](https://zh.wikipedia.org/wiki/%E5%91%BD%E4%BB%A4%E5%88%97%E4%BB%8B%E9%9D%A2 "命令行接口")和类似[POSIX](https://zh.wikipedia.org/wiki/POSIX "POSIX") I/O 接口的插件。
FreeRTOS实现了多线程，主程序会在规律的短时间区间内调用一个线程时计方法，这个方法会以[循环制](https://zh.wikipedia.org/wiki/%E5%BE%AA%E7%92%B0%E5%88%B6 "循环制")依照任务的优先级进行任务切换，一般来说，这个短时间区间介于 1/1000 秒与 1/100 秒之间，透过一个硬件时计中断来计时，但这个区间经常随着特定的应用而改变。
从FreeRTOS官网（[FreeRTOS.org](http://www.freertos.org/))所下载到的代码包含准备用来移植或编译的配置文件和演示代码，让用户可以快速地进行应用程序设计。
