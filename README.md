# TIMER_PWM_ON_SERVO STM32 Project

This repository contains a complete Servo Motor control application using Hardware Timers and PWM (Pulse Width Modulation), developed for the STM32F407VETx microcontroller. The project demonstrates how to configure timer peripherals, calculate prescaler and auto-reload values for 50Hz PWM, and utilize the STM32 HAL framework within the STM32CubeIDE ecosystem to create a smooth servo sweeping motion.

## Development Environment
* **IDE:** STM32CubeIDE
* **Framework:** STM32 HAL (Hardware Abstraction Layer
* **Target MCU:** STM32F407VETx

## Hardware & Programmer
* **Board:** STM32F407 Development Board (core logic is fully adaptable for Geehy/APM32 equivalent architectures).
* **Actuator:** Standard Servo Motor (e.g., SG90 or MG995).
* **Programmer:** ST-LINK V2
* **Wiring Setup:** 
  * Servo `GND` (Brown/Black wire) -> STM32 `GND`
  * Servo `VCC` (Red wire) -> External `5V` Power Supply (Servos draw too much current for the MCU's 3.3V pin; **ensure you connect the external power supply's GND to the STM32 GND**).
  * Servo `PWM/Signal` (Orange/Yellow wire) -> STM32 `PA0` (TIM2_CH1).

## Quick Start Guide
### 1. create the project.
### 2. IOC Pin & Timer Configuration
To drive a standard servo, you need a 50Hz (20ms period) PWM signal. Based on the 84 MHz system clock (`HCLK`), configure the following:

1. Open the `.ioc` Device Configuration Tool.
2. Go to **System Core > SYS** and set **Debug** to **Serial Wire** (crucial for reprogramming).
3. Go to **Timers > TIM2**.
4. Set **Clock Source** to **Internal Clock**.
5. Set **Channel 1** to **PWM Generation CH1**. (This automatically assigns pin `PA0`).
6. In the **Parameter Settings** for TIM2, configure the following to get exactly 50Hz:
   * **Prescaler (PSC):** `84 - 1` (Reduces the 84MHz clock to 1MHz)
   * **Counter Period (ARR):** `20000 - 1` (1MHz / 20000 = 50Hz)
   * **Auto-reload preload:** Enable
7. Expand **PWM Generation Channel 1** settings:
   * **Pulse:** `0` (Initial pulse value)
8. Save (`Ctrl+S`) and click **Yes** to generate the initialization code[cite: 2].

### 3. Application Code
Open `Core/Src/main.c`. Start the PWM signal before the infinite loop, and then add the sweeping logic utilizing `__HAL_TIM_SET_COMPARE` inside the loop.

/* USER CODE BEGIN WHILE */
  while (1)
  {
      // Sweep forward (0 to 180 degrees)
      for (int i = 1000; i <= 2000; i += 20) {
          __HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_1, i);
          HAL_Delay(20);
      }
      HAL_Delay(500);
      for (int i = 2000; i >= 1000; i -= 20) {
          __HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_1, i);
          HAL_Delay(20);
      }
      HAL_Delay(500);
  }


 ### 4. Build and Flash (Using ST-LINK)
Click the Build (Hammer) icon and verify the .elf binary is generated with zero errors[cite: 2].

Connect your ST-LINK and target board via USB.

Click the Run (Play) icon, leave the ST-LINK debug probe defaults, and click OK.

The servo will immediately begin smoothly sweeping forward and backward.

# I uploaded the zip file of this project in case there is any issue. just extract the zip file and import in the stmcube IDE for the reference.
