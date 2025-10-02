#T7

##Ejercicio7.1 PWM

##Instrucciones:

Implementar un circuito con un motor DC controlado mediante PWM variando el duty cycle.

Usar 2 botones para seleccionar entre 3 velocidades predefinidas (baja, media y alta).

Documentar:

Valores de duty usados, con el porque.

Circuito

##Codigo


```c
 
#include "pico/stdlib.h"
#include "hardware/pwm.h"
#include <stdio.h>
 
#define PWM_PIN     0
#define AIN1_PIN    1
#define AIN2_PIN    2
#define STBY_PIN    3
#define BTN_INC_PIN 4
#define BTN_DEC_PIN 5
 

#define PWM_WRAP 625u
#define PWM_CLKDIV 10.0f
 

#define DUTY_LOW_PCT  30.0f
#define DUTY_MED_PCT  60.0f
#define DUTY_HIGH_PCT 90.0f
 

#define DEBOUNCE_MS 50
 

static uint slice_num;
static uint chan;
static uint32_t duty_vals[3];
 

static void pwm_init_for_pin(uint gpio_pin) {
    gpio_set_function(gpio_pin, GPIO_FUNC_PWM);
    slice_num = pwm_gpio_to_slice_num(gpio_pin);
    chan = pwm_gpio_to_channel(gpio_pin);
    pwm_set_wrap(slice_num, PWM_WRAP);
    pwm_set_clkdiv(slice_num, PWM_CLKDIV);
    pwm_set_chan_level(slice_num, chan, 0);
    pwm_set_enabled(slice_num, true);
}
 
static bool button_pressed_once(uint gpio_pin) {
    static absolute_time_t last_time_inc = {0};
    static absolute_time_t last_time_dec = {0};
    absolute_time_t *last_time = (gpio_pin == BTN_INC_PIN) ? &last_time_inc : &last_time_dec;
 
    if (gpio_get(gpio_pin) == 0) {
        absolute_time_t now = get_absolute_time();
        if (absolute_time_diff_us(*last_time, now) < (DEBOUNCE_MS * 1000)) return false;
        sleep_ms(10);
        if (gpio_get(gpio_pin) == 0) {
            *last_time = make_timeout_time_ms(DEBOUNCE_MS);
            while (gpio_get(gpio_pin) == 0) sleep_ms(5);
            return true;
        }
    }
    return false;
}
 

int main() {
    stdio_init_all();
 

    gpio_init(AIN1_PIN); gpio_set_dir(AIN1_PIN, GPIO_OUT);
    gpio_init(AIN2_PIN); gpio_set_dir(AIN2_PIN, GPIO_OUT);
    gpio_init(STBY_PIN); gpio_set_dir(STBY_PIN, GPIO_OUT);
 
    gpio_init(BTN_INC_PIN); gpio_set_dir(BTN_INC_PIN, GPIO_IN); gpio_pull_up(BTN_INC_PIN);
    gpio_init(BTN_DEC_PIN); gpio_set_dir(BTN_DEC_PIN, GPIO_IN); gpio_pull_up(BTN_DEC_PIN);


    pwm_init_for_pin(PWM_PIN);
 

    duty_vals[0] = (uint32_t)((DUTY_LOW_PCT / 100.0f) * (float)(PWM_WRAP + 1));
    duty_vals[1] = (uint32_t)((DUTY_MED_PCT / 100.0f) * (float)(PWM_WRAP + 1));
    duty_vals[2] = (uint32_t)((DUTY_HIGH_PCT / 100.0f) * (float)(PWM_WRAP + 1));
 
    int speed_idx = 0;
 

    gpio_put(AIN1_PIN, 1);
    gpio_put(AIN2_PIN, 0);
 
    
    gpio_put(STBY_PIN, 1);


    pwm_set_chan_level(slice_num, chan, duty_vals[speed_idx]);
 
    printf("Control motor iniciado. PWM pin: %d\n", PWM_PIN);
 
    while (true) {
        if (button_pressed_once(BTN_INC_PIN)) {
            speed_idx = (speed_idx + 1) % 3;
            pwm_set_chan_level(slice_num, chan, duty_vals[speed_idx]);
            printf("Velocidad -> %d (level=%u)\n", speed_idx, (unsigned)duty_vals[speed_idx]);
        }
        if (button_pressed_once(BTN_DEC_PIN)) {
            speed_idx = (speed_idx + 2) % 3; 
            pwm_set_chan_level(slice_num, chan, duty_vals[speed_idx]);
            printf("Velocidad -> %d (level=%u)\n", speed_idx, (unsigned)duty_vals[speed_idx]);
        }
        sleep_ms(20);
    }
 
    return 0;
}
 
``` 

##Esquemático

##Video


##Ejercicio7.2 Melodia con buzzer

