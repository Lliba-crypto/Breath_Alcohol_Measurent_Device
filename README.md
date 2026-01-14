# Breath_Alcohol_Measurent_Device
Introduction to Electric and Telecomunication projects


📌 README này vừa mô tả phần cứng, kết nối, cấu hình `.ioc`, vừa hướng dẫn cách thêm thư viện LCD và viết code trong `main.c`.  

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
  LiquidCrystal_I2C lcd;               // Tạo biến struct lcd.
  LCD_Init(&lcd, &hi2c1, 0x27, 16, 2); // Khởi tạo LCD: gán handle I2C (hi2c1), địa chỉ I2C (0x27), số cột (16), số hàng (2).
- Application:
  ~~~c
  LCD_Clear(&lcd);              // Xóa toàn màn hình
  LCD_Backlight(&lcd);          // Bật đèn nền LCD
  LCD_SetCursor(&lcd, 0, 0);    // Đặt con trỏ tại vị trí (0,0)
  LCD_Print(&lcd, "Hello world!"); // In chuỗi tại vị trí con trỏ
- Đọc giá trị MQ-3 qua ADC, chuyển đổi sang điện áp và hiển thị BAC:
  ~~~c
  HAL_ADC_PollForConversion(&hadc1, HAL_MAX_DELAY);
  uint32_t adc_value = HAL_ADC_GetValue(&hadc1);
  float voltage = (adc_value / 4095.0f) * 3.3f;
  float bac = voltage * 0.4f; // hệ số giả định, cần hiệu chuẩn

  char buffer[16];
  snprintf(buffer, sizeof(buffer), "BAC: %.2f", bac);
  LCD_SetCursor(&lcd, 0, 0);
  LCD_Print(&lcd, buffer);
- Kiểm tra ngưỡng BAC để bật LED và buzzer:
  ~~~c
  if(bac > 0.25f) {
    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, GPIO_PIN_RESET); // LED ON
    __HAL_TIM_SET_COMPARE(&htim1, TIM_CHANNEL_1, 500);     // Buzzer kêu
  } else {
    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, GPIO_PIN_SET);   // LED OFF
    __HAL_TIM_SET_COMPARE(&htim1, TIM_CHANNEL_1, 0);       // Buzzer tắt
  }

