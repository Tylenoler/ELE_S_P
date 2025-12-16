

# 简介
- DMA（Direct Memory Access）直接存储器存取
- DMA可以提供外设和存储器或者存储器和存储器之间的高速数据传输，无须CPU干预，节省了CPU的资源
- 12个独立可配置的通道： DMA1（7个通道）， DMA2（5个通道）
- 每个通道都支持软件触发和特定的硬件触发
# 存储器映像
![[Pasted image 20250313204903.png]]
# DMA框图
![[Pasted image 20250313204919.png]]
# DMA基本结构
![[Pasted image 20250313204954.png]]
# DMA请求
![[Pasted image 20250313205018.png]]
# 数据宽度与对齐
![[Pasted image 20250313205112.png]]
# 数据转运+DMA
![[Pasted image 20250313205151.png]]
# ADC扫描模式+DMA
![[Pasted image 20250313205231.png]]

# DMA
我们把 **DMA（Direct Memory Access，直接内存访问）** 当成 STM32 里一个“极度务实、ROI 极高的基础设施组件”来看，它的存在目标只有一个：**把 CPU 从搬数据这类低价值体力活中解放出来**，让系统整体吞吐和实时性上一个台阶。

---

先给一句**管理层总结版结论**：

> **DMA = 外设 ↔ 内存 的高速直通车，绕开 CPU，降低中断负载，提升实时性与功耗效率，是嵌入式系统规模化和工程化的关键能力。**

下面我们拆解它的**原理、能力、配置模型、使用场景、常见坑**，把这件事讲透。

---

## 一、如果没有 DMA，STM32 是怎么“搬数据”的？

以 UART 接收为例（这是你后面一定会大量用到的）：

1. UART 收到 1 个字节
    
2. 触发 **中断**
    
3. CPU 进中断函数
    
4. 从 UART_DR 读数据
    
5. 存到 RAM 缓冲区
    
6. 退出中断
    

问题在哪？

- **每个字节一次中断**（115200bps ≈ 每秒 11,520 次）
    
- CPU 时间被大量碎片化
    
- 中断嵌套风险增加
    
- 功耗上升
    
- 实时控制抖动
    

这在“玩具程序”里还能忍，一旦系统复杂（多串口、CAN、SPI、传感器、控制环），就会直接失控。

---

## 二、DMA 的核心思想：让硬件替你搬数据

DMA 的工作方式是：

> **外设产生数据 → DMA 自动搬到内存 → CPU 只在“合适的时刻”被通知**

CPU 不再参与“每个字节”的搬运，只负责：

- 启动 DMA
    
- 在需要时处理“已经搬好的一批数据”
    

这就是**工程级思维**：  
👉 **把确定性、重复性的事情交给硬件**

---

## 三、STM32 中 DMA 的本质结构

从架构角度看，DMA 是一个**独立于 CPU 的硬件模块**，核心组成是：

- **DMA Controller（控制器）**
    
- **DMA Channel / Stream（通道）**
    
- **请求源（UART / SPI / ADC / TIM 等）**
    
- **目标地址（RAM / 外设寄存器）**
    

你可以把它理解为：

> “一个被外设触发的、自动执行 memcpy 的硬件状态机”

---

## 四、DMA 能干哪些事？（能力矩阵）

在 STM32 里，DMA 通常用于：

### 1️⃣ 外设 → 内存（最常见）

- UART RX
    
- SPI RX
    
- ADC 采样
    
- I2C RX
    

### 2️⃣ 内存 → 外设

- UART TX
    
- SPI TX
    
- DAC 输出
    
- PWM 波形数据
    

### 3️⃣ 内存 → 内存（部分型号支持）

- 数据搬运
    
- 数据格式转换（高级 DMA）
    

---

## 五、DMA 的关键配置参数（这是理解的核心）

### 1️⃣ 方向（Direction）

- Peripheral → Memory   (外围到内存)
    
- Memory → Peripheral  (内存到外围)
    
- Memory → Memory  （内存到内存）
    

方向一旦错，DMA 不会报错，只会“悄悄不工作”。

---

### 2️⃣ 数据宽度（Data Width）

- 8 bit（byte）
    
- 16 bit（half-word）
    
- 32 bit（word）
    

⚠️ **必须和外设寄存器真实宽度一致**

比如：

- UART_DR：8 bit
    
- ADC_DR：12 bit（通常用 16 bit 接）
    

---

### 3️⃣ 地址是否自增（Increment）

- 外设地址：通常 **不自增**
    
- 内存地址：通常 **自增**
    

这是 DMA 能“连续写数组”的根本原因。

---

### 4️⃣ 传输长度（NDTR）

DMA 不知道“你想要多少数据”，你必须明确告诉它：

```c
DMA_CNDTR = 64;   // 搬 64 次
```

一次 = 一个数据宽度单位  
不是字节数量，这是新手非常容易踩的坑。

---

### 5️⃣ 模式（Mode）

这是 **STM32 DMA 的灵魂设计**：

- **Normal 模式**
    
    - 搬完 N 次就停
        
    - 适合“明确长度”的任务
    - 适合发送模式
        
