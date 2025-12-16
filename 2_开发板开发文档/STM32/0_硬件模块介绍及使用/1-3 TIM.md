

# 简介
- TIM：TIM（Timer）定时器
- 定时器可以对输入的时钟进行计数，并在计数值达到设定值时触发中断
- 16位计数器、预分频器、自动重装寄存器的时基单元，在72MHz计数时钟下可以实现最大59.65s的定时。
- 不仅具备基本的定时中断功能，而且还包含内外时钟源选择、输入捕获、输出比较、编码器接口、主从触发模式等多种功能
- 根据复杂度和应用场景分为高级定时器、通用定时器、基本定时器三种类型
# 定时器类型

|  类型   |         编号          |  总线  |                         功能                          |
| :---: | :-----------------: | :--: | :-------------------------------------------------: |
| 高级定时器 |      TIM1、TIM8      | APB2 |      拥有通用定时器全部功能，并额外具有重复计数器，死区生成，互补输出，刹车输入等功能       |
| 通用定时器 | TIM2、TIM3、TIM4、TIM5 | APB1 | 拥有基本定时器的全部功能，并额外具有内外时钟源选择、输入捕获、输出比较、编码器接口、主从触发模式等功能 |
| 基本定时器 |      TIM6、TIM7      | APB1 |                 拥有定时中断、主模式触发DAC的功能                  |
# 高级定时器
![[Pasted image 20250310100517.png]]
# 通用定时器
![[Pasted image 20250310100529.png]]
# 基本定时器
![[Pasted image 20250310100540.png]]
# 定时中断基本结构
![[Pasted image 20250310100615.png]]
# 预分频器时序
![[Pasted image 20250310100654.png]]
# 计数器时序
![[Pasted image 20250310100715.png]]
# 计数器无预装时序
![[Pasted image 20250310100731.png]]
# 计数器有预装时序
![[Pasted image 20250310100750.png]]
# RCC时钟树
![[Pasted image 20250310100818.png]]
# 输出比较
- OC（Output Compare）输出比较
- 输出比较可以通过比较CNT与CCR寄存器值的关系，来对输出电平进行置1、置0或者翻转的操作，用于输出一定频率和占空比的PWM波形
- 每个高级定时器和通用定时器都拥有4个输出比较通道
- 高级定时器的前三个通道额外拥有死区生成和互补输出的功能
# PWM简介
- PWM（Pulse Width Modulation）脉冲宽度调制
- 在具有惯性的系统中，可以通过对一系列脉冲的宽度进行调制，来等效地获得所需要的模拟参量，常应用于电机控速等领域
- PWM参数：
	- 频率 = 1 / TS            
	- 占空比 = TON / TS          
	- 分辨率 = 占空比变化步距
- ![[Pasted image 20250310103522.png]]
# 输出比较通道（高级定时器）
![[Pasted image 20250310103611.png]]
# 输出比较通道（通用定时器）
![[Pasted image 20250310103635.png]]
# 输出比较模式

|    模式    |                                          描述                                          |
| :------: | :----------------------------------------------------------------------------------: |
|    冻结    |                                  CNT=CCR时，REF保持为原状态                                  |
| 匹配时置有效电平 |                                  CNT=CCR时，REF置有效电平                                   |
| 匹配时置无效电平 |                                  CNT=CCR时，REF置无效电平                                   |
| 匹配时电平翻转  |                                   CNT=CCR时，REF电平翻转                                   |
| 强制为无效电平  |                                 CNT与CCR无效，REF强制为无效电平                                 |
| 强制为有效电平  |                                 CNT与CCR无效，REF强制为有效电平                                 |
|  PWM模式1  | 向上计数：CNT<CCR时，REF置有效电平，CNT≥CCR时，REF置无效电平<br>向下计数：CNT>CCR时，REF置无效电平，CNT≤CCR时，REF置有效电平 |
|  PWM模式2  | 向上计数：CNT<CCR时，REF置无效电平，CNT≥CCR时，REF置有效电平<br>向下计数：CNT>CCR时，REF置有效电平，CNT≤CCR时，REF置无效电平 |

# PWM基本结构
![[Pasted image 20250310104804.png]]
## 参数计算
![[Pasted image 20250310105028.png]]
# 输入捕获简介
- IC（Input Capture）输入捕获
- 输入捕获模式下，当通道输入引脚出现指定电平跳变时，当前CNT的值将被锁存到CCR中，可用于测量PWM波形的频率、占空比、脉冲间隔、电平持续时间等参数
- 每个高级定时器和通用定时器都拥有4个输入捕获通道
- 可配置为PWMI模式，同时测量频率和占空比
- 可配合主从触发模式，实现硬件全自动测量
# 频率测量
![[Pasted image 20250310110433.png]]
# 输入捕获通道
![[Pasted image 20250310110533.png]]
# 主从触发模式
![[Pasted image 20250310110603.png]]
# 输入捕获基本结构
![[Pasted image 20250310110720.png]]
# PWMI基本结构
![[Pasted image 20250310110748.png]]



