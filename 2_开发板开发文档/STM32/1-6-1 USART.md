- USART（Universal Synchronous/Asynchronous Receiver/Transmitter）通用同步/异步收发器
- USART是STM32内部集成的硬件外设，可根据数据寄存器的一个字节数据自动生成数据帧时序，从TX引脚发送出去，也可自动接收RX引脚的数据帧时序，拼接为一个字节数据，存放在数据寄存器里
- 自带波特率发生器，最高达4.5Mbits/s
- 可配置数据位长度（8/9）、停止位长度（0.5/1/1.5/2）
- 可选校验位（无校验/奇校验/偶校验）
- 支持同步模式、硬件流控制、DMA、智能卡、IrDA、LIN
![[Pasted image 20250313210450.png]]
# USART基本结构
![[Pasted image 20250313211012.png]]
# 数据帧
![[Pasted image 20250313211042.png]]
![[Pasted image 20250313211128.png]]
# 起始位侦测
![[Pasted image 20250313211148.png]]
# 数据采样
![[Pasted image 20250313211202.png]]
# 波特率发生器
![[Pasted image 20250313211249.png]]
# 数据模式
![[Pasted image 20250313211314.png]]
# HEX数据包
![[Pasted image 20250313211335.png]]
# 文本数据包
![[Pasted image 20250313211356.png]]
# HEX数据包接收
![[Pasted image 20250313212054.png]]
# 文本数据包接收
![[Pasted image 20250313212116.png]]

# USART的使用
***
函数使用概括性总结，操作步骤为：
从该代码可以看出，其初始化串口的过程，和我们前面介绍的一致。我们用标号①~⑥标示了顺序
**① 串口时钟使能， GPIO时钟使能**
**② 串口复位**
**③ GPIO端口模式设置**
**④ 串口参数初始化**
**⑤ 初始化 NVIC并且开启中断**
**⑥ 使能串口**

***
# USART在库函数下的使用
函数和定义主要分布在`stm32f10x_usart.h`和 `stm32f10x_usart.c`文件中。
1. **串口时钟使能**：串口是挂载在 APB2下面的外设，所以使能函数为：
```c
RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1);
```
2. **串口复位**。 当外设出现异常的时候可以通过复位设置，实现该外设的复位，然后重新配置这个外设达到让其重新工作的目的。一般在系统刚开始配置外设的时候，都会先执行复位该外设的操作。复位的是在函数 USART_DeInit()中完成：
```C
void USART_DeInit(USART_TypeDef* USARTx);//串口复位
```
3. **串口参数初始化**。 串口初始化是通过 USART_Init()函数实现的，
```C
void USART_Init(USART_TypeDef* USARTx, USART_InitTypeDef* USART_InitStruct);
```
这个函数的的第一个入口参数是指定初始化的串口标号，这里选择
USART1。
第二个入口参数是一个USART_InitTypeDef类型的结构体指针，这个结构体指针的成员变量用来设置串口的一些参数。一般的实现格式为：
```c
USART_InitStructure.USART_BaudRate = bound; //波特率 ; 
USART_InitStructure.USART_WordLength = USART_WordLength_8b;//字长为 8位数据格式
USART_InitStructure.USART_StopBits = USART_StopBits_1; //一个停止位
USART_InitStructure.USART_Parity = USART_Parity_No; //无奇偶校验位
USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None; //无硬件数据流控制
USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx; //收发模式

USART_Init(USART1, &USART_InitStructure); //初始化串口
```

4. **数据发送与接收**。 
STM32的发送与接收是通过数据寄存器 USART_DR来实现的，这一个双寄存器，包含了 TDR和 RDR。当向该寄存器写数据的时候，串口就会自动发送，当收到收据的时候，也是存在该寄存器内。STM32库函数操作 USART_DR寄存器发送数据的函数是：
```c
void USART_SendData(USART_TypeDef* USARTx, uint16_t Data);
```
通过该函数向串口寄存器USART_DR写入一个数据。STM32库函数操作 USART_DR寄存器读取串口接收到的数据的函数是：
```c
uint16_t USART_ReceiveData(USART_TypeDef* USARTx);
```
通过该函数可以读取串口接受到的数据。
5. **串口状态**。 串口的状态可以通过状态寄存器 USART_SR读取。
 ![[Pasted image 20250607160228.png]]
