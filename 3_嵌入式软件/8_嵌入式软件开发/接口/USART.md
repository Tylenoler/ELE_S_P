# USART在STM32开发板上的使用
## 接口

|   接口    |  定义  |
| :-----: | :--: |
| STM32RX | 接收数据 |
| STM32TX | 发送数据 |
## 具体步骤/代码
### 1. 串口进行初始化
```c
void Serial_Init(void)
{
	/*开启时钟*/
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1, ENABLE);	//开启USART1的时钟
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);	//开启GPIOA的时钟
	
	/*GPIO初始化*/
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);					//将PA9引脚初始化为复用推挽输出
	
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);					//将PA10引脚初始化为上拉输入
	
	
	/*USART初始化*/
	USART_InitTypeDef USART_InitStructure;					//定义结构体变量
	USART_InitStructure.USART_BaudRate = 9600;				//波特率
	USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;	//硬件流控制，不需要
	USART_InitStructure.USART_Mode = USART_Mode_Tx | USART_Mode_Rx;	//模式，发送模式和接收模式均选择
	USART_InitStructure.USART_Parity = USART_Parity_No;		//奇偶校验，不需要
	USART_InitStructure.USART_StopBits = USART_StopBits_1;	//停止位，选择1位
	USART_InitStructure.USART_WordLength = USART_WordLength_8b;		//字长，选择8位
	USART_Init(USART1, &USART_InitStructure);				//将结构体变量交给USART_Init，配置USART1
	
	/*中断输出配置*/
	USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);			//开启串口接收数据的中断
	
	/*NVIC中断分组*/
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);			//配置NVIC为分组2
	
	/*NVIC配置*/
	NVIC_InitTypeDef NVIC_InitStructure;					//定义结构体变量
	NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn;		//选择配置NVIC的USART1线
	NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;			//指定NVIC线路使能
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;		//指定NVIC线路的抢占优先级为1
	NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;		//指定NVIC线路的响应优先级为1
	NVIC_Init(&NVIC_InitStructure);							//将结构体变量交给NVIC_Init，配置NVIC外设
	
	/*USART使能*/
	USART_Cmd(USART1, ENABLE);								//使能USART1，串口开始运行
}
```
#### 详细解释：
这个函数 `Serial_Init()` 是用来初始化 STM32 微控制器的 USART1 串口通信功能的。USART（Universal Synchronous/Asynchronous Receiver/Transmitter）是一种常见的串行通信接口，用于微控制器与其他设备（如传感器、计算机等）之间的数据传输。下面是对代码的详细解释：