- **Circular（循环）模式**
    
    - 搬到末尾自动回到起点
        
    - 永不停止
        
    - 非常适合：UART RX、ADC 连续采样
    - 适合接收任务
        

👉 **长期运行系统，80% 用 Circular**

---

## 六、中断 + DMA：真正的“黄金组合”

DMA 不是“没中断”，而是：

> **用“少量、关键中断”替代“海量字节中断”**

常见 DMA 中断类型：

- Half Transfer（半缓冲）
    
- Transfer Complete（全缓冲）
    
- Transfer Error
    

### 实战常见结构（强烈推荐）

```text
DMA 循环模式
┌──────────────┐
│ Buffer[0~31] │ ← 半传输中断
├──────────────┤
│ Buffer[32~63]│ ← 全传输中断
└──────────────┘
```

这样你可以：

- 一边 DMA 在写
    
- 一边 CPU 在处理另一半
    
- **零丢包 + 高实时性**
    

这是**工业级串口驱动的标准套路**。

---
# 常用函数介绍

下面不是“函数大全”，而是**你在 STM32 实际项目里 90% 会用到的 DMA 关键函数 + 正确使用姿势**。目标很明确：**让你能写出稳定、可复用、可扩展的 DMA 驱动**。

我默认你现在用的是 **STM32 HAL 库**（这是企业项目和个人项目里性价比最高的选择）。

---

## 一句话总览（给你一个心智模型）

在 HAL 体系里，**DMA 从来不是单独用的**，它一定是：

> **DMA + 外设（UART / ADC / SPI）+ 回调函数 + 状态机**

你真正要掌握的是这条链路，而不是“某一个函数”。

---

## 一、DMA 初始化相关（只做一次，属于“资产配置”）

### 1️⃣ `MX_DMA_Init()`

**作用**：  
开启 DMA 控制器时钟、配置中断优先级。

**典型位置**（以实际CUBEMX生成为准）：

```c
void MX_DMA_Init(void)
{
    __HAL_RCC_DMA1_CLK_ENABLE();

    HAL_NVIC_SetPriority(DMA1_Channel5_IRQn, 5, 0);
    HAL_NVIC_EnableIRQ(DMA1_Channel5_IRQn);
}
```

**你该怎么用**：

- 不要手写，**CubeMX 生成即可**
    
- 优先级要低于实时控制中断，高于普通业务
    
- 这是**系统级初始化**，不用反复碰
    

📌 企业经验：

> DMA 中断优先级 = “后台服务级别”，不要抢控制环的 CPU。

#### 用 CubeMX 正确配置（这是成败关键）

在 CubeMX 里：

- UARTx → Mode：
    
    - ✅ Asynchronous
        
- DMA Settings：
    
    - 添加 **USARTx_RX**
        
    - Direction：Peripheral → Memory
        
    - Mode：
        
        - 普通接收：Normal
            
        - 连续接收：Circular（常用）
            
    - Data Width：Byte
        
- NVIC：
    
    - 打开 UART 中断
        
    - 打开 DMA RX 中断
        

**一句工程经验：**

> UART + DMA 不开中断 = 你永远不知道它什么时候收完


---

## 二、DMA 启动函数（真正的“执行入口”）

### 2️⃣ `HAL_UART_Receive_DMA()`

**作用**：  
启动 UART → DMA → 内存 的接收链路。

```c
HAL_UART_Receive_DMA(&huart1, rx_buf, RX_LEN);
```

**你要知道的三件关键事实**：

1. 这是 **一次性配置 + 启动**
    
2. 对应的 DMA 会自动进入 RUN 状态
    
3. **Circular 模式下只需要调一次**
    

📌 正确姿势（强烈推荐）：

```c
// 初始化阶段
HAL_UART_Receive_DMA(&huart1, uart_rx_buf, UART_RX_BUF_SIZE);
__HAL_UART_ENABLE_IT(&huart1, UART_IT_IDLE);
```

这是一套“长期运行”的接收体系，而不是临时测试代码。

例如：
```c
HAL_UART_Receive_DMA(&huart1, uart_rx_buf, 64);
```
这行代码等价于一句非常重要的“人话”：

> “以后 USART1 收到的 64 个字节，  
> 全部由 DMA 自动写进 uart_rx_buf”

你注意到了吗？

- **你没有 while**  
    
- **你没有 memcpy**  
    
- **你没有轮询寄存器**  
    

DMA 已经接管了这件事。



---

### 3️⃣ `HAL_UART_Transmit_DMA()`

**作用**：  
通过 DMA 发送数据，CPU 不阻塞。

```c
HAL_UART_Transmit_DMA(&huart1, tx_buf, len);
```

**注意点（非常重要）**：

- DMA 发送是 **异步的**
    
- 函数返回 ≠ 发送完成
    
- 发送期间不要修改 `tx_buf`
    

📌 工程建议：

```c
if (uart_tx_busy == 0)
{
    uart_tx_busy = 1;
    HAL_UART_Transmit_DMA(&huart1, buf, len);
}
```

👉 **一定要有 busy 标志或队列**，这是成熟系统的基本素养。

