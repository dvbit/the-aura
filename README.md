# The Aura – ESPHome Fragrance Diffuser Firmware

> **Version:** 1.1  
> **Platform:** ESP32-S3-DevKitC-1 (16MB Flash, Arduino framework)  
> **License:** MIT

## Overview

**The Aura** is an ESPHome firmware for a smart fragrance diffuser built on an ESP32-S3. It features NFC cartridge detection, a MAX7219 LED matrix display, addressable LED rings for progress and cursor feedback, PWM fan control with countdown timer, and a full button-driven menu system.

## Hardware

| Component | Detail |
|---|---|
| MCU | ESP32-S3-DevKitC-1, 16MB Flash |
| Display | MAX7219 digit, 4 chips cascade (SPI) |
| NFC Reader | PN532 on I2C (400kHz) |
| Buzzer | PWM, GPIO6 |
| Fan | LEDC PWM 25kHz (GPIO13), RPM pulse counter (GPIO14) |
| Progress LED | 20× WS2812 GRB ring (GPIO7) |
| Cursor LED | 10× WS2811 GRB ring (GPIO18) |
| Buttons | 4 bistable (Power, Wind, Light, Time) + 4 momentary (Plus, Minus, OK, ESC) |

### Pin Map

| Function | GPIO |
|---|---|
| SPI CLK | 21 |
| SPI MOSI | 4 |
| Display CS | 5 |
| I2C SDA | 1 |
| I2C SCL | 2 |
| Buzzer | 6 |
| Progress LED | 7 |
| Cursor LED | 18 |
| Fan PWM | 13 |
| Fan RPM | 14 |
| Power btn | 12 |
| Wind btn | 11 |
| Light btn | 10 |
| Time btn | 9 |
| Plus btn | 48 |
| OK btn | 19 |
| Minus btn | 47 |
| ESC btn | 20 |

## Installation

1. Copy `the_aura.yaml` into your ESPHome configuration directory.
2. Create or update your `secrets.yaml` with:
   ```yaml
   wifi_ssid: "YourSSID"
   wifi_password: "YourPassword"
   api_key: "your-api-encryption-key"
   ota_password: "your-ota-password"
   ap_password: "fallback-ap-password"
   ```
3. Compile and flash:
   ```bash
   esphome run the_aura.yaml
   ```

## Usage

### Power On/Off
Flip the **Power** toggle switch. On power-on, the cursor LED ring animates clockwise, then the device enters ON state with saved settings restored.

### Fan Speed (Wind)
1. Flip the **Wind** toggle → display shows current speed (%).
2. Press **PLUS/MINUS** to adjust (0–100%, step 1%).
3. Press **OK** to confirm or **ESC** to cancel.

### Timer (Time)
1. Flip the **Time** toggle → display shows remaining time (MM:SS).
2. Press **PLUS/MINUS** to adjust (0–60 min, step 1 min).
   - `0` = permanent (fan stays on indefinitely).
   - `1-60` = countdown; fan stops when time expires.
3. Press **OK** to confirm or **ESC** to cancel.

### Light Menu
1. Flip the **Light** toggle → display shows submenu name.
2. Press **PLUS/MINUS** to navigate: **FX** → **Brt** → **Color**.
3. Press **OK** to enter submenu:
   - **FX**: cycle through 12 effects (including custom "Progresso").
   - **Brt**: adjust intensity 1–10.
   - **Color**: adjust HSV hue (10° steps) with real-time preview.
4. Press **OK** to confirm selection, **ESC** to cancel.

### NFC Cartridge
Insert an NFC cartridge with an NDEF text record containing the fragrance name. The name appears in the idle display cycle. Remove the cartridge to clear.

### Idle Display
When no editing is active, the display cycles every 2 seconds:
1. Fragrance name (scrolling if long)
2. Time remaining (MM:SS) or "PERM" if permanent
3. Fan speed (%)

## Available Light Effects

