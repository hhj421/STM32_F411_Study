# STM32F411RE LED 闪烁实验
NUCLEO-F411RE 开发板，实现板载 LED（PA5）500ms 周期闪烁。

## 开发环境
- 芯片：STM32F411RET6
- 开发板：NUCLEO-F411RE
- 工具：STM32CubeMX + Keil MDK

## 实现功能
- GPIO 输出控制 LED
- HAL_Delay 延时
- 电平翻转闪烁

## 遇到的问题
1. 缺少 STM32F4 固件包
2. Keil 编译器版本错误
3. ST-LINK 进入 DFU 模式，已修复

## 结果
绿色 LED 正常闪烁。
