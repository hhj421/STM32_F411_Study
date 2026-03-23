📝 第一次点亮 STM32F411RE LED 闪烁实验记录
 
记录从环境搭建到硬件调试的完整过程，包含踩坑与解决方法
 
 
一、实验概述
 
- 实验目标：在 NUCLEO-F411RE 开发板上实现板载 LED（PA5）以 500ms 为周期闪烁
- 核心芯片：STM32F411RET6（Cortex-M4 内核，100MHz 主频）
- 开发工具：STM32CubeMX（可视化配置）+ Keil MDK（编译/下载）
- 硬件平台：NUCLEO-F411RE 开发板（板载 ST-LINK 调试器、用户 LED）
 
 
 
二、实验步骤
 
1. 环境搭建
 
1. 安装 STM32CubeMX，下载并安装 STM32F4 系列固件包（STM32Cube FW_F4 V1.28.3）
2. 安装 Keil MDK-ARM，配置 ARM 编译器，安装 STM32F4 芯片支持包
3. 安装 ST-LINK 驱动，确保开发板连接后能被电脑识别
 
2. 工程配置（STM32CubeMX）
 
1. 新建工程，选择芯片  STM32F411RET6 
2. 引脚配置：将 PA5 设置为  GPIO_Output （推挽输出，无上拉下拉，低速模式），命名为  LED 
- 推荐截图： CubeMX 引脚配置界面 （右侧芯片视图中 PA5 设为输出）![[Pasted image 20260322172609.png]]
3. 时钟配置：将系统时钟 HCLK 配置为 100MHz（芯片最高主频）
- 推荐截图： Clock Configuration 标签页 （显示 HCLK=100MHz）![[Pasted image 20260322172650.png]]
4. 工程管理：设置工程名为  LED_Blink ，选择 MDK-ARM 工具链，生成初始化代码
- 推荐截图： Project Manager 界面 （显示工程名和工具链选择）![[Pasted image 20260322172728.png]]
 
3. 代码编写（Keil MDK）
 
在  main.c  的  while(1)  循环中添加闪烁逻辑：
 
c
  
while (1)
{
  HAL_GPIO_TogglePin(LED_GPIO_Port, LED_Pin); // 翻转 LED 电平
  HAL_Delay(500); // 延时 500ms
}
 
 
- 推荐截图： Keil 代码编辑界面 （显示 main.c 中 while 循环的闪烁代码）
 ![[Pasted image 20260322173000.png]]
1. 编译与下载
 
1. 编译工程，确保无语法错误
- 推荐截图： Keil Build Output 窗口 （显示 0 Error(s)）
- ![[Pasted image 20260322173040.png]]
2. 连接开发板到电脑，通过 Keil 下载代码到 Flash
3. 验证效果：板载绿色 LED（LD2）开始周期性闪烁
- 推荐照片： 开发板实物图 （显示红色电源灯和绿色 LED 亮起）
 ![[8d9983ceb9a57674098b552a87019953_720.jpg]]


 
三、遇到的问题与解决方法
 
问题 1：固件包缺失，无法生成代码
 
- 现象：CubeMX 弹窗提示  The Firmware Package is not available ，无法生成工程代码
- 解决：点击弹窗  Yes ，自动下载并安装对应版本的 STM32F4 固件包；若下载失败，手动从 ST 官网下载后通过  Manage embedded software packages  安装
- 推荐截图： CubeMX 固件包缺失弹窗 
 ![[3d7ad94c74b89b5b8cf51c7408f41139_720.jpg]]
问题 2：编译器版本不兼容，编译失败
 
- 现象：Keil 编译时提示  Target uses ARM-Compiler 'Default Compiler Version 5' which is not available ，编译中止
- 解决：打开  Options for Target → Target ，将 ARM 编译器切换为已安装的  Version 6 ，重新编译
- 推荐截图： Keil Target 选项界面 （显示编译器版本切换）
 
问题 3：ST-LINK 未检测到，下载失败
 
