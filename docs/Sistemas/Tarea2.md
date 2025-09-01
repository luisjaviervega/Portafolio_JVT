#Contador binario de bits
En 4 LEDS debe mostrarse un contador del 0 al 15 en binario cada segundo

##Código:


include "pico/stdlib.h"
include "hardware/gpio.h"
 
define A 0
define B 1
define C 2
define D 3
 
int main() {
    const uint32_t MASK = (1u<<A) | (1u<<B) | (1u<<C) | (1u<<D);
    gpio_init_mask(MASK);
    gpio_set_dir_masked(MASK, MASK);
    while (true) {
        for (uint8_t i = 0; i < 16; i++) {
            gpio_put_masked(MASK, i << A);
            sleep_ms(500);
        }
    }
}
 

