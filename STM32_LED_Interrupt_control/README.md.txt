STM32F411RET6 TIM2定时器中断控制LED闪烁实验
 
基于STM32CubeMX + HAL库，在Nucleo-F411RE开发板上实现TIM2定时器中断控制板载LED周期性闪烁。
 
🎯 实验目的
 
掌握STM32定时器中断配置，实现精准非阻塞延时，控制PA5引脚LED以500ms周期闪烁。
 
⚙️ 实验环境与硬件
 
- 开发板：STM32 Nucleo-F411RE (STM32F411RET6)
- 编程软件：STM32CubeMX, Keil MDK-ARM
- 硬件资源：
- LED：板载绿灯LD2 (GPIO: PA5)
- 定时器：TIM2 (内部时钟，中断模式)
 
🚀 快速上手
 
1. 获取代码：下载或Clone本项目到本地。
2. 打开工程：用Keil5打开 MDK-ARM 目录下的 .uvprojx 工程文件。
3. 编译下载：编译工程后下载到开发板。
4. 测试现象：复位后LED以500ms亮/500ms灭稳定闪烁。
 
💡 核心逻辑
 
配置TIM2定时器10ms中断，累计50次中断后翻转PA5电平，主循环空转，由中断异步实现LED控制。