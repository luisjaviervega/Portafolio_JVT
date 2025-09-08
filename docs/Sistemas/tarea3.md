#Inputs
##3 compuertas básicas, AND / OR / XOR con 2 botones
Qué debe hacer: Con dos botones A y B (pull-up; presionado=0) enciende tres LEDs que muestren en paralelo los resultados de AND, OR y XOR. En el video muestra las 4 combinaciones (00, 01, 10, 11).

##Código: Para compuerta AND

```
#include "pico/stdlib.h"
#include "hardware/gpio.h"

#define BTN_A 0
#define BTN_B 1
#define LED_AND 2

int main() {
    stdio_init_all();

    gpio_init(BTN_A);
    gpio_set_dir(BTN_A, false);
    gpio_pull_up(BTN_A);

    gpio_init(BTN_B);
    gpio_set_dir(BTN_B, false);
    gpio_pull_up(BTN_B);

    gpio_init(LED_AND);
    gpio_set_dir(LED_AND, true);

    while (true) {
        bool a = !gpio_get(BTN_A);
        bool b = !gpio_get(BTN_B);

        bool result = a && b;

        gpio_put(LED_AND, result);

        sleep_ms(50);
    }
}

```
##Código: Para compuerta OR

```

#include "pico/stdlib.h"
#include "hardware/gpio.h"

#define BTN_A 0
#define BTN_B 1
#define LED_OR 2

int main() {
    stdio_init_all();

    gpio_init(BTN_A); gpio_set_dir(BTN_A, false); gpio_pull_up(BTN_A);
    gpio_init(BTN_B); gpio_set_dir(BTN_B, false); gpio_pull_up(BTN_B);

    gpio_init(LED_OR); gpio_set_dir(LED_OR, true);

    while (true) {
        bool a = !gpio_get(BTN_A);
        bool b = !gpio_get(BTN_B);

        bool result = a || b;

        gpio_put(LED_OR, result);
        sleep_ms(50);
    }
}

```
##Código: Para compuerta XOR

```
#include "pico/stdlib.h"
#include "hardware/gpio.h"

#define BTN_A 0
#define BTN_B 1
#define LED_XOR 2

int main() {
    stdio_init_all();

    gpio_init(BTN_A); gpio_set_dir(BTN_A, false); gpio_pull_up(BTN_A);
    gpio_init(BTN_B); gpio_set_dir(BTN_B, false); gpio_pull_up(BTN_B);

    gpio_init(LED_XOR); gpio_set_dir(LED_XOR, true);

    while (true) {
        bool a = !gpio_get(BTN_A);
        bool b = !gpio_get(BTN_B);

        bool result = a ^ b;

        gpio_put(LED_XOR, result);
        sleep_ms(50);
    }
}

```
##Esquemático de conexión: Se usó las mismas conexiones para las 3 diferentes compuertas.
![Diagrama del sistema](ANDORXOR.png)