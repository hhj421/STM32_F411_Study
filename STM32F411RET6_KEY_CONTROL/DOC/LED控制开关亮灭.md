
下面这版是适合直接放 GitHub 做 README 的完整版，结构清晰、步骤完整、无表格、包含实验准备、从 CubeMX 到代码、问题总结全部一次性写好，你复制粘贴就能用。
 
 
 
STM32F411RET6 按键控制 LED 亮灭实验
 
基于 STM32CubeMX + HAL 库实现按键控制 LED 翻转，适用于 Nucleo-F411RE 开发板。
 
一、实验目的
 
通过配置 GPIO 输入输出，实现板载 USER 按键控制板载 LED 亮灭翻转，掌握 STM32 基础工程搭建、引脚配置与简单按键逻辑编写。
 
二、实验环境与准备
 
硬件准备
 
- STM32F411RET6 核心板 / Nucleo-F411RE 开发板
- USB 数据线（用于供电与程序下载）
- 电脑一台
 ![[cd61372d6fc33abe34e719f963c041b7_720.jpg]]
软件准备
 
- STM32CubeMX（用于引脚配置与工程生成）
- Keil MDK-ARM 或 STM32CubeIDE（用于编译、下载、调试）
- ST-Link 驱动（确保电脑能识别开发板）
 ![[60250de0e775015abe095bbd3db6bcca.png]]![[96b5b077c505da649158a220cb269676.png]]
三、硬件资源说明
 
- 板载绿色 LED（LD2）连接在 PA5 引脚
- 板载 USER 按键连接在 PC13 引脚
- 按键电路：一端接地，另一端接 PC13，因此配置为上拉输入
 
四、STM32CubeMX 详细配置步骤
 
1. 打开 STM32CubeMX，新建工程，搜索并选择  STM32F411RET6 。![[cdd0f00ce7e3e8923c12806b9d273299.png]]
2. 配置 PA5 为  GPIO_Output ，作为 LED 控制引脚。
3. 配置 PC13 为  GPIO_Input ，模式设置为上拉  Pull-up 。![[303aa76c17da6c8792bfb0649774a769.png]]![[2e75c809c661ef30e47e3f8ac4806180.png]]
4. 进入 Clock Configuration，将系统时钟配置为 84MHz。![[a24acce63b38819c04d150b12d61ff30.png]]
5. 进入 Project Manager，设置项目名称、保存路径，选择 IDE 为 MDK-ARM（Keil5）。![[3ebef9dd42153015ec432572692d5700.png]]
6. 点击 GENERATE CODE 生成工程，并打开工程。
 
五、代码实现（暂时由ai生成，本人还不会2026年3月23日16:18:06）
 
在  main.c  的  while(1)  循环中添加如下逻辑：
 while (1)
  {
    // ?? PC13 ??(????,???????)
    if (HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13) == GPIO_PIN_SET)
    {
      HAL_Delay(20);  // ??
      
      if (HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13) == GPIO_PIN_SET)
      {
        led_state = !led_state;        // ?? LED ??
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, led_state);  // ?? PA5 LED
        
        while (HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13) == GPIO_PIN_SET);  // ????
      }
    }
  }
 
六、编译与下载
 
1. 在 Keil5 中点击编译，确保无错误。
2. 连接开发板到电脑，选择下载器为 ST-Link。
3. 下载程序至单片机。
 
七、实验现象
 
- 烧录完成后，先按 RESET 按键并松开，程序正常运行，LED 亮起。
- 按下并松开 USER 按键，LED 状态翻转（亮 ↔ 灭）。
 ![[701cc405fc49b2cbc52f5775c3929e6c_720.jpg|533]]![[f1781e433583440310c305d83faae4d6_720.jpg]]
八、实验遇到的问题及解决方法
 
1. 不清楚硬件引脚对应关系
 
刚开始实验时不了解 LED 和按键对应的 GPIO，无法进行配置。
通过查阅资料和咨询 AI 后确认：LD2 对应 PA5，USER 按键对应 PC13。
 
