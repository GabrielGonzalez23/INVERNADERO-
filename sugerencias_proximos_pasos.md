# 🌿 Sugerencias para el Proyecto Invernadero (Próximos Pasos)

> Actualizado: Marzo 2026 | Estado del sistema: v5.4 estable con SSE, DHT11, **8 relés** (GPIO 14,27,26,25,33,32,18,13), BLE dinámico, OTA

---

## ✅ Ya implementado

| Feature | Estado |
|---------|--------|
| Lectura de temperatura/humedad (DHT11) | ✅ Funcionando |
| Sensor ThermoPro BLE (termómetro inalámbrico) | ✅ Funcionando (3 intentos al arranque) |
| Control de relés en tiempo real (< 1 segundo) vía SSE | ✅ Funcionando |
| Envío de datos a Firebase (HTTPS) | ✅ Funcionando |
| Historial de eventos (bitácora) en la PWA | ✅ Funcionando |
| OTA (actualización de código por WiFi) | ✅ Funcionando |
| Botón web para reiniciar ESP32 y buscar BLE | ✅ Funcionando |
| Panel web con gráficas y control de relés | ✅ Funcionando |

---

## 🥇 Prioridad Alta — Alto impacto, fácil de implementar

### 1. 🌱 Sensor de Humedad de Suelo (analógico)
- **Hardware:** Sensor capacitivo de humedad de suelo (~$2-5 USD) en GPIO 34 (ADC)
- **Función:** Leer el nivel de humedad del sustrato y activar automáticamente la bomba de riego cuando baje de un umbral configurable desde la web.
- **Ejemplo:** *"💧 Humedad suelo: 32% → Bomba encendida automáticamente"*
- **Beneficio:** Es el sensor más útil en un invernadero real. Riego inteligente sin desperdicio de agua.
- **RAM extra estimada:** < 1 KB

### 2. 🚨 Alertas por Temperatura/Humedad Extrema
- **Hardware:** Ninguno adicional (usa datos del DHT11 o ThermoPro)
- **Función:** Si temperatura > 35°C o < 10°C, enviar una notificación push al celular vía Web Push API (sin app store).
- **Ejemplo:** *"⚠️ ALERTA: Temperatura crítica 38°C en el invernadero"*
- **Beneficio:** Protege tus plantas si algo falla mientras no estás mirando la pantalla.
- **RAM extra estimada:** 0 KB (lógica en Firebase Functions)

### 3. 💡 Pantalla OLED Local (I2C)
- **Hardware:** SSD1306 0.96" (~$3 USD) en pines SDA=21, SCL=22
- **Función:** Mostrar temperatura, humedad y estado de relés directamente en el módulo físico, sin necesidad de internet ni celular.
- **Ejemplo:** *"T: 31.2°C | H: 48% | Relés: 2/6 ON"*
- **Beneficio:** El invernadero opera y monitorea aunque no tengas internet.
- **RAM extra estimada:** 5-10 KB

---

## 🥈 Prioridad Media — Más hardware, más valor

### 4. 💧 Sensor de Nivel de Agua (flotador)
- **Hardware:** Sensor de flotador magnético (~$2 USD) en un GPIO digital
- **Función:** Detectar cuando el tanque de agua está vacío y apagar automáticamente la bomba para protegerla de daño por funcionamiento en seco.
- **Beneficio:** Protege la bomba de riego. Evita quemarla sin agua.
- **RAM extra estimada:** < 1 KB

### 5. 🌞 Sensor de Luz (BH1750 I2C)
- **Hardware:** BH1750 (~$3 USD), comparte bus I2C con OLED (SDA=21, SCL=22)
- **Función:** Medir luz en lux. Activar luces LED de crecimiento cuando hay poca luz natural. Registrar horas de luz diaria en Firebase.
- **Beneficio:** Control de fotoperíodo automático para plantas de interior.
- **RAM extra estimada:** 2 KB

### 6. ⚗️ Sensor de CO₂ (MH-Z19B UART)
- **Hardware:** MH-Z19B (~$20 USD) en pines UART2
- **Función:** Medir concentración de CO₂. Activar ventilación si CO₂ > 1000 ppm.
- **Beneficio:** Cultura de hongos, germinación o cultivo en cuartos cerrados requiere CO₂ controlado.
- **RAM extra estimada:** 3 KB

---

## 🥉 Prioridad Baja — Mejoras de software puro

### 7. 📊 Gráficas históricas por semana/mes en la web
- **Hardware:** Ninguno
- **Función:** Ampliar el filtro de fechas en las gráficas actuales para ver tendencias de 7, 30 o 90 días.
- **Beneficio:** Ya tienes los datos en Firebase, solo falta la UI.

### 8. 📱 Notificaciones Push Web (PWA)
- **Hardware:** Ninguno
- **Función:** Usar Web Push API para enviar alertas al celular sin necesidad de app.
- **Ejemplo:** *"🌡️ Temp > 35°C"*, *"💧 Tanque vacío"*, *"🔴 Relé apagado por termostato"*

### 9. ⏰ Temporizador de Seguridad (Auto-Apagado Manual)
- **Hardware:** Ninguno
- **Función:** Al activar un relé en modo manual, ofrecer opción de "apagar en X minutos" para evitar olvidos que puedan inundar cultivos o quemar motores.

### 10. 🤖 Modo Automático por Hora del Día
- **Hardware:** Ninguno (usa el reloj NTP que ya tiene el ESP32)
- **Función:** Reglas simples como: *"Si es mediodía y temp > 30°C → activar ventilador"* o *"Si es noche → cerrar válvulas de riego"*

---

## 💡 Orden recomendado de implementación

```
1. 🌱 Humedad de suelo     → el más útil para un invernadero
2. 🚨 Alertas críticas     → protección de plantas sin estar presente
3. 💡 Pantalla OLED        → operación autónoma sin internet
4. 💧 Nivel de agua        → protege la bomba
5. 🌞 Sensor de luz        → control fotoperíodo
```

---

## 📋 Notas técnicas

- **RAM disponible en runtime:** ~99 KB (suficiente para todo lo anterior)
- **Flash disponible:** ~800 KB (suficiente para mucho código adicional)
- **Limitación conocida:** BLE + SSL simultáneo excede la RAM disponible. Solución actual: BLE solo al arranque (3 intentos), luego el botón web reinicia el ESP32.
- **Bus I2C disponible:** SDA=21, SCL=22 — puede conectar múltiples sensores (OLED + BH1750 + otros)
- **ADC disponibles:** GPIO 32, 33, 34, 35 — para sensores analógicos (humedad suelo, pH, etc.)