# STM32通用定时器简介
STM32的通用定时器是一个通过可编程预分频器（ PSC）驱动的 16位自动装载计数器（ CNT）构成。
STM32的通用定时器可以被用于：测量输入信号的脉冲长度 (输入捕获 )或者产生输出波形 (输出比较和 PWM)等。 使用定时器预分频器和 RCC时钟控制器预分频器，脉冲长度和波形周期可以在几个微秒到几个毫秒间调整。 STM32的每个通用定时器都是完全独立的，没有互相共享的任何资源。
STM3的通用 TIMx (TIM2、 TIM3、 TIM4和 TIM5)定时器功能包括：
1. 16位向上、向下、向上 /向下自动装载计数器（ TIMx_CNT）。
2. 16位可编程 (可以实时修改 )预分频器 (TIMx_PSC)，计数器时钟频率的分频系数为 1~65535之间的任意数值。
3. 4个独立通道（ TIMx_CH1~4），这些通道可以用来作为
	A. 输入捕获
	B. 输出比较
	C. PWM生成 (边缘或中间对齐模式 )
	D. 单脉冲模式输出
4. 可使用外部信号（TIMx_ETR）控制定时器和定时器互连（可以用 1个定时器控制另外一个定时器）的同步电路。
5. 如下事件发生时产生中断 /DMA
	A．更新：计数器向上溢出 /向下溢出，计数器初始化 (通过软件或者内部 /外部触发 )
	B．触发事件 (计数器启动、停止、初始化或者由内部 /外部触发计数 )
	C．输入捕获
	D．输出比较
	E．支持针对定位的增量 (正交 )编码器和霍尔传感器电路
	F．触发输入作为外部时钟或者按周期的电流管理
## 
下面我们介绍一下与我们这章的实验密切相关的几个通用定时器的寄存器。
![[Pasted image 20250608112050.png]]
在本实验中，我们只用到了 TIMx_CR1的最低位，也就是计数器使能位，该位必须置 1才能让定时器开始计数。 接下来介绍第二个与我们这 章 密切相关的寄存器： DMA/中断使能寄存器（ TIMx_DIER）。该寄存器是一个 16位的寄存器，其各位描述如 图所示
![[Pasted image 20250608112127.png]]
这里我们同样 仅关心它的第 0位， 该位是更新中断允许位， 本章 用到的是 定时器 的更新 中断， 所以该位要设置为 1，来允许由于更新事件 所 产生的中断。接下来我们看第三个与我们这章 有关的寄存器：预分频寄存器（ TIMx_PSC）。该寄存器用设置对时钟进行分频，然后提供给计数器，作为计数器的时钟。该寄存器的各位描述如 图 12.1.3所示
![[Pasted image 20250608112153.png]]
这里，定时器的时钟来源有 4个：
1.  内部时钟（ CK_INT
2. 外部时钟模式 1：外部输入脚 TIx
3. 外部时钟模式 2：外部触发输入 ETR
4. 内部触发输入（ ITRx）：使用 A定时器作为 B定时器的预分频器（ A为 B提供时钟）。
这些时钟，具体选择哪个可以通过TIMx_SMCR寄存器的相关位来设置。这里的 CK_INT时钟是从 APB1倍频的来的， STM32中 除非 APB1的时钟分频数设置为 1，否则通用定时器 TIMx的时钟是 APB1时钟的 2倍，当 APB1的时钟不分频的时候，通用定时器 TIMx的时钟就等于APB1的时钟。这里还要注意的就是高级定时器的时钟不是来自 APB1，而是来自 APB2的。


