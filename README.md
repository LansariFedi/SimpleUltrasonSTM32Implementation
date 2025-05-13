# HC-SR04 Ultrasonic Sensor with STM32F407VG

This project demonstrates how to interface an HC-SR04 ultrasonic distance sensor with an STM32F407VG microcontroller. The system measures distances and sends the results via USB to a host computer.

## Hardware Components

- STM32F407VG Discovery Board
- HC-SR04 Ultrasonic Sensor
- USB Cable for power and communication

## Connections

- HC-SR04 Trigger Pin → STM32F407VG PA0
- HC-SR04 Echo Pin → STM32F407VG PA1
- HC-SR04 VCC → 5V
- HC-SR04 GND → GND

## Project Features

- Precise microsecond timing using TIM10
- Distance measurement with centimeter accuracy
- USB CDC Serial communication to send measurements to PC
- System clock configured for optimal performance

## How It Works

The system works by:

1. Sending a 10μs pulse to the HC-SR04 trigger pin
2. Measuring the duration of the echo pulse
3. Converting the time to distance (using the formula: distance = time/58)
4. Transmitting the results via USB CDC

## Key Functions

### `delay_us()`

```c
void delay_us(uint16_t us)
{
    __HAL_TIM_SET_COUNTER(&htim10, 0);
    HAL_TIM_Base_Start(&htim10);
    while (__HAL_TIM_GET_COUNTER(&htim10) < us);
    HAL_TIM_Base_Stop(&htim10);
}
```

This function provides microsecond-level delay precision using Timer 10, which is essential for the precise timing required by the HC-SR04 sensor.

### Main Measurement Logic

```c
HAL_GPIO_WritePin(GPIOA, GPIO_PIN_0, GPIO_PIN_SET);
delay_us(10);
HAL_GPIO_WritePin(GPIOA, GPIO_PIN_0, GPIO_PIN_RESET);

__HAL_TIM_SET_COUNTER(&htim10, 0);
HAL_TIM_Base_Start(&htim10);

while(HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_1) == GPIO_PIN_RESET);
startTime = __HAL_TIM_GET_COUNTER(&htim10);

while(HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_1) == GPIO_PIN_SET);
endTime = __HAL_TIM_GET_COUNTER(&htim10);

HAL_TIM_Base_Stop(&htim10);

timeElapsed = endTime - startTime;
distanceCM = timeElapsed / 58;
```

This sequence shows how the ultrasonic pulse is triggered and timed, with the echo duration converted to distance.

## Clock Configuration

The system clock is configured as follows:

- HSE (High-Speed External) oscillator at 8 MHz
- PLL configuration with:
  - PLLM = 4
  - PLLN = 168
  - PLLP = 2
  - PLLQ = 7
- System clock from PLL with AHB divided by 2
- Resulting in a 84 MHz system clock

## Timer Configuration

TIM10 is configured with:

- Prescaler = 83
- Counter mode = Up-counting
- Period = 65535
- No clock division
- No auto-reload preload

This gives a microsecond-level timer resolution for accurate distance measurement.

## Contact

For any questions about this project, please contact:

Lansari Fedi - lansarifedi7@gmail.com