| # | Effect |
|---|---|
| 0 | Progresso (custom progress bar) |
| 1 | Random |
| 2 | Pulse |
| 3 | Strobe |
| 4 | Flicker |
| 5 | Addressable Rainbow |
| 6 | Addressable Color Wipe |
| 7 | Addressable Scan |
| 8 | Addressable Twinkle |
| 9 | Addressable Random Twinkle |
| 10 | Addressable Fireworks |
| 11 | Addressable Flicker |

## Configurable Parameters

All timing and behavior parameters are defined in the `substitutions:` section at the top of the YAML for easy tuning:

| Parameter | Default | Description |
|---|---|---|
| `beep_ms` | 100 | Buzzer beep duration (ms) |
| `blink_on_ms` | 20 | Display blink ON phase (ms) |
| `blink_off_ms` | 200 | Display blink OFF phase (ms) |
| `anim_step_ms` | 100 | Power animation delay per LED (ms) |
| `hue_step` | 10 | HSV hue increment per press (°) |
| `idle_interval` | 2s | Idle display cycle interval |
| `pin_progress_numled` | 20 | Number of Progress ring LEDs |
| `pin_cursor_numled` | 10 | Number of Cursor ring LEDs |

## Specification

See [SPEC.md](SPEC.md) for the full functional requirement document.

---

# The Aura – Firmware ESPHome per Diffusore di Fragranze (IT)

## Panoramica

**The Aura** è un firmware ESPHome per un diffusore di fragranze smart basato su ESP32-S3. Include rilevamento cartucce NFC, display a matrice LED MAX7219, anelli LED addressable per feedback di progresso e cursore, controllo ventola PWM con timer countdown e un sistema completo di menu a pulsanti.

## Installazione

1. Copiare `the_aura.yaml` nella directory di configurazione ESPHome.
2. Creare o aggiornare `secrets.yaml` con le credenziali WiFi, chiave API e password OTA.
3. Compilare e flashare: `esphome run the_aura.yaml`

## Utilizzo

### Accensione/Spegnimento
Attivare l'interruttore **Power**. All'accensione, l'anello LED cursore anima in senso orario, poi il dispositivo entra in stato ON con i valori salvati ripristinati.

### Velocità Ventola (Wind)
1. Attivare l'interruttore **Wind** → il display mostra la velocità corrente (%).
2. Premere **PLUS/MINUS** per regolare (0–100%, passo 1%).
3. Premere **OK** per confermare o **ESC** per annullare.

### Timer (Time)
1. Attivare l'interruttore **Time** → il display mostra il tempo rimanente (MM:SS).
2. Premere **PLUS/MINUS** per regolare (0–60 min, passo 1 min).
   - `0` = permanente (ventola sempre accesa).
   - `1-60` = countdown attivo; la ventola si ferma allo scadere.
3. Premere **OK** per confermare o **ESC** per annullare.

### Menu Luce
1. Attivare l'interruttore **Light** → il display mostra il nome del sottomenu.
2. Premere **PLUS/MINUS** per navigare: **FX** → **Brt** → **Color**.
3. Premere **OK** per entrare nel sottomenu:
   - **FX**: scorrere tra 12 effetti (incluso "Progresso" custom).
   - **Brt**: regolare intensità 1–10.
   - **Color**: regolare tonalità HSV (passi di 10°) con anteprima in tempo reale.
4. Premere **OK** per confermare, **ESC** per annullare.

### Cartuccia NFC
Inserire una cartuccia NFC con un record di testo NDEF contenente il nome della fragranza. Il nome appare nel ciclo display idle. Rimuovere la cartuccia per cancellare.

### Display Idle
Quando nessuna modifica è attiva, il display cicla ogni 2 secondi:
1. Nome fragranza (scrolling se lungo)
2. Tempo rimanente (MM:SS) o "PERM" se permanente
3. Velocità ventola (%)

## Parametri Configurabili

Tutti i parametri di timing e comportamento sono definiti nella sezione `substitutions:` in cima al file YAML per una facile regolazione. Vedere la tabella nella sezione inglese sopra.

## Specifica

Vedere [SPEC.md](SPEC.md) per il documento completo dei requisiti funzionali.