##Instrucciones:

Programar un buzzer piezoeléctrico para reproducir una melodía reconocible.

Variar la frecuencia del PWM para las notas, manteniendo el duty en 50 %.

Cada nota debe incluir su frecuencia y duración en el código.

```c
#include "pico/stdlib.h"
#include "hardware/pwm.h"
#include <stdio.h>
 
#define PIN_BUZZER 0
#define TOP_PWM 1023
 

#define TEMPO 108
 

#define CLKDIV_MIN 1.0f
#define CLKDIV_MAX 255.0f
 
// duty fijo al 50%
#define DUTY_50 (TOP_PWM/2)
 
typedef struct {
    int frecuencia;   
    int token;        
} Nota;
 

// token: 8 = corchea, 4 = negra, 2 = blanca, 1 = redonda. Token negativo = puntillo (1.5×).
static const Nota melodia[] = {
    {466, 8}, {466, 8}, {466, 8}, {698, 2}, {1047, 2},
    {932, 8}, {880, 8}, {784, 8}, {1397, 2}, {1047, 4},
    {932, 8}, {880, 8}, {784, 8}, {1397, 2}, {1047, 4},
    {932, 8}, {880, 8}, {932, 8}, {784, 2},
    {523, 8}, {523, 8}, {523, 8}, {698, 2}, {1047, 2},
    {932, 8}, {880, 8}, {784, 8}, {1397, 2}, {1047, 4},
    {932, 8}, {880, 8}, {784, 8}, {1397, 2}, {1047, 4},
    {932, 8}, {880, 8}, {932, 8}, {784, 2},
    {523, -8}, {523, 16}, {587, -4}, {587, 8},
    {932, 8}, {880, 8}, {784, 8}, {698, 8}, {698, 8},
    {784, 8}, {880, 8}, {784, 4}, {587, 8}, {659, 4},
    {523, -8}, {523, 16}, {587, -4}, {587, 8},
    {932, 8}, {880, 8}, {784, 8}, {698, 8}, {1047, -8}, {784, 16}, {784, 2},
    {0, 8}, {523, 8}, {587, -4}, {587, 8},
    {932, 8}, {880, 8}, {784, 8}, {698, 8}, {698, 8},
    {784, 8}, {880, 8}, {784, 4}, {587, 8}, {659, 4},
    {1047, -8}, {1047, 16}, {1397, 4}, {1245, 8}, {1109, 4},
    {1047, 8}, {932, 4}, {831, 8}, {784, 4}, {698, 8}, {1047, 1}
};
 
static const int MELODIA_LEN = sizeof(melodia) / sizeof(melodia[0]);
 
static inline float limitar_f(float v, float minimo, float maximo) {
    if (v < minimo) return minimo;
    if (v > maximo) return maximo;
    return v;
}
 
int calcular_duracion_ms(int tempo, int token) {

    float nota_entera = (60000.0f / tempo) * 4.0f;
    float duracion;
    if (token > 0) {
        duracion = nota_entera / (float)token;
    } else {

        duracion = nota_entera / (float)(-token);
        duracion *= 1.5f;
    }
    return (int)(duracion + 0.5f);
}
 
int main() {
    stdio_init_all();

    gpio_set_function(PIN_BUZZER, GPIO_FUNC_PWM);
    uint slice = pwm_gpio_to_slice_num(PIN_BUZZER);
    uint canal = pwm_gpio_to_channel(PIN_BUZZER);
    pwm_set_wrap(slice, TOP_PWM);
    pwm_set_chan_level(slice, canal, 0);
    pwm_set_enabled(slice, true);
 
    const int tempo = TEMPO;
 
    while (true) {
        for (int i = 0; i < MELODIA_LEN; i++) {
            int freq = melodia[i].frecuencia;
            int token = melodia[i].token;
 
            int notaMs = calcular_duracion_ms(tempo, token);
 
            if (freq == 0) {

                pwm_set_chan_level(slice, canal, 0);
                sleep_ms((int)(notaMs * 0.9f));
            
                sleep_ms((int)(notaMs * 0.1f));
                continue;
            }
 
            const float f_clk = 125000000.0f;
            float clkdiv = f_clk / ((float)freq * (TOP_PWM + 1));
            clkdiv = limitar_f(clkdiv, CLKDIV_MIN, CLKDIV_MAX);
            pwm_set_clkdiv(slice, clkdiv);
 
            pwm_set_chan_level(slice, canal, DUTY_50);
            
            sleep_ms((int)(notaMs * 0.9f));
 
            pwm_set_chan_level(slice, canal, 0);
            sleep_ms((int)(notaMs * 0.1f));
        }
 
        
        sleep_ms(1500);
    }
 
    return 0;
}
```

![Diagrama del sistema](T7.2.jpg)