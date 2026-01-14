# Breath_Alcohol_Measurent_Device
Introduction to Electric and Telecomunication projects

# Breath Alcohol Measurement Project (STM32 Blue Pill)

## 1.1. Deliverables

- Hiển thị chuỗi dữ liệu đo từ cảm biến MQ-3 lên màn hình LCD 1602.
- Debug: kiểm tra hành động hiển thị bằng SWV ITM Data Console trong STM32CubeIDE.

## 1.2. Hardware Needed

1. STM32F103C8T6 Blue Pill Microcontroller
2. MQ-3 Alcohol Sensor
3. LK1: [Màn hình LCD text LCD1602 xanh lá](https://hshop.vn/lcd-text-lcd1602-xanh-lo)
4. LK2: [Mạch chuyển giao tiếp LCD1602, LCD1604, LCD2004 sang I2C](https://hshop.vn/mach-chuyen-giao-tiep-lcd1602-lcd1604-lcd2004-sang-i2c)
5. LED cảnh báo (PC13)
6. Buzzer (PA8, PWM)

## 1.3. References

- https://www.micropeta.com/video61
- Datasheet MQ-3 Sensor
- STM32CubeIDE Documentation

## 1.4. Hardware Connection

### Connect external components with Microcontroller

| No. | Component | STM32F103C8T6 Blue Pill |
| --- | --------- | ----------------------- |
| 1   | MQ-3 Analog Out | PA0 (ADC1_IN0) |
| 2   | LCD1602+I2C Backpack GND | GND |
| 3   | LCD1602+I2C Backpack VCC | 5V |
| 4   | LCD1602+I2C Backpack SDA | PB7 |
| 5   | LCD1602+I2C Backpack SCL | PB6 |
| 6   | LED Alert | PC13 |
| 7   | Buzzer | PA8 (TIM1_CH1 PWM) |

## 1.5. Configure .ioc in STM32CubeIDE

- Enable **ADC1** → chọn PA0 làm ADC1_IN0.
- Enable **I2C1** → chọn PB6 (SCL), PB7 (SDA).
- Enable **TIM1** → chọn PA8 làm TIM1_CH1 (PWM).
- Enable **GPIO Output** cho PC13 (LED cảnh báo).

## 1.6. Code

**AFTER auto-code-generation from 1.5. Configure .ioc in STM32CubeIDE,**

### 1.6.1. Adding Necessary Libraries

- Add `liquidcrystal_i2c.h` inside `Core/Inc` folder.
- Add `liquidcrystal_i2c.c` inside `Core/Src` folder.
- Modify `main.c` at:
  - `/* Includes ------------------------------------------------------------------*/`
  - `/* USER CODE BEGIN Includes */`
  - `/* USER CODE BEGIN PV */`
  - `/* USER CODE BEGIN PFP */`

### 1.6.2. What are Needed Declaration & Function for `main.c`?

- Initialize LCD:
  ```c
  LiquidCrystal_I2C lcd;
  LCD_Init(&lcd, &hi2c1, 0x27, 16, 2);