这个函数的主要作用是初始化 USART1 串口通信功能，包括配置 GPIO 引脚、设置 USART 参数、配置中断和 NVIC，最后使能 USART1。通过这些配置，USART1 可以以 9600 的波特率进行全双工通信，并且在接收到数据时触发中断。
##### 1. 开启时钟
```c
RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1, ENABLE);	//开启USART1的时钟
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);	//开启GPIOA的时钟
```
- **RCC_APB2PeriphClockCmd** 是 STM32 标准库中的一个函数，用于使能或禁用 APB2 总线上的外设时钟。
- `RCC_APB2Periph_USART1` 和 `RCC_APB2Periph_GPIOA` 是宏定义，分别表示 USART1 和 GPIOA 的时钟。
- 这两行代码的作用是开启 USART1 和 GPIOA 的时钟，因为 USART1 和 GPIOA 都需要时钟信号才能正常工作。
##### 2. GPIO 初始化
```c
GPIO_InitTypeDef GPIO_InitStructure;
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9;
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
GPIO_Init(GPIOA, &GPIO_InitStructure);					//将PA9引脚初始化为复用推挽输出

GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_10;
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
GPIO_Init(GPIOA, &GPIO_InitStructure);					//将PA10引脚初始化为上拉输入
```
- **GPIO_InitTypeDef** 是一个结构体类型，用于配置 GPIO 引脚的模式、速度等参数。
- **GPIO_Mode_AF_PP** 表示将 GPIO 引脚配置为复用推挽输出模式。PA9 是 USART1 的 TX（发送）引脚，因此需要配置为输出模式。
- **GPIO_Mode_IPU** 表示将 GPIO 引脚配置为上拉输入模式。PA10 是 USART1 的 RX（接收）引脚，因此需要配置为输入模式。
- **GPIO_Speed_50MHz** 表示 GPIO 引脚的输出速度为 50MHz。
- **GPIO_Init** 函数用于根据 `GPIO_InitStructure` 结构体中的配置初始化 GPIO 引脚。
##### 3. USART 初始化
```c
USART_InitTypeDef USART_InitStructure;					//定义结构体变量
USART_InitStructure.USART_BaudRate = 9600;				//波特率
USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;	//硬件流控制，不需要
USART_InitStructure.USART_Mode = USART_Mode_Tx | USART_Mode_Rx;	//模式，发送模式和接收模式均选择
USART_InitStructure.USART_Parity = USART_Parity_No;		//奇偶校验，不需要
USART_InitStructure.USART_StopBits = USART_StopBits_1;	//停止位，选择1位
USART_InitStructure.USART_WordLength = USART_WordLength_8b;		//字长，选择8位
USART_Init(USART1, &USART_InitStructure);				//将结构体变量交给USART_Init，配置USART1
```
- **USART_InitTypeDef** 是一个结构体类型，用于配置 USART 的波特率、数据位、停止位、校验位等参数。
- **USART_BaudRate** 设置为 9600，表示通信的波特率为 9600 bps。
- **USART_HardwareFlowControl** 设置为 `USART_HardwareFlowControl_None`，表示不使用硬件流控制。
- **USART_Mode** 设置为 `USART_Mode_Tx | USART_Mode_Rx`，表示同时启用发送和接收模式。
- **USART_Parity** 设置为 `USART_Parity_No`，表示不使用奇偶校验。
- **USART_StopBits** 设置为 `USART_StopBits_1`，表示使用 1 位停止位。
- **USART_WordLength** 设置为 `USART_WordLength_8b`，表示数据帧长度为 8 位。
- **USART_Init** 函数用于根据 `USART_InitStructure` 结构体中的配置初始化 USART1。
##### 4. 中断输出配置
```c
USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);			//开启串口接收数据的中断
```
- **USART_ITConfig** 函数用于配置 USART 的中断。
- `USART_IT_RXNE` 表示接收数据寄存器非空中断，即当 USART 接收到数据时触发中断。
- 这行代码的作用是使能 USART1 的接收中断，当 USART1 接收到数据时，会触发中断。
##### 5. NVIC 中断分组
```c
NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);			//配置NVIC为分组2
```
- **NVIC_PriorityGroupConfig** 函数用于配置 NVIC（Nested Vectored Interrupt Controller）的中断优先级分组。
- `NVIC_PriorityGroup_2` 表示将中断优先级分为 2 位抢占优先级和 2 位响应优先级。
##### 6. NVIC 配置
```c
NVIC_InitTypeDef NVIC_InitStructure;					//定义结构体变量
NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn;		//选择配置NVIC的USART1线
NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;			//指定NVIC线路使能
NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;		//指定NVIC线路的抢占优先级为1
NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;		//指定NVIC线路的响应优先级为1
NVIC_Init(&NVIC_InitStructure);							//将结构体变量交给NVIC_Init，配置NVIC外设
```
- **NVIC_InitTypeDef** 是一个结构体类型，用于配置 NVIC 的中断通道、优先级等参数。
- `USART1_IRQn` 表示 USART1 的中断通道。
- `NVIC_IRQChannelCmd` 设置为 `ENABLE`，表示使能该中断通道。
- `NVIC_IRQChannelPreemptionPriority` 设置为 1，表示抢占优先级为 1。
- `NVIC_IRQChannelSubPriority` 设置为 1，表示响应优先级为 1。
- **NVIC_Init** 函数用于根据 `NVIC_InitStructure` 结构体中的配置初始化 NVIC。
##### 7. USART 使能
```c
USART_Cmd(USART1, ENABLE);								//使能USART1，串口开始运行
```
- **USART_Cmd** 函数用于使能或禁用 USART。
- 这行代码的作用是使能 USART1，使其开始工作。

