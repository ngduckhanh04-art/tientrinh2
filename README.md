#include "stm32f10x.h"

#define LED_PIN     GPIO_Pin_0
#define BUTTON_PIN  GPIO_Pin_1

void GPIO_Config(void) {
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);

    GPIO_InitTypeDef gpio;

    gpio.GPIO_Pin = LED_PIN;
    gpio.GPIO_Speed = GPIO_Speed_50MHz;
    gpio.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_Init(GPIOA, &gpio);

    gpio.GPIO_Pin = BUTTON_PIN;
    gpio.GPIO_Speed = GPIO_Speed_50MHz;
    gpio.GPIO_Mode = GPIO_Mode_IPD;   
    GPIO_Init(GPIOA, &gpio);
}

int main(void) {
    GPIO_Config();

    uint8_t lastState = 0;  
    uint8_t ledState = 0;    

    GPIO_ResetBits(GPIOA, LED_PIN);

    while (1) {
        uint8_t currentState = GPIO_ReadInputDataBit(GPIOA, BUTTON_PIN);

        if (lastState == 0 && currentState == 1) {
            ledState = !ledState;

            if (ledState)
                GPIO_SetBits(GPIOA, LED_PIN);
            else
                GPIO_ResetBits(GPIOA, LED_PIN);

            for (volatile int i = 0; i < 50000; i++);
        }

        lastState = currentState;
    }
}
