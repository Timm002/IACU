---
marp: true
---

# Arquitectura IoT de Pulseras LED en el Concierto de Shakira

Tomás Cano Santa Catalina
![bg right](https://images.unsplash.com/photo-1520242739010-44e95bde329e?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D)

---

# 1. Introducción y contexto

* **Gran despliegue de dispositivos IoT:** 60.000 dispositivos en una superficie grande actuando en milisegundos
* **Directrices para el dispositivo:**
	* Minimización de costos
	* Alimentación para la duración de la actuación
	* Sin configuración por parte del usuario
---
# 2. Arquitectura de la solución

| Capa                  | Description                                                                             |
| --------------------- | --------------------------------------------------------------------------------------- |
| Capa de Aplicación    | Diseño del show de luces, cues por canción y sincronización por *timecode* (SMPTE/MIDI) |
| Capa de Procesamiento | Consola de iluminación profesional (grandMA)                                            |
| Capa de Transporte    | Ethernet (sACN/Art-Net), DMX512, Transmisores direccionales (DMX -> IR)                 |
| Capa de Percepción    | Pulseras que recibe el público                                                          |

---

# 3. Capa de Percepción

* Hardware de la pulsera:
	* Microcontrolador: de 8 bits, sencillo
	* Memoria EEPROM
	* Receptor Infrarrojo
	* Actuadores: 2 LEDs RGB
	* Alimentación: 2 pilas CR1632

* Firmware:
	* Reposo de bajo consumo, solo activo con la recepción IR
	* Sin confirmación de recepción, espera a la siguiente trama

---
# 4. Capa de Transporte
## 4.1. IR

* Propagación en < 1 µs, indiferente al número de pulseras en un mismo espacio
* División de emisores para reducir puntos ciegos y dividir el estadio por zonas
* Emisión repetida de tramas para recuperación inmediata ante pérdidas
* Filtro de ruido infrarrojo mediante frecuencia portadora
## 4.2. DMX512

* Estándar en la industria del espectáculo, facilitando la compatibilidad a la mayoría de dispositivos
* 512 canales transmitidos 44 veces / segundo
---
## 4.3. Ethernet: Art-Net y sACN

Para reducir cientos de cables saliendo desde la consola hasta cada torre de iluminación, se encapsula DMX en paquetes UDP, viajando hasta nodos donde se retransmiten en DMX por RS-485.

---
# 5. Capas de Procesamiento y Aplicación

Consiste en una consola de iluminación controlada por un operario, siguiendo el show programado según el timecode, pero que permite modificar el tiempo en el que se ejecutan los eventos según las necesidades del espectáculo.

---
# 6. Comparación de posibles tecnologías 

| Criterio                     | IR                                       | BLE                    | RF sub-GHz                                            | NFC           |
| ---------------------------- | ---------------------------------------- | ---------------------- | ----------------------------------------------------- | ------------- |
| Dirección de la comunicación | Bajada                                   | Bidireccional posible  | Bajada                                                | Bidireccional |
| Escalabilidad                | Excelente                                | Limitada               | Buena                                                 | No aplica     |
| Alcance                      | 10-100m (con linea de visión)            | 10-50m                 | Cientos de metros, atraviesa obstáculos               | Centímetros   |
| Efectos espaciales           | Nativos                                  | Difíciles              | Difíciles                                             | No aplica     |

---

| Criterio       | IR                                       | BLE                    | RF sub-GHz                                            | NFC                      |
| -------------- | ---------------------------------------- | ---------------------- | ----------------------------------------------------- | ------------------------ |
| Interferencias | Luz solar y focos (mitigados por filtro) | Banda 2.4 GHz saturada | Espectro compartido por equipos inalámbricos del show | Mínimas                  |
| Coste          | Muy bajo                                 | Bajo-Medio             | Bajo-Medio                                            | Muy bajo                 |
| Consumo        | Muy bajo                                 | Bajo                   | Bajo-Medio                                            | Nulo                     |
| Uso típico     | Espectáculo de luces                     | Eventos pequeños       | Espectáculos de luces                                 | Pagos, control de acceso | 

**Como podemos ver, el IR gana en escalabilidad, coste, consumo y efectos espaciales**

---
# 7. Diseño IoT

1. Inteligencia centralizada, dispositivos baratos: evita el Edge Computing para dispositivos tan numerosos
2. Escalabilidad: el sistema no sufre colisiones ni reajustes añadiendo más dispositivos
3. Lazo abierto y fiabilidad por reemisión
4. Abstracción del usuario final de todo el proceso