---

## 三、DMA 状态与控制函数（风险管理）

### 4️⃣ `HAL_DMA_Abort()`

**作用**：  
强制停止 DMA 传输。

```c
HAL_DMA_Abort(huart1.hdmarx);
```

**什么时候用？**

- 协议异常
    
- 通信复位
    
- 模块热重启
    
- 外设状态不可控时的“止损操作”
    

📌 心智模型：

> Abort 是“应急刹车”，不是日常流程。

---

### 5️⃣ `__HAL_DMA_GET_COUNTER()`

**作用**：  
读取 DMA **还剩多少次没搬完**。

```c
uint16_t remain = __HAL_DMA_GET_COUNTER(huart1.hdmarx);
```

**这是高手函数**，用途极大。

📌 典型用途：  
UART + DMA + IDLE 中断里，计算已接收数据长度：

```c
recv_len = RX_BUF_SIZE - __HAL_DMA_GET_COUNTER(huart->hdmarx);
```

这是**无拷贝、高性能协议栈**的基础。

---

## 四、DMA 回调函数（真正干活的地方）

HAL 把 DMA 的“事件”通过回调抛给你。

### 6️⃣ `HAL_UART_RxCpltCallback()`

**触发条件**：  
DMA 完成一次完整缓冲区接收（Normal 模式 / Circular 的整圈）

```c
void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart)
{
    if (huart->Instance == USART1)
    {
        // 处理一整包数据
    }
}
```

>📌 注意：  在 **Circular 模式** 下，它会不断触发。

📌 这时候：

- 数据已经在你指定的内存
    
- DMA 已经停了（Normal 模式,因为在Circular模式下会一直触发）

另外讲一嘴，如果是使用RS485通讯但是并没有开启STM32CubeIDE里面的硬件流控制的话，则需要在此接收回调函数中将RS485通信的控制引脚进行反转，以方便数据可以传出去。
详见RS485通讯

如果你想继续收：

```c
HAL_UART_Receive_DMA(&huart1, uart_rx_buf, 64);
```

---

### 7️⃣ `HAL_UART_RxHalfCpltCallback()`

**触发条件**：  
DMA 写到缓冲区一半。

```c
void HAL_UART_RxHalfCpltCallback(UART_HandleTypeDef *huart)
{
    // 处理前半缓冲
}
```

📌 这是**实时流式处理**的关键能力，很多人不用，但一用就回不去。

---

### 8️⃣ `HAL_UART_TxCpltCallback()`

**作用**：  
DMA 发送完成通知。

```c
void HAL_UART_TxCpltCallback(UART_HandleTypeDef *huart)
{
    uart_tx_busy = 0;
}
```

📌 企业级约定：

> 所有 DMA TX 必须在这个回调里“释放资源”。

---

## 五、UART + DMA 的“黄金组合”：IDLE 中断

### 9️⃣ `UART_IT_IDLE` + `HAL_UART_IRQHandler()`

**IDLE 是什么？**  
串口线路空闲一个字符时间，STM32 自动触发。

📌 这是 **不定长协议的神器**。

典型用法：

```c
void USART1_IRQHandler(void)
{
    if (__HAL_UART_GET_FLAG(&huart1, UART_FLAG_IDLE))
    {
        __HAL_UART_CLEAR_IDLEFLAG(&huart1);

        uint16_t len = RX_BUF_SIZE - __HAL_DMA_GET_COUNTER(huart1.hdmarx);

        // 把 len 字节的数据交给协议层
    }
    HAL_UART_IRQHandler(&huart1);
}
```

这套结构 =  
**工业串口通信的标准答案**

---

## 六、你该如何“正确使用 DMA”？给你一套方法论

### 1️⃣ 不要在业务代码里“频繁启停 DMA”

- RX：一次启动，长期运行
    
- TX：状态机 / 队列化
    

### 2️⃣ DMA 只负责“搬数据”

- 不做协议判断
    
- 不做解析
    
- 不关心业务语义
    

### 3️⃣ 协议层只和“缓冲区 + 长度”打交道

- DMA 是底层基础设施
    

---
## 七、现在最容易犯的 3 个错误（提前帮你避坑）

### ❌ 错误 1：以为 DMA 是“接收后再搬”

DMA 是 **直接写内存**，不是后处理工具。

---

### ❌ 错误 2：在 main 里等数据

```c
while(!rx_done);
```

这是**性能和架构双杀**，DMA 的意义直接被你抹掉。

---

### ❌ 错误 3：回调里做太重的事

回调只做：

- 拷贝指针
    
- 设置标志
    
- 投递消息
    

复杂逻辑放主循环或任务

## 八、抽象成一个“可复用认知模板”

以后你看到任何：

> **外设 + DMA**

你脑子里自动出现这四句话：

1. **目标内存是谁？**
    
2. **DMA 什么时候启动？**
    
3. **完成事件在哪通知我？**
    
4. **我是否需要连续接收？**
    

你已经开始从“写 HAL 代码”，  
走向“设计数据通路”，  
这是**嵌入式工程能力的分水岭**。
