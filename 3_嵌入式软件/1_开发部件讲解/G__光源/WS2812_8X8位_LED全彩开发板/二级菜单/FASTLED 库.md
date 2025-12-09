
使用已正常，将LED Stdio所生成的32BIT数据经由python文件进行转换为24bit数据并复制到此文件对应位置即可
```c
#include <FastLED.h>

// ===== 可配置参数 =====
#define LED_PIN     6     // LED数据引脚
#define NUM_LEDS    16    // LED数量（根据你的灯带修改）
#define LED_TYPE    WS2812B // LED型号
#define COLOR_ORDER GRB   // 颜色顺序
#define BRIGHTNESS  20   // 初始亮度(0-255)

CRGB leds[NUM_LEDS];      // LED数组

// ===== 帧动画系统 =====
// 定义帧结构（存储在PROGMEM中以节省内存）
struct AnimationFrame {
  const uint32_t* data;   // 帧数据指针
  uint16_t length;        // 帧数据长度
  uint16_t delayMs;       // 帧显示时间(毫秒)
};

// === 帧数据定义 - 保持你原始的名称 ===
const uint32_t ledarray0[] PROGMEM = {
0xFFFFFF, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
};

const uint32_t ledarray1[] PROGMEM = {
0xFFFFFF, 0xFFFFFF, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
};

const uint32_t ledarray2[] PROGMEM = {
0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
};

const uint32_t ledarray3[] PROGMEM = {
0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
};

const uint32_t ledarray4[] PROGMEM = {
0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
};

const uint32_t ledarray5[] PROGMEM = {
0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
};

const uint32_t ledarray6[] PROGMEM = {
0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
};

const uint32_t ledarray7[] PROGMEM = {
0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 0xFFFFFF, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 
};

// 帧序列（在这里添加所有帧）
AnimationFrame frames[] = {
  { ledarray0, sizeof(ledarray0)/sizeof(uint32_t), 500 },  // 第0帧，显示500ms
  { ledarray1, sizeof(ledarray1)/sizeof(uint32_t), 500 },  // 第1帧
  { ledarray2, sizeof(ledarray2)/sizeof(uint32_t), 500 },   // 第2帧
  { ledarray3, sizeof(ledarray2)/sizeof(uint32_t), 500 },   // 第2帧
  { ledarray4, sizeof(ledarray2)/sizeof(uint32_t), 500 },   // 第2帧
  { ledarray5, sizeof(ledarray2)/sizeof(uint32_t), 500 },   // 第2帧
  { ledarray6, sizeof(ledarray2)/sizeof(uint32_t), 500 },   // 第2帧
  { ledarray7, sizeof(ledarray2)/sizeof(uint32_t), 500 },   // 第2帧
  // 添加更多帧...
};

const uint8_t FRAME_COUNT = sizeof(frames)/sizeof(AnimationFrame); // 总帧数

// 动画模式枚举
enum AnimationMode {
  SOLID_COLOR,            // 固定颜色
  RAINBOW,                // 彩虹效果
  FIRE,                   // 火焰效果
  PULSE,                  // 呼吸灯
  RUNNING_LIGHTS,         // 跑马灯
  CONFETTI,               // 彩色斑点
  FRAME_ANIMATION,        // 帧动画模式
  CUSTOM_EFFECT_1         // 自定义效果
};

AnimationMode currentMode = FRAME_ANIMATION; // 默认模式
CRGB solidColor = CRGB::Blue;        // 单色模式的默认颜色

// 帧动画变量
uint8_t currentFrame = 0;            // 当前帧索引
unsigned long lastFrameTime = 0;     // 上次帧切换时间

// ===== 辅助函数 =====
void setAll(CRGB color) {
  fill_solid(leds, NUM_LEDS, color);
}

// 从PROGMEM加载帧数据到LED
void loadFrame(uint8_t frameIndex) {
  if (frameIndex >= FRAME_COUNT) return;
  
  AnimationFrame frame = frames[frameIndex];
  
  // 确保帧数据长度不超过LED数量
  uint16_t ledCount = min(frame.length, (uint16_t)NUM_LEDS);
  
  for (uint16_t i = 0; i < ledCount; i++) {
    // 从PROGMEM读取32位颜色值
    uint32_t colorData = pgm_read_dword(&(frame.data[i]));
    
    // 将32位颜色值转换为CRGB
    leds[i] = CRGB(
      (colorData >> 16) & 0xFF,  // 提取红色分量
      (colorData >> 8)  & 0xFF,  // 提取绿色分量
      colorData        & 0xFF   // 提取蓝色分量
    );
  }
  
  // 如果帧数据少于LED数量，剩余LED设为黑色
  for (uint16_t i = ledCount; i < NUM_LEDS; i++) {
    leds[i] = CRGB::Black;
  }
}

// 帧动画函数
void frameAnimation() {
  if (millis() - lastFrameTime >= frames[currentFrame].delayMs) {
    // 切换到下一帧
    currentFrame = (currentFrame + 1) % FRAME_COUNT;
    loadFrame(currentFrame);
    lastFrameTime = millis();
    FastLED.show();
  }
}

// ===== 主程序 =====
void setup() {
  // 串口初始化
  Serial.begin(115200);
  Serial.println("FastLED 帧动画模板已启动");
  
  // LED初始化
  FastLED.addLeds<LED_TYPE, LED_PIN, COLOR_ORDER>(leds, NUM_LEDS);
  FastLED.setBrightness(BRIGHTNESS);
  
  // 初始化帧动画
  loadFrame(0);
  FastLED.show();
  lastFrameTime = millis();
  
  Serial.println("可用命令:");
  Serial.println("0: 单色模式");
  Serial.println("1: 彩虹");
  Serial.println("2: 火焰");
  Serial.println("3: 呼吸灯");
  Serial.println("4: 跑马灯");
  Serial.println("5: 彩色斑点");
  Serial.println("6: 帧动画");
  Serial.println("b [亮度]: 设置亮度(0-255)");
  Serial.println("c [R] [G] [B]: 设置单色模式颜色");
  Serial.println("f [帧号]: 跳转到指定帧");
}

void loop() {
  // 处理串口命令
  if (Serial.available()) {
    char command = Serial.read();
    handleCommand(command);
  }
  
  // 执行当前动画
  switch(currentMode) {
    case SOLID_COLOR:
      setAll(solidColor);
      FastLED.show();
      break;
    case FRAME_ANIMATION:
      frameAnimation();
      break;
    // 其他模式可以根据需要启用
    default:
      // 其他动画模式
      break;
  }
}

// 处理串口命令
void handleCommand(char cmd) {
  switch(cmd) {
    case '0':
      currentMode = SOLID_COLOR;
      Serial.println("模式: 单色");
      break;
    case '6':
      currentMode = FRAME_ANIMATION;
      Serial.println("模式: 帧动画");
      // 重置到第一帧
      currentFrame = 0;
      loadFrame(currentFrame);
      FastLED.show();
      lastFrameTime = millis();
      break;
    case 'b': // 设置亮度
      while(!Serial.available()); // 等待数据
      int brightness = Serial.parseInt();
      FastLED.setBrightness(constrain(brightness, 0, 255));
      Serial.print("亮度设置为: ");
      Serial.println(brightness);
      FastLED.show(); // 立即更新亮度
      break;
    case 'c': // 设置颜色
      while(Serial.available() < 3); // 等待RGB值
      int r = Serial.parseInt();
      int g = Serial.parseInt();
      int b = Serial.parseInt();
      solidColor = CRGB(r, g, b);
      Serial.print("颜色设置为: R:");
      Serial.print(r);
      Serial.print(" G:");
      Serial.print(g);
      Serial.print(" B:");
      Serial.println(b);
      break;
    case 'f': // 跳转帧
      while(!Serial.available());
      int frame = Serial.parseInt();
      if (frame >= 0 && frame < FRAME_COUNT) {
        currentFrame = frame;
        loadFrame(currentFrame);
        FastLED.show();
        lastFrameTime = millis();
        Serial.print("跳转到帧: ");
        Serial.println(frame);
      } else {
        Serial.print("无效帧号, 有效范围: 0-");
        Serial.println(FRAME_COUNT - 1);
      }
      break;
    default:
      Serial.println("未知命令");
      break;
  }
}
```