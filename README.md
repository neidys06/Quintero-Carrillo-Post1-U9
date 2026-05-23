# Quintero-Carrillo-Post1-U9
Laboratorio Post-Contenido 1 Unidad 9 -  Entrada y Salida Avanzados - Arquitectura de Computadores

## Objetivo
Implementar programas en ensamblador x86 (NASM + DOSBox) que acceden
directamente a puertos de E/S mediante las instrucciones IN y OUT,
aplicando polling para sincronización con dispositivos.

## Prerrequisitos
- DOSBox 0.74 o superior
- NASM 2.x (nasm.exe en la misma carpeta o en el PATH de DOSBox)
- Directorio de trabajo: `C:\U9P1\`

## Programas

## Programa 1: TECL.ASM — Lectura del Puerto de Estado del Teclado

### Descripción
Lee el registro de estado del controlador 8042 (puerto 64h) mediante 
polling. Espera hasta que el bit OBF (Output Buffer Full, bit 0) sea 1, 
luego lee el scancode del Data Port (puerto 60h) y lo muestra en 
hexadecimal en pantalla.

### Puertos utilizados
- `64h` — Status Register del 8042 (lectura del bit OBF)
- `60h` — Data Port del 8042 (lectura del scancode)
  
**Compilar:**
nasm -f bin TECL.ASM -o TECL.COM
**Ejecutar:**
TECL
### Comportamiento esperado
Al presionar una tecla (por ejemplo **A**), el programa muestra su 
scancode en hexadecimal (`1E`) y termina.

### Checkpoint 1 ✅
El programa compila sin errores y muestra correctamente el scancode 
de la tecla presionada.

![Checkpoint 1](checkpoint1.png)

---

## Programa 2: POLL_T.ASM — Polling con Timeout

### Descripción
Implementa un bucle de polling con contador de reintentos (registro CX). 
Si el dispositivo no responde antes de agotar `MAX_RETRY` intentos, 
muestra un mensaje de timeout y termina. Evita el bloqueo indefinido 
del programa si el dispositivo falla.

### Puertos utilizados
- `64h` — Status Register del 8042 (verificación del bit OBF)
  
**Compilar:**
nasm -f bin POLL_T.ASM -o POLL_T.COM
**Ejecutar:**
POLL_T
### Comportamiento esperado
Con `MAX_RETRY EQU 0005h` (valor pequeño de prueba), el programa muestra 
inmediatamente el mensaje de timeout sin necesidad de presionar ninguna 
tecla. Con `MAX_RETRY EQU 0FFFFh` espera una pulsación real.

### Checkpoint 2 ✅
El programa muestra "Timeout: sin respuesta del dispositivo" cuando 
MAX_RETRY es pequeño y no se presiona ninguna tecla.

![Checkpoint 2](checkpoint2.png)

---

## Programa 3: LPT1.ASM — Escritura al Puerto Paralelo LPT1

### Descripción
Implementa el protocolo Centronics completo para enviar un carácter al 
puerto paralelo LPT1. El protocolo consiste en:
1. Esperar que BUSY# (bit 7 del registro de estado) esté en alto
2. Colocar el dato (carácter 'A', 0x41) en el Data Register (0x378)
3. Activar la señal STROBE (bit 0 del Control Register en bajo)
4. Esperar retardo mínimo (~1µs)
5. Desactivar STROBE

### Puertos utilizados
- `0x378` — Data Register (escritura del dato)
- `0x379` — Status Register (verificación de BUSY#)
- `0x37A` — Control Register (generación del pulso STROBE)

**Compilar:**
nasm -f bin LPT1.ASM -o LPT1.COM
**Ejecutar:**
LPT1

### Comportamiento observado en DOSBox
El bit BUSY# (bit 7 del puerto 0x379) aparece en alto por defecto en 
DOSBox, indicando que la "impresora" está lista. Por tanto, el bucle de 
espera termina inmediatamente. El programa envía el carácter 'A' (0x41) 
al bus de datos, genera el pulso STROBE mediante los registros de control, 
y termina sin error ni bloqueo. No hay dispositivo físico conectado, pero 
el acceso directo a los puertos no genera excepción en el entorno emulado.

### Checkpoint 3 ✅
El programa compila y se ejecuta sin errores de acceso a puerto en DOSBox.

![Checkpoint 3](checkpoint3.png)

---