定时器相关的库函数主要集中在固件库文件 stm32f10x_tim.h和 stm32f10x_tim.c文件中。
1. TIM3时钟使能。
TIM3是挂载在 APB1之下，所以我们通过 APB1总线下的时钟 使能函数来使能 TIM3。调用的函数是：
```c
RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM3, ENABLE); //时钟使能
```
2. 初始化定时器参 数 ,设置 自动重装值 分频系数 ，计数方式 等 。
在库函数中，定时器的初始化参数是通过初始化函数TIM_TimeBaseInit实现的：
```c
voidTIM_TimeBaseInit(TIM_TypeDef*TIMx, TIM_TimeBaseInitTypeDef* TIM_TimeBaseInitStruct);
```
第一个参数是确定是哪个定时器，这个比较容易理解。
第二个参数是定时器初始化参数结构体指针，结构体类型为 TIM_TimeBaseInitTypeDef，下面我们看看这个结构体的定义
```c
typedef struct 
{ 
uint16_t TIM_Prescaler; 
uint16_t TIM_CounterMode; 
uint16_t TIM_Period; 
uint16_t TIM_ClockDivision; 
uint8_t TIM_RepetitionCounter; 
} TIM_TimeBaseInitTypeDef;
```
这个结构体一共有5个成员变量，要说明的是，对于通用定时器只有前面四个参数有用，最后一个参数 TIM_RepetitionCounter是高级定时器才有用的，这里不多解释。
- 第一个参数TIM_Prescaler是用来设置分频系数的，刚才上面有讲解。
- 第二个参数TIM_CounterMode是用来设置计数方式，上面讲解过，可以设置为向上计数，向下计数方式还有中央对齐计数方式，比较常用的是向上计数模式 TIM_CounterMode_Up和向下计数模式 TIM_CounterMode_Down。
- 第三个参数是设置自动重载计数周期值，这在前面也已经讲解过。
- 第四个参数是用来设置时钟分频因子。
针对TIM3初始化范例代码格式：
```c
TIM_TimeBaseInitTypeDef TIM_TimeBaseStructure; TIM_TimeBaseStructure.TIM_Period = 5000; TIM_TimeBaseStructure.TIM_Prescaler =7199; TIM_TimeBaseStructure.TIM_ClockDivision = TIM_CKD_DIV1; TIM_TimeBaseStructure.TIM_CounterMode = TIM_CounterMode_Up; TIM_TimeBaseInit(TIM3, &TIM_TimeBaseStructure);
```
3. 设置 TIM3_DIER允许更新中断。
因为我们要使用TIM3的更新中断，寄存器的相应位便可使能更新中断。在库函数里面定时器中断使能是通过 TIM_ITConfig函数来实现的：
```c
void TIM_ITConfig(TIM_TypeDef* TIMx, uint16_t TIM_IT, FunctionalState NewState);
```
第一个参数是选择定时器号，这个容易理解，取值为TIM1~TIM17。
第二个参数非常关键，是用来指明我们使能的定时器中断的类型，定时器中断的类型有很多种，包括更新中断 TIM_IT_Update，触发中断 TIM_IT_Trigger，以及输入捕获中断等等。
第三个参数就很简单了，就是失能还是使能。例如我们要使能TIM3的更新中断，格式为：
```c
TIM_ITConfig(TIM3,TIM_IT_Update,ENABLE );
```
4. TIM3中断优先级设置。在定时器中断使能之后，因为要产生中断，必不可少的要设置NVIC相关寄存器，设置中断优先级。之前多次讲解到用 NVIC_Init函数实现中断优先级的设置，这里就不重复讲解。
5. 允许 TIM3工作，也就是使能 TIM3。光配置好定时器还不行，没有开启定时器，照样不能用。我们在配置完后要开启定时器，通过 TIM3_CR1的 CEN位来设置。在固件库里面使能定时器的函数是通过 TIM_Cmd函数来实现的：
```c
void TIM_Cmd(TIM_TypeDef* TIMx, FunctionalState NewState)
```
这个函数非常简单，比如我们要使能定时器3，方法为
```c
TIM_Cmd(TIM3, ENABLE); //使能 TIMx外设
```
6. 编写中断服务函数。在最后，还是要编写定时器中断服务函数，通过该函数来处理定时器产生的相关中断。在中断产生后，通过状态寄存器的值来判断此次产生的中断属于什么类型。然后执行相关的操作，我们这里使用的是更新（溢出）中断，所以在状态寄存器 SR的最低位。在处理完中断之后应该向 TIM3_SR的最低位写 0，来清除该中断标志。在固件库函数里面，用来读取中断状态寄存器的值判断中断类型的函数是：
```c
ITStatus TIM_GetITStatus(TIM_TypeDef* TIMx, uint16_t)
```
该函数的作用是，判断定时器TIMx的中断类型 TIM_IT是否发生中断。比如，我们要判断定时器 3是否发生更新（溢出）中断，方法为：
```c
if (TIM_GetITStatus(TIM3, TIM_IT_Update) != RESET){}
```
固件库中清除中断标志位的函数是：
```c
void TIM_ClearITPendingBit(TIM_TypeDef* TIMx, uint16_t TIM_IT)
```
该函数的作用是，清除定时器TIMx的中断 TIM_IT标志位。使用起来非常 简单，比如我们在TIM3的溢出中断发生后，我们要清除中断标志位，方法是：
```c
TIM_ClearITPendingBit(TIM3, TIM_IT_Update );
```
这里需要说明一下，固件库还提供了两个函数用来判断定时器状态以及清除定时器状态标志位的函数 TIM_GetFlagStatus和 TIM_ClearFlag，他们的作用和前面两个函数的作用类似。只是在 TIM_GetITStatus函数中会先判断这种中断是否使能，使能了才去判断中断标志位，而TIM_GetFlagStatus直接用来判断状态标志位。