这里我们关注一下两个位，第5、 6位 RXNE和 TC。
- RXNE（读数据寄存器非空），当该位被置 1的时候，就是提示已经有数据被接收到了，并且可以读出来了。这时候我们要做的就是尽快去读取 USART_DR，通过读 USART_DR可以将该位清零，也可以向该位写 0，直接清除。
- TC（发送完成），当该位被置位的时候，表示 USART_DR内的数据已经被发送完成了。如果设置了这个位的中断，则会产生中断。该位也有两种清零方式： 1）读 USART_SR，写USART_DR。 2）直接向该位写 0。
在我们固件库函数里面，读取串口状态的函数是：
```c
FlagStatus USART_GetFlagStatus(USART_TypeDef* USARTx, uint16_t USART_FLAG);
```
这个函数的第二个入口参数非常关键，它是标示我们要查看串口的哪种状态，比如上面讲解的RXNE(读数据寄存器非空 )以及 TC(发送完成 )。例如我们要判断读寄存器是否非空 (RXNE)，操作库函数的方法是：
```C
USART_GetFlagStatus(USART1, USART_FLAG_RXNE);
```
我们要判断发送是否完成(TC)，操作库函数的方法是
```c
USART_GetFlagStatus(USART1, USART_FLAG_TC);
```
6. **串口使能**。 串口使能是通过函数 `USART_Cmd()`来实现的，这个很容易理解，使用方法是：
```c
USART_Cmd(USART1, ENABLE); //使能串口
```
7. **开启串口响应中断**。 有些时候当我们还需要开启串口中断，那么我们还需要使能串口中断，使能串口中断的函数是：
```c
void USART_ITConfig(USART_TypeDef* USARTx, uint16_t USART_IT, FunctionalState NewState);
```
这个函数的第二个入口参数是标示使能串口的类型，也就是使能哪种中断，因为串口的中断类型有很多种。比如
- 在接收到数据的时候（ RXNE读数据寄存器非空），我们要产生中断，那么我们开启中断的方法是：
```c
USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);//开启中断，接收到数据中断
```
- 我们在发送数据结束的时候（TC，发送完成）要产生中断，那么方法是
```c
USART_ITConfig(USART1 USART_IT_TC ENABLE);
```
8. **获取相应中断状态**。 当我们使能了某个中断的时候，当该中断发生了，就会设置状态寄存器中的某个标志位。经常我们在中断处理函数中，要判断该中断是哪种中断，使用的函数是：
```c
ITStatus USART_GetITStatus(USART_TypeDef* USARTx, uint16_t USART_IT);
```
比如我们使能了串口发送完成中断，那么当中断发生了，我们便可以在中断处理函数中调用这个函数来判断到底是否是串口发送完成中断，方法是：
```c
USART_GetITStatus(USART1, USART_IT_TC);
```
返回值是`SET`，说明是串口发送完成中断发生。
## 整体代码
```c
//初始化 IO 串口 1 //bound:波特率
void uart_init(u32 bound) { 
GPIO_InitTypeDef GPIO_InitStructure; 
USART_InitTypeDef USART_InitStructure; 
NVIC_InitTypeDef NVIC_InitStructure; 
//①串口时钟使能， GPIO时钟使能，复用时钟使能
RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1| RCC_APB2Periph_GPIOA, ENABLE); //使能 USART1,GPIOA时钟
//②串口复位
USART_DeInit(USART1); //复位串口 1 
//③ GPIO端口模式设置
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9; //ISART1_TX PA.9
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; 
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP; //复用推挽输出
GPIO_Init(GPIOA, &GPIO_InitStructure); //初始化 GPIOA.9 

GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10; //USART1_RX PA.10 
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING; //浮空输入
GPIO_Init(GPIOA, &GPIO_InitStructure); //初始化 GPIOA.10 //④串口参数初始化

USART_InitStructure.USART_BaudRate = bound; //波特率 设置
USART_InitStructure.USART_WordLength = USART_WordLength_8b; //字长为 8位
USART_InitStructure.USART_StopBits = USART_StopBits_1; //一个停止位
USART_InitStructure.USART_Parity = USART_Parity_No; //无奇偶校验位
USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None; //无硬件数据流控制
USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;//收发模式
USART_Init(USART1, &USART_InitStructure); //初始化串口
#if EN_USART1_RX //如果使能了接收
//⑤初始化 
NVIC NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn; NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority=3 ; //抢占优先级 3 
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 3; //子优先级 3 
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE; //IRQ通道使能
NVIC_Init(&NVIC_InitStructure); //中断优先级初始化
//⑤开启中断
USART_ITConfig(USART1, USART_IT_RXNE, ENABLE); //开启中断
#endif 
//⑥使能串口
USART_Cmd(USART1, ENABLE); //使能串口
}
```
## 注意
- 因为我们使用到了串口的中断接收，必须在`usart.h`里面设置`EN_USART1_RX`为 1（默认设置就是 1的） 。该函数才会配置中断使能，以及开启串口 1的NVIC中断。这里我们把串口 1中断放在组 2，优先级设置为组 2里面的最低。