- 现象：Keil 下载时提示  No ST-LINK detected ，无法烧录代码
- 解决：
1. 更换能传输数据的 USB 数据线，避免仅充电线
2. 检查设备管理器，确认  STM32 STLink  设备正常显示，若驱动异常则重新安装 ST-LINK 驱动
3. Keil 中确认调试器选择为  ST-LINK Debugger ，端口为  SW  模式
- 推荐截图： 设备管理器中 STM32 STLink 设备 、 Keil Debug 配置界面 ![[Pasted image 20260322173839.png|326]]![[Pasted image 20260322174805.png]]
 ![[b8607bf04ea24ccac13ccc573bb2cc7f.jpg|445]![[749ad8c637f849cdbad7e41516e53f9b.jpeg~tplv-a9rns2rl98-image.png|550]
问题 4：ST-LINK 进入 DFU 模式，通信错误
 
- 现象：设备管理器显示  STM32 BOOTLOADER ，ST-LINK 升级工具提示  Communication error ，无法正常下载
- 解决：
1. 强制进入 DFU 模式：短接开发板 ST-LINK 部分的  BOOT0  与  VDD  引脚，重新上电
2. 使用  ST-LinkUpgrade.exe  工具连接设备，自动升级 ST-LINK 固件
3. 升级完成后断开短接，重启开发板，恢复正常  STM32 STLink  设备状态
- 推荐照片： 开发板 BOOT0 与 VDD 短接实物图 、 ST-LinkUpgrade 工具升级成功界面 
 ![[Pasted image 20260322173932.png]]
 ![[Pasted image 20260322173951.png]]![[2335c11ee6cbce17ed53810b48132dac_720.jpg]]
 
四、实验结果与总结
 
✅ 成功标志
 
板载绿色 LED（LD2）稳定以 500ms 亮、500ms 灭的周期闪烁，验证了 GPIO 输出控制、延时函数、代码下载与硬件调试的完整流程。
 
- 推荐照片： 最终运行效果 （开发板上绿色 LED 闪烁状态）
 ![[8d9983ceb9a57674098b552a87019953_720 1.jpg]]
📌 经验收获
 
- 工具链认知：掌握了 STM32CubeMX 可视化配置 + Keil 编译下载的标准开发流程
- 硬件调试：学会了排查 USB 连接、驱动安装、ST-LINK 固件等常见硬件问题
- 底层理解：理解了 LED 闪烁的核心原理——通过翻转 GPIO 电平控制 LED 通断，配合延时函数实现周期性闪烁
 
🚀 后续方向
 
- 进阶实验：尝试按键控制 LED 亮灭、定时器精准延时、中断控制闪烁频率
- 知识拓展：深入学习 STM32 时钟树、GPIO 工作模式、HAL 库函数原理
 
 
 
五、附录：代码
 
/* USER CODE BEGIN Header */
/**
  ******************************************************************************
  * @file           : main.c
  * @brief          : Main program body
  ******************************************************************************
  * @attention
  *
  * Copyright (c) 2026 STMicroelectronics.
  * All rights reserved.
  *
  * This software is licensed under terms that can be found in the LICENSE file
  * in the root directory of this software component.
  * If no LICENSE file comes with this software, it is provided AS-IS.
  *
  ******************************************************************************
  */
/* USER CODE END Header */
/* Includes ------------------------------------------------------------------*/
#include "main.h"

/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */

/* USER CODE END Includes */

/* Private typedef -----------------------------------------------------------*/
/* USER CODE BEGIN PTD */

/* USER CODE END PTD */

/* Private define ------------------------------------------------------------*/
/* USER CODE BEGIN PD */

/* USER CODE END PD */

/* Private macro -------------------------------------------------------------*/
/* USER CODE BEGIN PM */

/* USER CODE END PM */

/* Private variables ---------------------------------------------------------*/

/* USER CODE BEGIN PV */

/* USER CODE END PV */

/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);
static void MX_GPIO_Init(void);
/* USER CODE BEGIN PFP */

/* USER CODE END PFP */

/* Private user code ---------------------------------------------------------*/
/* USER CODE BEGIN 0 */

/* USER CODE END 0 */

/**
  * @brief  The application entry point.
  * @retval int
  */
int main(void)
{

  /* USER CODE BEGIN 1 */

  /* USER CODE END 1 */

  /* MCU Configuration--------------------------------------------------------*/

  /* Reset of all peripherals, Initializes the Flash interface and the Systick. */
  HAL_Init();

  /* USER CODE BEGIN Init */

  /* USER CODE END Init */

  /* Configure the system clock */
  SystemClock_Config();

  /* USER CODE BEGIN SysInit */

  /* USER CODE END SysInit */

  /* Initialize all configured peripherals */
  MX_GPIO_Init();
  /* USER CODE BEGIN 2 */

  /* USER CODE END 2 */

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */

    while (1)
	{
  /* USER CODE END WHILE */
  HAL_GPIO_TogglePin(LED_GPIO_Port, LED_Pin); // ??LED??
  HAL_Delay(500); // ??500ms
  /* USER CODE BEGIN 3 */
	}
  /* USER CODE END 3 */
}

/**
  * @brief System Clock Configuration
  * @retval None
  */
void SystemClock_Config(void)
{
  RCC_OscInitTypeDef RCC_OscInitStruct = {0};
  RCC_ClkInitTypeDef RCC_ClkInitStruct = {0};

  /** Configure the main internal regulator output voltage
  */
  __HAL_RCC_PWR_CLK_ENABLE();
  __HAL_PWR_VOLTAGESCALING_CONFIG(PWR_REGULATOR_VOLTAGE_SCALE1);

  /** Initializes the RCC Oscillators according to the specified parameters
  * in the RCC_OscInitTypeDef structure.
  */
  RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSI;
  RCC_OscInitStruct.HSIState = RCC_HSI_ON;
  RCC_OscInitStruct.HSICalibrationValue = RCC_HSICALIBRATION_DEFAULT;
  RCC_OscInitStruct.PLL.PLLState = RCC_PLL_NONE;
  if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK)
  {
    Error_Handler();
  }

  /** Initializes the CPU, AHB and APB buses clocks
  */
  RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK|RCC_CLOCKTYPE_SYSCLK
                              |RCC_CLOCKTYPE_PCLK1|RCC_CLOCKTYPE_PCLK2;
  RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_HSI;
  RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
  RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV1;
  RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV1;

  if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_0) != HAL_OK)
  {
    Error_Handler();
  }
}

