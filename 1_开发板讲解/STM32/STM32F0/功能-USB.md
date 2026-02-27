![[Pasted image 20260101120111.png]]

![[Pasted image 20260101121104.png]]
## 一、USB配置部分

### 1. **USB设备类选择** (Class For FSIP)
这是选择USB设备工作模式的选项：
- **Communication Device Class (Virtual Port Com)** - **虚拟串口**（常用）
  - 设备通过USB模拟成串口（COM口），电脑识别为虚拟串行端口
  - 用于调试、数据通信等场景
- **Audio Device Class** - 音频设备类（如USB声卡）
- **Download Firmware Update Class (DFU)** - 固件更新类
  - 通过USB更新芯片固件
- **Human Interface Device Class (HID)** - 人机接口设备类
  - 键盘、鼠标、游戏手柄等
- **Mass Storage Class** - 大容量存储类
  - U盘模式

### 2. **基本参数设置**
- **USBD_MAX_NUM_INTERFACES** - USB接口最大数量（设为1）
- **USBD_MAX_NUM_CONFIGURATION** - USB配置最大数量（设为1）
- **USBD_MAX_STR_DESC_SZ** - 字符串描述符最大大小（512字节）
  - 用于存储设备名称、厂商信息等
- **USBD_SELF_POWERED** - 自供电使能
  - 设备是否自带电源（与总线供电相对）
- **USBD_DEBUG_LEVEL** - USB调试级别
  - 用于调试时输出更多信息

### 3. **CDC类特定参数**（虚拟串口）
- **USB CDC Rx Buffer Size** - 接收缓冲区大小
- **USB CDC Tx Buffer Size** - 发送缓冲区大小
  - 这两个参数决定了虚拟串口的数据缓存能力

## 二、引脚配置部分

### 1. **特殊功能引脚**
- **USB_DP/USB_DM** - **USB差分数据线**
  - DP = Data Positive（数据正）
  - DM = Data Minus（数据负）
  - **这是USB通信必需的两个引脚**
![[Pasted image 20260101121017.png]]

![[Pasted image 20260101121033.png]]

