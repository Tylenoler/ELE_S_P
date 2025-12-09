#STM32_GPIO 

# 简介
- GPIO：通用输入输出接口
- 可配置8种输入输出模式
- 引脚电平：0~3.3V，部分引脚可容忍5V
- ***输出模式***：可控制端口输出高低电平，用以驱动LED、控制蜂鸣器、模拟通信协议输出时序等。
- **输入模式**：课读取端口的高低电平或电压，用于读取按键输入、外接模块电平信号输入、ADC电压采集、模拟通信协议接收数据等。
# 基本结构
![[Pasted image 20250309213839.png]]
# GPIO位结构
![[Pasted image 20250309213905.png]]
# GPIO模式
- 通过配置GPIO的端口配置寄存器，端口可以配置成以下8种模式

|    模式名称    |  性质  |            特征             |
| :--------: | :--: | :-----------------------: |
|  **浮空输入**  | 数字输入 |   可读取引脚电平，若引脚悬空，则电平不稳定    |
|  **上拉输入**  | 数字输入 | 可读取引脚电平，内部连接上拉电阻，悬空时默认高电平 |
|  **下拉输入**  | 数字输入 | 可读取引脚电平，内部连接下拉电阻，悬空时默认低电平 |
|  **模拟输入**  | 模拟输入 |  GPIO无效，引脚直接接入内部[[ADC]]   |
|  **开漏输出**  | 数字输出 |  可输出引脚电平，高电平为高阻态，低电平接VSS  |
|  **推挽输出**  | 数字输出 |  可输出引脚电平，高电平接VDD，低电平接VSS  |
| **复用开漏输出** | 数字输出 |  有片上外设控制，高电平为高阻态，低电平接VSS  |
| **复用推挽输出** | 数字输出 |  有片上外设控制，高电平接VDD，低电平接VSS  |
## 浮空/上拉/下拉输入
![[Pasted image 20250309215003.png]]
## 模拟输入
![[Pasted image 20250309215015.png]]
## 开漏/推挽输出
![[Pasted image 20250309215034.png]]
## 复用开漏/推挽输出
![[Pasted image 20250309215207.png]]


# GPIO的使用
***
GPIO函数使用概括性总结，操作步骤为：
1. 使能 IO口时钟。调用函数为 `RCC_APB2PeriphClockCmd()`。
2. 初始化 IO参数。调用函数 `GPIO_Init()`;
3. 操作 IO。操作 IO的方法就是下面我们讲解的方法。
***
## 库函数下GPIO配置
库函数定义及相关文件：**stm32f10x_gpio.h | stm32f10x_gpio.c**
### GPIO初始化函数
```c
void GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_InitStruct)
```
此函数第一个参数用来指定GPIO端口，第二个参数用来进行初始化。
第二个参数对应的结构体：
```c
typedef struct
{uint16_t GPIO_Pin; 
GPIOSpeed_TypeDef GPIO_Speed; 
GPIOMode_TypeDef GPIO_Mode; 
}GPIO_InitTypeDef;
```
通过初始化结构体初始化GPIO的常用格式是：
```c
GPIO_InitTypeDef GPIO_InitStructure; GPIO_InitStructure.GPIO_Pin = GPIO_Pin_5; //LED0---->PB.5 端口配置
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; //推挽输出
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;//速度 50MHz GPIO_Init(GPIOB, &GPIO_InitStructure);//根据设定参数配置 GPIO
```
关于`GPIO_InitStruct`结构体的参数解释：
- 第一个参数即为需要初始化的IO口
- 第二个成员变量 GPIO_Mode是用来设置对应 IO端口的输出输入模式,具体八个模式如下：
```c
typedef enum { GPIO_Mode_AIN = 0x0, //模拟输入
GPIO_Mode_IN_FLOATING = 0x04, //浮空输入
GPIO_Mode_IPD = 0x28, //下拉输入
GPIO_Mode_IPU = 0x48, //上拉输入
GPIO_Mode_Out_OD = 0x14, //开漏输出
GPIO_Mode_Out_PP = 0x10, //通用推挽输出
GPIO_Mode_AF_OD = 0x1C, //复用开漏输出
GPIO_Mode_AF_PP = 0x18 //复用推挽
}GPIOMode_TypeDef;
```
- 第三个参数是IO口速度设置，有三个可选值，在 MDK中同样是通过枚举类型定义：
```c
typedef enum 
{ GPIO_Speed_10MHz = 1, 
GPIO_Speed_2MHz, 
GPIO_Speed_50MHz 
}GPIOSpeed_TypeDef;
```
### GPIO端口输出/读取函数
详细讲解。在 STM32固件库中，通过 BSRR和 BRR寄存器设置 GPIO端口输出是通过函数`GPIO_SetBits()`和函数 `GPIO_ResetBits()`来完成的。
```c
void GPIO_SetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin); void GPIO_ResetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
```
### GPIO初始化整体代码
```C
GPIO_InitTypeDef GPIO_InitStructure; 
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA| RCC_APB2Periph_GPIOD, ENABLE); //使能 PA,PD端口时钟
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_8; //LED0---->PA.8 端口配置
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; //推挽输出
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; //IO口速度为 50MHz 
GPIO_Init(GPIOA, &GPIO_InitStructure); //初始化 GPIOA.8 
GPIO_SetBits(GPIOA,GPIO_Pin_8); //PA.8 输出高

GPIO_InitStructure.GPIO_Pin = GPIO_Pin_2; //LED1---->PD.2 端口配置 , 推挽输出
GPIO_Init(GPIOD, &GPIO_InitStructure); //推挽输出 IO口速度为 50MHz 
GPIO_SetBits(GPIOD,GPIO_Pin_2); //PD.2 输出高
```
***
## 寄存器下GPIO配置

***
## HAL库下GPIO配置



# 其余问题
## 当使用JTAG接口所占用的GPIO口
调用
```c
GPIO_PinRemapConfig(GPIO_Remap_SWJ_JTAGDisable, ENABLE);
```
这个函数，用于禁止 JTAG开启 SWD，使JTAG所占用的IO口释放，将其当作普通IO口使用。