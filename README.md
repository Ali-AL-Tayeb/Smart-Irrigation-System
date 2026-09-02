# Smart Irrigation System — Live Monitoring Dashboard

A web-based live monitoring dashboard for a three-part smart irrigation and environmental control system.

The dashboard connects directly to the MQTT broker from the browser and displays telemetry from the three ESP32 subsystems in real time.

## Live Dashboard

Scan the QR code below to open the project dashboard:

![Smart Irrigation Dashboard QR Code](qr-code.png)

> **Note:** The QR code is provided as an image so it can be committed to the same GitHub repository as this README.

## System Overview

The dashboard monitors three ESP32-based subsystems:

### System 1 — Dam & Irrigation Pump

Monitors the irrigation controller and water/soil state:

- Channel sensor — `WET` / `DRY`
- Soil sensor — `WET` / `DRY`
- Dam / stepper position — `OPEN` / `CLOSED`
- Irrigation pump — `ON` / `OFF`
- Automatic mode — automatic / manual

### System 2 — Agriculture & Environment

Monitors environmental conditions and agricultural sensors:

- Temperature — DHT22, °C
- Humidity — DHT22, %
- Light — LDR raw ADC
- Soil moisture — raw ADC
- Rain sensor — raw ADC
- Water level — raw ADC
- Ventilation fan — `ON` / `OFF`
- Humidifier — `ON` / `OFF`
- Automatic mode — automatic / manual

### System 3 — Solar Panel Cooling

Monitors the solar panel cooling subsystem:

- Panel temperature — DS18B20, °C
- Cooling pump — `ON` / `OFF`

## MQTT Architecture

The browser subscribes to the same MQTT topics used by the ESP32 systems.

**Browser → HiveMQ**

- Secure WebSocket connection
- WebSocket TLS port: `8884`

**ESP32 → HiveMQ**

- MQTT over TLS
- MQTT TLS port: `8883`

The dashboard is message-driven, so readings update when MQTT messages arrive without requiring a page reload.

## MQTT Topics

### System 1

```text
system1/sensors/channel
system1/sensors/soil
system1/status/pump
system1/status/dam
system1/status/automode
```

### System 2

```text
system2/sensors/temp
system2/sensors/hum
system2/sensors/ldr
system2/sensors/soil
system2/sensors/rain
system2/sensors/water
system2/status/fan
system2/status/humidifier
system2/status/automode
```

### System 3

```text
system3/sensors/solar_temp
system3/status/cooling_pump
```

## Historical Data

The dashboard includes a **View History** function for each subsystem.

Historical readings are stored locally in the browser using **IndexedDB**. The dashboard can show:

- Last 24 hours
- Last 7 days
- Last 30 days
- All saved data

The history view also supports CSV export.

**Important:** because the history is stored locally in the browser, the data is not automatically stored on GitHub Pages or in a central database. Each browser/device maintains its own saved history.

## Timestamping

Each reading is saved when the browser receives the MQTT message.

The dashboard uses the browser's current time for its local history timestamp rather than an ESP32-provided timestamp.

Therefore:

- The MQTT message contains the sensor value.
- The browser receives the message.
- The browser records the reception time in IndexedDB.

## Deployment

This project is suitable for static hosting such as **GitHub Pages** because the dashboard runs entirely in the browser.

Typical deployment structure:

```text
/
├── index.html
├── README.md
└── qr-code.png
```

Rename the supplied HTML file to `index.html` when publishing the dashboard as the repository's main GitHub Pages entry point.

## Technologies

- HTML5
- JavaScript
- Tailwind CSS
- Font Awesome
- MQTT.js
- HiveMQ Cloud
- IndexedDB
- GitHub Pages

## Security Note

The current dashboard code contains MQTT connection credentials in client-side JavaScript. Because GitHub Pages serves JavaScript publicly, those credentials should be treated as exposed and should **not** be used for a production deployment.

For a safer deployment, use a restricted MQTT account with the minimum required permissions, or place authenticated MQTT access behind a backend/service that does not expose broker credentials to every visitor.

## Project Status

The dashboard is designed for live observation of the three ESP32 subsystems and local historical review of incoming telemetry.