### 2. 串口中断配置
这个中断服务程序通过状态机的思路，依次处理数据包的不同部分：包头、数据、包尾。状态机的状态由`RxState`变量控制，根据不同的状态执行不同的操作。数据包的结构为：`@`（包头） + 数据 + `$`（第一个包尾） + `#`（第二个包尾）。成功接收一个完整的数据包后，`Serial_RxFlag`被置1，表示可以处理这个数据包。
```c
void USART1_IRQHandler(void)
{
	static uint8_t RxState = 0;		//定义表示当前状态机状态的静态变量
	static uint8_t pRxPacket = 0;	//定义表示当前接收数据位置的静态变量
	if (USART_GetITStatus(USART1, USART_IT_RXNE) == SET)	//判断是否是USART1的接收事件触发的中断
	{
		uint8_t RxData = USART_ReceiveData(USART1);			//读取数据寄存器，存放在接收的数据变量
		
		/*使用状态机的思路，依次处理数据包的不同部分*/
		
		/*当前状态为0，接收数据包包头*/
		if (RxState == 0)
		{
			if (RxData == '@' && Serial_RxFlag == 0)//如果数据确实是包头，并且上一个数据包已处理完毕
			{
				RxState = 1;			//置下一个状态
				pRxPacket = 0;			//数据包的位置归零
			}
		}
		/*当前状态为1，接收数据包数据，同时判断是否接收到了第一个包尾*/
		else if (RxState == 1)
		{
			if (RxData == '$')			//如果收到第一个包尾
			{
				RxState = 2;			//置下一个状态
			}
			else						//接收到了正常的数据
			{
				Serial_RxPacket[pRxPacket] = RxData;		//将数据存入数据包数组的指定位置
				pRxPacket ++;			//数据包的位置自增
			}
		}
		/*当前状态为2，接收数据包第二个包尾*/
		else if (RxState == 2)
		{
			if (RxData == '#')			//如果收到第二个包尾
			{
				RxState = 0;			//状态归0
				Serial_RxPacket[pRxPacket] = '\0';		//将收到的字符数据包添加一个字符串结束标志
				Serial_RxFlag = 1;		//接收数据包标志位置1，成功接收一个数据包
			}
		}
		
		USART_ClearITPendingBit(USART1, USART_IT_RXNE);		//清除标志位
	}
}
```
#### 详细解释
这个代码是一个用于处理USART1接收中断的中断服务程序（ISR），它通过状态机的思路来解析接收到的数据包。以下是对代码的详细解释：
##### 1. **变量定义**
   ```c
   static uint8_t RxState = 0;		//定义表示当前状态机状态的静态变量
   static uint8_t pRxPacket = 0;	//定义表示当前接收数据位置的静态变量
   ```
   - `RxState`：用于表示当前状态机的状态。状态机的状态决定了程序如何处理接收到的数据。
   - `pRxPacket`：用于表示当前接收数据包的位置，即数据包数组中的索引。

   `static`关键字表示这两个变量是静态的，它们的值在函数调用之间保持不变。
##### 2. **中断触发条件**
   ```c
   if (USART_GetITStatus(USART1, USART_IT_RXNE) == SET)	//判断是否是USART1的接收事件触发的中断
   ```
   - `USART_GetITStatus(USART1, USART_IT_RXNE)`：检查USART1的接收缓冲区非空中断标志位是否被置位。如果置位，表示有数据到达，触发中断。
   - `USART_IT_RXNE`：表示“接收缓冲区非空”中断。
##### 3. **读取接收到的数据**
   ```c
   uint8_t RxData = USART_ReceiveData(USART1);			//读取数据寄存器，存放在接收的数据变量
   ```
   - `USART_ReceiveData(USART1)`：从USART1的数据寄存器中读取接收到的数据，并将其存储在`RxData`变量中。
##### 4. **状态机处理**
   状态机通过`RxState`的值来决定如何处理接收到的数据。状态机有三个状态：
   - **状态0：接收数据包包头**
     ```c
     if (RxState == 0)
     {
         if (RxData == '@' && Serial_RxFlag == 0)		//如果数据确实是包头，并且上一个数据包已处理完毕
         {
             RxState = 1;			//置下一个状态
             pRxPacket = 0;			//数据包的位置归零
         }
     }
     ```
     - 在状态0，程序等待接收数据包的开始标志`@`。
     - 如果接收到`@`并且`Serial_RxFlag`为0（表示上一个数据包已经处理完毕），则进入状态1，并将数据包位置`pRxPacket`归零。
   - **状态1：接收数据包数据**
     ```c
     else if (RxState == 1)
     {
         if (RxData == '$')			//如果收到第一个包尾
         {
             RxState = 2;			//置下一个状态
         }
         else						//接收到了正常的数据
         {
             Serial_RxPacket[pRxPacket] = RxData;		//将数据存入数据包数组的指定位置
             pRxPacket ++;			//数据包的位置自增
         }
     }
     ```
     - 在状态1，程序接收数据包的实际数据。
     - 如果接收到`$`，表示数据包的第一个包尾，进入状态2。
     - 如果接收到的是正常数据，将其存储在`Serial_RxPacket`数组中，并递增`pRxPacket`。
   - **状态2：接收数据包第二个包尾**
     ```c
     else if (RxState == 2)
     {
         if (RxData == '#')			//如果收到第二个包尾
         {
             RxState = 0;			//状态归0
             Serial_RxPacket[pRxPacket] = '\0';			//将收到的字符数据包添加一个字符串结束标志
             Serial_RxFlag = 1;		//接收数据包标志位置1，成功接收一个数据包
         }
     }
     ```
     - 在状态2，程序等待接收数据包的结束标志`#`。
     - 如果接收到`#`，表示数据包接收完毕，状态机回到状态0。
     - 在数据包的末尾添加字符串结束标志`\0`，并将`Serial_RxFlag`置1，表示成功接收一个数据包。
##### 5. **清除中断标志位**
   ```c
   USART_ClearITPendingBit(USART1, USART_IT_RXNE);		//清除标志位
   ```
   - `USART_ClearITPendingBit(USART1, USART_IT_RXNE)`：清除USART1的接收缓冲区非空中断标志位，以便下一次中断能够正常触发。
