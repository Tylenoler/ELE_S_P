```C
typedef struct

{

uint32_t last_tick;

uint32_t interval_ms;

uint8_t state;

} LedTask_t;

  

LedTask_t led1_run = {

.last_tick = 0,

.interval_ms = 50, // 50ms 闪烁

.state = 0

};

  

LedTask_t led2_run = {

.last_tick = 0,

.interval_ms = 50, // 20ms 闪烁

.state = 0

};

  

void LED1_Run_Process(void)

{

if (HAL_GetTick() - led1_run.last_tick >= led1_run.interval_ms)

{

led1_run.last_tick = HAL_GetTick();

led1_run.state ^= 1;

  

if (led1_run.state)

LED1_On();

else

LED1_Off();

}

}

void LED2_Run_Process(void)

{

if (HAL_GetTick() - led2_run.last_tick >= led2_run.interval_ms)

{

led2_run.last_tick = HAL_GetTick();

led2_run.state ^= 1;

  

if (led2_run.state)

LED2_On();

else

LED2_Off();

}

}
```
此可以实现LED不占用Delay时间