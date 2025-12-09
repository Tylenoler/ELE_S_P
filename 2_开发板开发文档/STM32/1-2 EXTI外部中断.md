# 简介
- **中断**：在主程序运行过程中，出现了特定的中断触发条件（中断源），使得CPU暂停当前正在运行的程序，转而去处理中断程序，处理完成后又返回原来被暂停的位置继续运行。
- **中断优先级**：当有多个中断源同时申请中断时，CPU会根据中断源的轻重缓急进行裁决，优先响应更加紧急的中断源。
- **中断嵌套**：当一个中断程序正在进行时，又有新的更高优先级的中断源申请中断，CPU再次暂停当前中断程序，转而去处理新的中断程序，处理完成后依次进行返回
# 中断执行流程
![[Pasted image 20250309223209.png]]
# Stm32中断
- 共有68个可屏蔽中断通道，包括EXTI、TIM、ADC、USART、SPI、I2C、RTC等多个外设
- 使用NVIC统一管理中断，每个中断通道都拥有16个可编程的优先等级，可对优先级进行分组，进一步设置抢占式优先级和响应式优先级
![[Pasted image 20250309223918.png]]
![[Pasted image 20250309223923.png]]
![[Pasted image 20250309223929.png]]
![[Pasted image 20250309223934.png]]

# NVIC基本结构
![[Pasted image 20250309224351.png]]
# NVIC优先级分组
- NVIC的中断优先级由优先级寄存器的4位（0~15）决定，这4位可以进行切分，分为高n位的抢占优先级和低4-n位的响应优先级
- 抢占优先级高的可以中断嵌套，响应优先级高的可以优先排队，抢占优先级和响应优先级均相同的按中断号排队

| 分组方式 |   抢占优先级    |   响应优先级    |
| :--: | :--------: | :--------: |
| 分组0  |  0位，取值为0   | 4位，取值为0~15 |
| 分组1  | 1位，取值为0~1  | 3位，取值为0~7  |
| 分组2  | 2位，取值为0~3  | 2位，取值为0~3  |
| 分组3  | 3位，取值为0~7  | 1位，取值为0~1  |
| 分组4  | 4位，取值为0~15 |  0位，取值为0   |
# EXTI简介
- EXTI外部中断
- EXTI可以监测指定GPIO口的电平信号，当期指定的GPIO口产生点评变化时，EXTI将立即向NVIC发出中断申请，经过NVIC裁决后即可中断CPU主程序，使CPU执行EXTI对应的中断程序
- 支持的触发方式：上升沿/下降沿/双边沿/软件触发
- 支持的GPIO口：所有GPIO口，但相同的Pin不能同时触发中断
- 通道数：16个GPIO_Pin，外加PVD输出、RTC闹钟、USB唤醒、以太网唤醒
- 触发响应方式：中断响应/事件响应
# EXTI基本结构
![[Pasted image 20250309230042.png]]
## AFIO复用IO口
- AFIO主要用于引脚复用功能的选择和重定义
- 在STM32种，AFIO主要完成两个任务：复用功能引脚重映射、中断引脚选择
STM32F103的 19个外部中断为：

线0~15：对应外部 IO口的输入中断。
线16：连接到 PVD输出。
线17：连接到 RTC闹钟事件。
线18：连接到 USB唤醒事件。

从上面可以看出， STM32供 IO口使用的中断线只有 16个，但是 STM32的 IO口却远远不止 16个，那么 STM32是怎么把 16个中断线和 IO口一一对应起来的呢？
于是 STM32就这样设计， GPIO的管脚 GPIOx.0~GPIOx.15(x=A,B,C,D,E F,G)分别对应中断线 15~0。这样每个中断线对应了最多 7个 IO口，
以线 0为例：它对应了 GPIOA.0、 GPIOB.0、 GPIOC.0、 GPIOD.0、GPIOE.0、 GPIOF.0、 GPIOG.0。而中断线每次只能连接到 1个 IO口上，这样就需要通过配置来决定对应的中断线配置到哪个 GPIO上了。下面我们看看 GPIO跟中断线的映射关系图：
![[Pasted image 20250309230310.png]]
# EXTI框图
![[Pasted image 20250309230328.png]]