/**
  * @brief GPIO Initialization Function
  * @param None
  * @retval None
  */
static void MX_GPIO_Init(void)
{
  GPIO_InitTypeDef GPIO_InitStruct = {0};
  /* USER CODE BEGIN MX_GPIO_Init_1 */

  /* USER CODE END MX_GPIO_Init_1 */

  /* GPIO Ports Clock Enable */
  __HAL_RCC_GPIOA_CLK_ENABLE();

  /*Configure GPIO pin Output Level */
  HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, GPIO_PIN_RESET);

  /*Configure GPIO pin : LED_Pin */
  GPIO_InitStruct.Pin = LED_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(LED_GPIO_Port, &GPIO_InitStruct);

  /* USER CODE BEGIN MX_GPIO_Init_2 */

  /* USER CODE END MX_GPIO_Init_2 */
}

/* USER CODE BEGIN 4 */

/* USER CODE END 4 */

/**
  * @brief  This function is executed in case of error occurrence.
  * @retval None
  */
void Error_Handler(void)
{
  /* USER CODE BEGIN Error_Handler_Debug */
  /* User can add his own implementation to report the HAL error return state */
  __disable_irq();
  while (1)
  {
  }
  /* USER CODE END Error_Handler_Debug */
}
#ifdef USE_FULL_ASSERT
/**
  * @brief  Reports the name of the source file and the source line number
  *         where the assert_param error has occurred.
  * @param  file: pointer to the source file name
  * @param  line: assert_param error line source number
  * @retval None
  */
void assert_failed(uint8_t *file, uint32_t line)
{
  /* USER CODE BEGIN 6 */
  /* User can add his own implementation to report the file name and line number,
     ex: printf("Wrong parameters value: file %s on line %d\r\n", file, line) */
  /* USER CODE END 6 */
}
#endif /* USE_FULL_ASSERT */

 
 
 
 