2. 误将 RESET 按键当作控制开关
 
烧录后灯默认熄灭，按下 RESET 不松手灯灭，松开后灯亮，误以为 RESET 是开关。
实际上 RESET 是复位按键，按下会让系统不断初始化，并非控制按键。真正的按键是 USER（PC13）。
 
3. 刚烧录时 USER 按键无反应
 
烧录程序后直接按 USER 按键没有效果。
原因是程序烧录后需要复位一次才能正常进入主循环。先按 RESET 并松开，之后按键即可正常控制 LED。
 
九、实验总结
 
本次实验完成了 STM32F411RET6 的基础工程搭建，掌握了 GPIO 输入输出配置、按键消抖与 LED 控制逻辑。同时明确了 RESET 与 USER 按键的区别，对单片机启动流程和硬件功能有了更直观的理解。



附，完整代码
#include "main.h"

uint8_t led_state = 0;

void SystemClock_Config(void);
static void MX_GPIO_Init(void);

int main(void)
{
  HAL_Init();
  SystemClock_Config();
  MX_GPIO_Init();

  while (1)
  {
    // ?? PC13 ??(????,???????)
    if (HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13) == GPIO_PIN_SET)
    {
      HAL_Delay(20);  // ??
      
      if (HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13) == GPIO_PIN_SET)
      {
        led_state = !led_state;        // ?? LED ??
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, led_state);  // ?? PA5 LED
        
        while (HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_13) == GPIO_PIN_SET);  // ????
      }
    }
  }
}

void SystemClock_Config(void)
{
  // ????????(???)
  RCC_OscInitTypeDef RCC_OscInitStruct = {0};
  RCC_ClkInitTypeDef RCC_ClkInitStruct = {0};

  __HAL_RCC_PWR_CLK_ENABLE();
  __HAL_PWR_VOLTAGESCALING_CONFIG(PWR_REGULATOR_VOLTAGE_SCALE1);

  RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSI;
  RCC_OscInitStruct.HSIState = RCC_HSI_ON;
  RCC_OscInitStruct.HSICalibrationValue = RCC_HSICALIBRATION_DEFAULT;
  RCC_OscInitStruct.PLL.PLLState = RCC_PLL_ON;
  RCC_OscInitStruct.PLL.PLLSource = RCC_PLLSOURCE_HSI;
  RCC_OscInitStruct.PLL.PLLM = 8;
  RCC_OscInitStruct.PLL.PLLN = 100;
  RCC_OscInitStruct.PLL.PLLP = RCC_PLLP_DIV2;
  RCC_OscInitStruct.PLL.PLLQ = 7;

  if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK)
  {
    Error_Handler();
  }

  RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK | RCC_CLOCKTYPE_SYSCLK
                                | RCC_CLOCKTYPE_PCLK1 | RCC_CLOCKTYPE_PCLK2;
  RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_PLLCLK;
  RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
  RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV2;
  RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV1;

  if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_3) != HAL_OK)
  {
    Error_Handler();
  }
}

static void MX_GPIO_Init(void)
{
  GPIO_InitTypeDef GPIO_InitStruct = {0};

  __HAL_RCC_GPIOA_CLK_ENABLE();
  __HAL_RCC_GPIOC_CLK_ENABLE();  // ?? GPIOC ??

  // PA5 ??(LED)
  HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET);
  GPIO_InitStruct.Pin = GPIO_PIN_5;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

  // PC13 ??(??),????
  GPIO_InitStruct.Pin = GPIO_PIN_13;
  GPIO_InitStruct.Mode = GPIO_MODE_INPUT;
  GPIO_InitStruct.Pull = GPIO_PULLDOWN;
  HAL_GPIO_Init(GPIOC, &GPIO_InitStruct);
}

void Error_Handler(void)
{
  __disable_irq();
  while (1) {}
}