总结一下使用 IO口外部中断的一般步骤：
1）初始化 IO口为输入。
2）开启 IO口复用时钟，设置 IO口与中断线的映射关系。
3）初始化线上中断，设置触发条件等。
4）配置中断分组 (NVIC），并使能中断。
5）编写中断服务函数。




这章的代码主要分布在固件库的stm32f10x_exti.h和 stm32f10x_exti.c文件中。

在库函数中，配置GPIO与中断线的映射关系的函数 `GPIO_EXTILineConfig()`来实现的：
```c
void GPIO_EXTILineConfig(uint8_t GPIO_PortSource, uint8_t GPIO_PinSource);
```
该函数将GPIO端口与中断线映射起来，使用范例是：
```c
GPIO_EXTILineConfig(GPIO_PortSourceGPIOE,GPIO_PinSource2);
```
将中断线2与 GPIOE映射起来，那么很显然是 GPIOE.2与 EXTI2中断线连接了。设置好中断线映射之后，那么到底来自这个IO口的中断是通过什么方式触发的呢？接下来我们就要设置该中断线上中断的初始化参数了。
中断线上中断的初始化是通过函数 EXTI_Init()实现的。 EXTI_Init()函数的定义是：
```c
void EXTI_Init(EXTI_InitTypeDef* EXTI_InitStruct);
```
下面我们用一个使用范例来说明这个函数的使用：
```c
EXTI_InitTypeDef EXTI_InitStructure; 
EXTI_InitStructure.EXTI_Line=EXTI_Line4; 
EXTI_InitStructure.EXTI_Mode = EXTI_Mode_Interrupt; 
EXTI_InitStructure.EXTI_Trigger =EXTI_Trigger_Falling; 
EXTI_InitStructure.EXTI_LineCmd = ENABLE; 
EXTI_Init(&EXTI_InitStructure);//根据 EXTI_InitStruct中指定的参数初始化外设 EXTI寄存器
```
结构体 EXTI_InitTypeDef的成员变量：
```c
typedef struct
{ 
uint32_t EXTI_Line; 
EXTIMode_TypeDef EXTI_Mode; 
EXTITrigger_TypeDef EXTI_Trigger; 
FunctionalState EXTI_LineCmd; 
}EXTI_InitTypeDef;
```
从定义可以看出，有4个参数需要设置。
第一个参数是中断线的标号，取值范围为`EXTI_Line0~EXTI_Line15`。这个在上面已经讲过中断线的概念。也就是说，这个函数配置的是某个中断线上的中断参数。
第二个参数是中断模式，可选值为中断 `EXTI_Mode_Interrupt`和事件 `EXTI_Mode_Event`。
第三个参数是触发方式，可以是下降沿触发 `EXTI_Trigger_Falling`，上升沿触发 `EXTI_Trigger_Rising`，或者任意电平（上升沿和下降沿）触发`EXTI_Trigger_Rising_Falling`。
最后一个参数就是使能中断线

既然是外部中断，涉及到中断我们当然还要设置 NVIC中断优先级。
```c
NVIC_InitTypeDef NVIC_InitStructure; 
NVIC_InitStructure.NVIC_IRQChannel = EXTI2_IRQn; //使能按键外部中断通道
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0x02; //抢占优先级 2
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 0x02; //子优先级 2 
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE; //使能外部中断通道

NVIC_Init(&NVIC_InitStructure); //中断优先级分组 初始化
```
STM32的 IO口外部中断函数只有 6个，分别为：
```c
EXPORT EXTI0_IRQHandler 
EXPORT EXTI1_IRQHandler 
EXPORT EXTI2_IRQHandler 
EXPORT EXTI3_IRQHandler 
EXPORT EXTI4_IRQHandler 
EXPORT EXTI9_5_IRQHandler 
EXPORT EXTI15_10_IRQHandler
```
中断线0~4每个中断线对应一个中断函数，中断线5~9共用中断函数`EXTI9_5_IRQHandler`，中断线10~15共用中断函数`EXTI15_10_IRQHandler`。
在编写中断服务函数的时候会经常使用到两个函数:
第一个函数是判断某个中断线上的中断是否发生（标志位是否置位）：
```c
ITStatus EXTI_GetITStatus(uint32_t EXTI_Line);
```
这个函数一般使用在中断服务函数的开头判断中断是否发生。
另一个函数是清除某个中断线上的中断标志位：
```c
void EXTI_ClearITPendingBit(uint32_t EXTI_Line);
```
这个函数一般应用在中断服务函数结束之前，清除中断标志位。
常用的中断服务函数格式为：
```c
void EXTI2_IRQHandler(void) 
{ 
	if(EXTI_GetITStatus(EXTI_Line2)!=RESET)//判断某个线上的中断是否发生
		{ 
			//中断逻辑......
			EXTI_ClearITPendingBit(EXTI_Line2); //清除 LINE上的中断标志位
		} 
}
```
在这里需要说明一下，固件库还提供了两个函数用来判断外部中断状态以及清除外部状态标志位的函数 `EXTI_GetFlagStatus`和 `EXTI_ClearFlag`，他们的作用和前面两个函数的作用类似。
只是在 `EXTI_GetITStatus`函数中会先判 断这种中断是否使能，使能了才去判断中断标志位，而`EXTI_GetFlagStatus`直接用来判断状态标志位。


