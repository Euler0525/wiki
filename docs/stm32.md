# STM32

## [HAL库函数](https://blog.csdn.net/KK_546464/article/details/111307451)

### GPIO

```C
typedef enum {
    GPIO_PIN_RESET = 0U,
    GPIO_PIN_SET
} GPIO_PinState;
```

---

```C
/**
 * HAL_GPIO_ReadPin(KEY1_GPIO_Port, KEY1_Pin);
 * @return GPIO_PIN_RESET / GPIO_PIN_SET
 */
GPIO_PinState HAL_GPIO_ReadPin(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);

/**
 * HAL_GPIO_WritePin(LED_RED_GPIO_Port, LED_RED_Pin, GPIO_PIN_SET);
 */
void HAL_GPIO_WritePin(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, GPIO_PinState PinState);

/**
 * HAL_GPIO_TogglePin(LED_RED_GPIO_Port, LED_RED_Pin);
 */
void HAL_GPIO_TogglePin(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);

/**
 * HAL_GPIO_EXTI_IRQHandler(KEY1_Pin);
 */
void HAL_GPIO_EXTI_IRQHandler(uint16_t GPIO_Pin);

__weak void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin);     
```

### UART

```C
/**
 * HAL_UART_Transmit(&huart2, recv_data_it, 2, HAL_MAX_DELAY);
 */
HAL_StatusTypeDef HAL_UART_Transmit(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size, uint32_t Timeout);
/**
 * HAL_UART_Receive(&huart2, recv_data_it, 2, HAL_MAX_DELAY);
 */
HAL_StatusTypeDef HAL_UART_Receive(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size, uint32_t Timeout);

/**
 * HAL_UART_Transmit_IT(&huart2, recv_data_it, 2);
 */
HAL_StatusTypeDef HAL_UART_Transmit_IT(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size);
/**
 * HAL_UART_Receive_IT(&huart2, recv_data_it, 2);
 */
HAL_StatusTypeDef HAL_UART_Receive_IT(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size);

/**
 * HAL_UART_Transmit_DMA(&huart2, recv_data_dma, Size);
 */
HAL_StatusTypeDef HAL_UART_Transmit_DMA(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size);

/**
 * HAL_UARTEx_ReceiveToIdle_DMA(&huart2, recv_data_dma, sizeof(recv_data_dma));
 */
HAL_StatusTypeDef HAL_UARTEx_ReceiveToIdle_DMA(UART_HandleTypeDef *huart, uint8_t *pData, uint16_t Size);

/**
 * HAL_UART_IRQHandler(&huart2);
 */
void HAL_UART_IRQHandler(UART_HandleTypeDef *huart);

__weak void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart);
```

### IIC

```c
/**
 * HAL_I2C_Master_Transmit(&hi2c1, AHT20_ADDR, send_buffer, 3);
 */
HAL_StatusTypeDef HAL_I2C_Master_Transmit(I2C_HandleTypeDef *hi2c, uint16_t DevAddress, uint8_t *pData, uint16_t Size, uint32_t Timeout);

/**
 * HAL_I2C_Master_Receive(&hi2c1, AHT20_ADDR, read_buffer, 6);
 */
HAL_StatusTypeDef  HAL_I2C_Master_Receive(I2C_HandleTypeDef *hi2c, uint16_t DevAddress, uint8_t *pData, uint16_t Size, uint32_t Timeout);

/**
 * HAL_I2C_Master_Transmit_IT(&hi2c1, AHT20_ADDR, send_buffer, 3);
 */
HAL_StatusTypeDef HAL_I2C_Master_Transmit_IT(I2C_HandleTypeDef *hi2c, uint16_t DevAddress, uint8_t *pData, uint16_t Size);

/**
 * HAL_I2C_Master_Receive_IT(&hi2c1, AHT20_ADDR, read_buffer, 6);
 */
HAL_StatusTypeDef HAL_I2C_Master_Receive_IT(I2C_HandleTypeDef *hi2c, uint16_t DevAddress, uint8_t *pData, uint16_t Size);

/**
 * HAL_I2C_Master_Transmit_DMA(&hi2c1, AHT20_ADDR, send_buffer, 3);
 */
HAL_StatusTypeDef HAL_I2C_Master_Transmit_DMA(I2C_HandleTypeDef *hi2c, uint16_t DevAddress, uint8_t *pData, uint16_t Size);

/**
 * HAL_I2C_Master_Receive_DMA(&hi2c1, AHT20_ADDR, read_buffer, 6);
 */
HAL_StatusTypeDef HAL_I2C_Master_Receive_DMA(I2C_HandleTypeDef *hi2c, uint16_t DevAddress, uint8_t *pData, uint16_t Size);

/**
 * void HAL_I2C_MasterTxCpltCallback(I2C_HandleTypeDef *hi2c);
 */
__weak void HAL_I2C_MasterTxCpltCallback(I2C_HandleTypeDef *hi2c);

/**
 * void HAL_I2C_MasterRxCpltCallback(I2C_HandleTypeDef *hi2c);
 */
__weak void HAL_I2C_MasterRxCpltCallback(I2C_HandleTypeDef *hi2c);
```

### TIM



### PWM



### ADC



### DAC



### DMA



### CAN



### SPI



### FLASH



### RTC



### IWDG && WWDG



### TIM





