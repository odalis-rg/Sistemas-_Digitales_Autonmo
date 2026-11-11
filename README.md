# Apuntes en clase de la materia Sistemas Digitales

Bienvenido a este repositorio. Aquí he reunido y digitalizado mis apuntes, prácticas y aprendizajes de la materia **Sistemas Digitales** 

El objetivo de esta guía es explicar lo que aprendimos semana a semana de una forma clara, sencilla y explicada en humano, sin enredarnos tanto con términos de manual técnico.

## Resumen General de las 4 Semanas

```
[Semana 1] ──► Del PC al Microcontrolador (Fundamentos y Lógica Bit a Bit)
[Semana 2] ──► Dando el salto al ESP32 (Pines, Registros y Tiempos)
[Semana 3] ──► El Reloj Interno (Timers por Hardware y Prescalers)
[Semana 4] ──► ¡Interrupciones! (Reaccionar al instante sin bloquear la CPU)
```

## Semana 1: Del Computador al Microcontrolador

En la primera semana entendimos la diferencia entre una computadora normal (como tu laptop) y un **sistema embebido** (como una lavadora, un microondas o un control remoto).

### Lo clave de la semana:

* **Microprocesador vs. Microcontrolador:** Una PC tiene la CPU en un chip y la RAM/Disco en otros chips aparte. Un microcontrolador tiene **todo dentro de una sola pastilla de silicio** (CPU, RAM, memoria Flash y pines).

* **Las 3 memorias esenciales:**
  * **Flash (El Libro):** Guarda el programa. No se borra al apagar, pero escribir en ella muy seguido la desgasta.
  * **SRAM (La Pizarra):** Es la memoria rápida donde viven tu variables mientras corre el programa. Si apagas el chip, se borra todo.
  * **EEPROM (La Libreta de Notas):** Para guardar datos pequeños que no deben perderse tras un corte de luz (configuraciones, contadores).

* **Manejo de Bits y Máscaras:** Aprendimos a prender, apagar e invertir bits individuales en los registros (`DDRB`, `PORTB`, `PINB`) usando operadores lógicos (`&`, `|`, `^`, `~`) y desplazamientos (`<<`).

## Semana 2: Saltando al ESP32 y Programando para el Hardware

Dejamos la tiendita de barrio (Arduino Uno) y nos pasamos al hipermercado (**ESP32**): un chip de 32 bits, 2 núcleos, velocidad de hasta $240\text{ MHz}$, con Wi-Fi y Bluetooth.

### Lo clave de la semana:

* **Registros atómicos (`W1TS` y `W1TC`):** Para no alterar otros pines sin querer, el ESP32 usa registros especiales donde escribir un `1` enciende (`Set`) o apaga (`Clear`) el pin de una sola pasada.

* **Cuidado con los Pines de Strapping:** Pines como `GPIO0`, `GPIO2` o `GPIO12` definen cómo arranca el chip. Si conectas botones o sensores mal ahí, la placa no cargará el código o no arrancará.

* **Lógica sin bloquear (`millis()` vs. `delay()`):** Usar `delay()` es congelar el chip (como un chef que se queda mirando fijamente el horno sin cocinar nada más). Con `millis()` leemos el reloj de pared para saber si ya pasó el tiempo, dejando que el bucle `loop()` siga trabajando.

* **Pulsadores reales y el "Rebote":** Los botones físicos vibran mecánicamente al presionarlos. Aprendimos a filtrar ese ruido con un antirrebote por software y a detectar el instante exacto en que se presiona (**flanco de bajada**).

## Semana 3: Temporizadores de Hardware y Prescalers

En lugar de poner a la CPU a contar milisegundos por software, el ESP32 incluye **temporizadores físicos independientes** que cuentan pulsos de reloj en segundo plano.

### Lo clave de la semana:

* **El Prescaler (El Divisor):** El reloj interno corre rapidísimo (ej. $80\text{ MHz}$). El prescaler frena o divide esos pulsos para que podamos medir tiempos más largos y útiles (como microsegundos o milisegundos).

* **La Alarma:** Es el límite de cuentas que le fijamos al temporizador. Al llegar a ese número, el temporizador hace sonar la alarma y ejecuta la acción indicada de forma exacta.

* **Fórmula de tiempo:**
  $$\text{Resolución } (T) = \frac{1}{f_{\text{prescaler}}}$$
  $$\text{Alarma} = \text{Tiempo Deseado} \times f_{\text{prescaler}}$$

## Semana 4: Interrupciones Externas (Atención Inmediata)

¿Qué pasa si el programa está haciendo una tarea pesada y alguien presiona el botón de emergencia? Con **sondeo (*polling*)** el evento se perdería. Con las **interrupciones**, el hardware avisa a la CPU para que detenga lo que está haciendo por un microsegundo, atienda la emergencia y vuelva a su trabajo.

### Las 4 Reglas de Oro de una Interrupción (ISR):

1. **La ISR debe ser ultrarrápida:** Nada de `delay()`, nada de `Serial.print()`, ni cálculos pesados. Si te demoras mucho, el *Watchdog* (perro guardián) reiniciará el ESP32.

2. **Usa `IRAM_ATTR`:** Obliga al chip a guardar la función de la interrupción en la memoria ultrarrápida IRAM para que responda instantáneamente.

3. **El Patrón de la Bandera:** La interrupción solo debe subir una banderita (`alerta = true`), y es el bucle principal `loop()` quien se encarga de hacer el trabajo pesado con calma.

4. **Usa la palabra clave `volatile`:** Toda variable que cambie dentro de una interrupción y se lea en el `loop()` debe declararse como `volatile bool alerta = false;`. Esto le exige al compilador que consulte la memoria RAM real y no use copias guardadas en el procesador.

## Tecnologías y Herramientas Utilizadas

* **Lenguaje:** C / C++ para sistemas embebidos.
* **Microcontroladores:** ATmega328P (Arduino Uno) y ESP32 DevKit V1.
* **Entornos:** Arduino IDE 2.x, Espressif ESP-IDF y Simulador [Wokwi](https://wokwi.com/).

### Autor / Autora

* **Materia:** Sistemas Digitales
* **Realizado por:** Rosillo G. Odalis C.
