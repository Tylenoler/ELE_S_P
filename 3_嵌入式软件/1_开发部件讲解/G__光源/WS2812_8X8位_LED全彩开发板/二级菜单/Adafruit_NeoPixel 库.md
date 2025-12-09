
# Adafruit_NeoPixel 库
下面是一个完整的示例，展示如何使用 Adafruit_NeoPixel 库控制 WS2812 LED 灯带实现各种动画效果：
```c
#include <Adafruit_NeoPixel.h>

// LED 配置
#define LED_PIN     6       // 数据线连接的Arduino引脚
#define LED_COUNT   64      // LED灯珠数量
#define BRIGHTNESS  20     // 亮度 (0-255)

// 创建灯带对象
Adafruit_NeoPixel strip(LED_COUNT, LED_PIN, NEO_GRB + NEO_KHZ800);

// 动画模式枚举
enum AnimationMode {
  SOLID_COLOR,
  RAINBOW,
  THEATER_CHASE,
  FIRE,
  PULSE,
  RUNNING_LIGHTS,
  BOUNCING_BALL,
  CYLON,
  WATER_RIPPLE,
  MODE_COUNT
};

// 当前动画模式
AnimationMode currentMode = SOLID_COLOR;
// 当前颜色
uint32_t currentColor = strip.Color(255, 0, 0); // 初始红色
// 帧率控制
unsigned long previousMillis = 0;
const long interval = 33; // 30 FPS (1000ms/30≈33ms)

void setup() {
  Serial.begin(115200);
  strip.begin();           // 初始化灯带
  strip.show();            // 初始关闭所有LED
  strip.setBrightness(BRIGHTNESS); // 设置亮度
  
  Serial.println("Adafruit NeoPixel 控制示例");
  Serial.println("通过串口发送命令:");
  Serial.println("1 - 静态颜色");
  Serial.println("2 - 彩虹效果");
  Serial.println("3 - 跑马灯效果");
  Serial.println("4 - 火焰效果");
  Serial.println("5 - 脉冲效果");
  Serial.println("6 - 流动灯光");
  Serial.println("7 - 弹跳球");
  Serial.println("8 - Cylon扫描效果");
  Serial.println("9 - 水波纹效果");
  Serial.println("c - 更改颜色 (格式: r,g,b)");
}

void loop() {
  // 检查串口输入
  if (Serial.available() > 0) {
    handleSerialInput();
  }

  // 帧率控制 - 每33ms更新一次 (约30FPS)
  unsigned long currentMillis = millis();
  if (currentMillis - previousMillis >= interval) {
    previousMillis = currentMillis;
    
    // 根据当前模式调用相应的动画函数
    switch(currentMode) {
      case SOLID_COLOR:
        solidColor();
        break;
      case RAINBOW:
        rainbow();
        break;
      case THEATER_CHASE:
        theaterChase();
        break;
      case FIRE:
        fireEffect();
        break;
      case PULSE:
        pulse();
        break;
      case RUNNING_LIGHTS:
        runningLights();
        break;
      case BOUNCING_BALL:
        bouncingBall();
        break;
      case CYLON:
        cylon();
        break;
      case WATER_RIPPLE:
        waterRipple();
        break;
    }
    
    // 更新LED显示
    strip.show();
  }
}

// 处理串口输入
void handleSerialInput() {
  char command = Serial.read();
  
  switch(command) {
    case '1': 
      currentMode = SOLID_COLOR;
      Serial.println("模式: 静态颜色");
      break;
    case '2': 
      currentMode = RAINBOW;
      Serial.println("模式: 彩虹效果");
      break;
    case '3': 
      currentMode = THEATER_CHASE;
      Serial.println("模式: 跑马灯效果");
      break;
    case '4': 
      currentMode = FIRE;
      Serial.println("模式: 火焰效果");
      break;
    case '5': 
      currentMode = PULSE;
      Serial.println("模式: 脉冲效果");
      break;
    case '6': 
      currentMode = RUNNING_LIGHTS;
      Serial.println("模式: 流动灯光");
      break;
    case '7': 
      currentMode = BOUNCING_BALL;
      Serial.println("模式: 弹跳球");
      break;
    case '8': 
      currentMode = CYLON;
      Serial.println("模式: Cylon扫描效果");
      break;
    case '9': 
      currentMode = WATER_RIPPLE;
      Serial.println("模式: 水波纹效果");
      break;
    case 'c':
      setColorFromSerial();
      break;
  }
}

// 从串口设置颜色
void setColorFromSerial() {
  Serial.println("输入RGB值 (格式: r,g,b):");
  
  while(Serial.available() == 0); // 等待输入
  String input = Serial.readStringUntil('\n');
  
  int r = getValue(input, ',', 0).toInt();
  int g = getValue(input, ',', 1).toInt();
  int b = getValue(input, ',', 2).toInt();
  
  currentColor = strip.Color(r, g, b);
  Serial.print("颜色设置为: R=");
  Serial.print(r);
  Serial.print(" G=");
  Serial.print(g);
  Serial.print(" B=");
  Serial.println(b);
}

// 辅助函数：分割字符串
String getValue(String data, char separator, int index) {
  int found = 0;
  int strIndex[] = {0, -1};
  int maxIndex = data.length()-1;

  for(int i=0; i<=maxIndex && found<=index; i++){
    if(data.charAt(i)==separator || i==maxIndex){
        found++;
        strIndex[0] = strIndex[1]+1;
        strIndex[1] = (i == maxIndex) ? i+1 : i;
    }
  }
  return found>index ? data.substring(strIndex[0], strIndex[1]) : "";
}

// ===== 动画效果函数 =====

// 1. 静态颜色
void solidColor() {
  for(int i=0; i<strip.numPixels(); i++) {
    strip.setPixelColor(i, currentColor);
  }
}

// 2. 彩虹效果
void rainbow() {
  static uint16_t j = 0;
  for(int i=0; i<strip.numPixels(); i++) {
    strip.setPixelColor(i, Wheel((i+j) & 255));
  }
  j = (j + 1) % 256;
}

// 3. 跑马灯效果
void theaterChase() {
  static int j = 0;
  static int q = 0;
  
  for (int i=0; i < strip.numPixels(); i=i+3) {
    strip.setPixelColor(i+q, currentColor);  // 每三个灯亮一个
  }
  
  for (int i=0; i < strip.numPixels(); i++) {
    if ((i-q) % 3 != 0) strip.setPixelColor(i, 0); // 关闭其他灯
  }
  
  q = (q + 1) % 3;
}

// 4. 火焰效果
void fireEffect() {
  static int cooling = 55;
  static int sparking = 120;
  static byte heat[LED_COUNT];
  
  // 步骤1: 冷却每个单元
  for (int i = 0; i < LED_COUNT; i++) {
    heat[i] = max(heat[i] - random(0, ((cooling * 10) / LED_COUNT) + 2), 0);
  }
  
  // 步骤2: 从底部向上传播热量
  for (int k = LED_COUNT - 1; k >= 2; k--) {
    heat[k] = (heat[k - 1] + heat[k - 2] + heat[k - 2]) / 3;
  }
  
  // 步骤3: 随机添加新火花
  if (random(255) < sparking) {
    int y = random(7);
    heat[y] = min(heat[y] + random(160, 255), 255);
  }
  
  // 步骤4: 将热量转换为颜色
  for (int j = 0; j < LED_COUNT; j++) {
    setPixelHeatColor(j, heat[j]);
  }
}

void setPixelHeatColor(int Pixel, byte temperature) {
  // 根据温度计算颜色
  byte t192 = round((temperature/255.0)*191);
  
  byte heatramp = t192 & 0x3F; // 0..63
  heatramp <<= 2; // 缩放到0..252
  
  if (t192 > 0x80) {                     // 最热
    strip.setPixelColor(Pixel, 255, 255, heatramp);
  } else if (t192 > 0x40) {              // 中等
    strip.setPixelColor(Pixel, 255, heatramp, 0);
  } else {                               // 较冷
    strip.setPixelColor(Pixel, heatramp, 0, 0);
  }
}

// 5. 脉冲效果
void pulse() {
  static uint8_t brightness = 0;
  static int8_t direction = 1;
  
  brightness += direction;
  if (brightness <= 0 || brightness >= 255) {
    direction = -direction;
  }
  
  strip.setBrightness(brightness);
  solidColor();
}

// 6. 流动灯光
void runningLights() {
  static int position = 0;
  
  for(int i=0; i<strip.numPixels(); i++) {
    // 创建移动的灯光效果
    int r = ((sin(i+position) * 127 + 128) / 255.0) * (currentColor >> 16 & 0xFF);
    int g = ((sin(i+position) * 127 + 128) / 255.0) * (currentColor >> 8 & 0xFF);
    int b = ((sin(i+position) * 127 + 128) / 255.0) * (currentColor & 0xFF);
    strip.setPixelColor(i, r, g, b);
  }
  
  position = (position + 1) % 256;
}

// 7. 弹跳球
void bouncingBall() {
  static float ballHeight[LED_COUNT] = {0};
  static float ballVelocity[LED_COUNT] = {0};
  static float gravity = -0.1;
  
  // 初始化
  static bool initialized = false;
  if (!initialized) {
    for (int i = 0; i < LED_COUNT; i++) {
      ballHeight[i] = 0;
      ballVelocity[i] = 0;
    }
    ballHeight[0] = 1.0; // 在位置0开始
    initialized = true;
  }
  
  // 清除LED
  for (int i = 0; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 0);
  }
  
  // 更新物理
  for (int i = 0; i < LED_COUNT; i++) {
    if (ballHeight[i] > 0) {
      ballVelocity[i] += gravity;
      ballHeight[i] += ballVelocity[i];
      
      // 弹跳
      if (ballHeight[i] < 0) {
        ballHeight[i] = 0;
        ballVelocity[i] = -ballVelocity[i] * 0.8; // 反弹损失能量
      }
      
      // 绘制球
      if (ballHeight[i] > 0) {
        int brightness = min(255, (int)(ballHeight[i] * 255));
        strip.setPixelColor(i, strip.Color(
          brightness * (currentColor >> 16 & 0xFF) / 255,
          brightness * (currentColor >> 8 & 0xFF) / 255,
          brightness * (currentColor & 0xFF) / 255
        ));
      }
      
      // 传递到下一个LED
      if (i < LED_COUNT - 1 && random(100) > 95) {
        ballHeight[i+1] = ballHeight[i] * 0.8;
        ballVelocity[i+1] = ballVelocity[i];
        ballHeight[i] = 0;
      }
    }
  }
}

// 8. Cylon扫描效果
void cylon() {
  static int position = 0;
  static int direction = 1;
  
  // 清除LED
  for (int i = 0; i < strip.numPixels(); i++) {
    strip.setPixelColor(i, 0);
  }
  
  // 设置扫描位置
  strip.setPixelColor(position, currentColor);
  
  // 添加拖尾效果
  for (int i = 1; i < 5; i++) {
    if (position - i >= 0) {
      int brightness = 100 / i;
      strip.setPixelColor(position - i, strip.Color(
        brightness * (currentColor >> 16 & 0xFF) / 255,
        brightness * (currentColor >> 8 & 0xFF) / 255,
        brightness * (currentColor & 0xFF) / 255
      ));
    }
    if (position + i < strip.numPixels()) {
      int brightness = 100 / i;
      strip.setPixelColor(position + i, strip.Color(
        brightness * (currentColor >> 16 & 0xFF) / 255,
        brightness * (currentColor >> 8 & 0xFF) / 255,
        brightness * (currentColor & 0xFF) / 255
      ));
    }
  }
  
  // 更新位置
  position += direction;
  if (position <= 0 || position >= strip.numPixels() - 1) {
    direction = -direction;
  }
}

// 9. 水波纹效果
void waterRipple() {
  static float ripple[LED_COUNT] = {0};
  static float damping = 0.95;
  
  // 随机添加涟漪
  if (random(100) > 95) {
    int pos = random(LED_COUNT);
    ripple[pos] = 1.0;
  }
  
  // 更新涟漪
  for (int i = 0; i < LED_COUNT; i++) {
    // 传播涟漪
    if (i > 0) ripple[i-1] += ripple[i] * 0.5;
    if (i < LED_COUNT - 1) ripple[i+1] += ripple[i] * 0.5;
    
    // 应用阻尼
    ripple[i] *= damping;
    if (ripple[i] < 0.01) ripple[i] = 0;
    
    // 设置LED颜色
    int brightness = min(255, (int)(ripple[i] * 255));
    strip.setPixelColor(i, strip.Color(
      brightness * (currentColor >> 16 & 0xFF) / 255,
      brightness * (currentColor >> 8 & 0xFF) / 255,
      brightness * (currentColor & 0xFF) / 255
    ));
  }
}

// 辅助函数：生成彩虹颜色
uint32_t Wheel(byte WheelPos) {
  WheelPos = 255 - WheelPos;
  if(WheelPos < 85) {
    return strip.Color(255 - WheelPos * 3, 0, WheelPos * 3);
  }
  if(WheelPos < 170) {
    WheelPos -= 85;
    return strip.Color(0, WheelPos * 3, 255 - WheelPos * 3);
  }
  WheelPos -= 170;
  return strip.Color(WheelPos * 3, 255 - WheelPos * 3, 0);
}
```