# REQUISITO FUNZIONALE – THE AURA
**Firmware ESPHome**
**Versione 1.1**

---

## 1. HARDWARE

| Componente | Dettaglio |
|---|---|
| Microcontrollore | ESP32-S3-DevKitC-1, Flash 16MB, framework Arduino |
| Display | MAX7219digit, 4 chip in cascata, SPI (CLK GPIO21, MOSI GPIO4, CS GPIO5), font Silkscreen 8px, intensità default 5 |
| Lettore NFC | PN532 su I2C (SDA GPIO1, SCL GPIO2, frequenza 400kHz) |
| Buzzer | PWM, GPIO6, frequenza configurabile per tasto |
| Ventola | LEDC PWM 25kHz, pin dati GPIO13; lettura RPM via pulse_counter su GPIO14, aggiornamento ogni 3s |
| Progress Led | NeoPixelBus WS2812 GRB, GPIO7, **20 LED**, circolare intorno alla capsula |
| Cursor Led | NeoPixelBus WS2811 GRB, GPIO18, **7 LED**, circolare lato destro |
| Tasto Power | Bistabile, GPIO12 |
| Tasto Wind | Bistabile, GPIO11 |
| Tasto Light | Bistabile, GPIO10 |
| Tasto Time | Bistabile, GPIO9 |
| Tasto Plus | Momentaneo, GPIO48, posizione 0° (ore 12) |
| Tasto OK | Momentaneo, GPIO19, posizione 90° (ore 3) |
| Tasto Minus | Momentaneo, GPIO47, posizione 180° (ore 6) |
| Tasto ESC | Momentaneo, GPIO20, posizione 270° (ore 9) |

---

## 2. TASTI CURSORE – FEEDBACK VISIVO E SONORO

### 2.1 Cursor Led – feedback visivo
Ad ogni pressione di un tasto cursore, il Cursor Led mostra una serie di led che si accendono progressivamente a partire dai led già accesi, con colore dipendente dal tasto:

| Tasto | Colore effetto |
|---|---|
| PLUS | Bianco |
| MINUS | Bianco |
| OK | Verde |
| ESC | Rosso |

Comportamento: 3 LED si accendono in sequenza dalla posizione corrente (LED 0 = ore 12), poi si spengono. La posizione avanza di 3 per la pressione successiva.

### 2.2 Buzzer – feedback sonoro
Ad ogni pressione di qualsiasi tasto viene emesso un beep di durata fissa (100ms). La frequenza è differenziata:

| Tasto/Gruppo | Nota RTTTL |
|---|---|
| PLUS e MINUS | C6 |
| OK | E6 |
| ESC | A5 |
| Power | G5 |
| Wind | D6 |
| Time | F6 |
| Light | B5 |

---

## 3. STATO GENERALE

- Il dispositivo ha due stati: **ON** e **OFF**, controllati dal tasto Power
- Tutti i valori (velocità ventola, tempo, effetto, intensità, colore) sono **persistenti** (restore_value: yes)
- In stato **OFF**: display spento, Progress Led spento, Cursor Led spento. I tasti Wind, Time, Light e i tasti cursore non hanno alcun effetto

---

## 4. TASTO POWER (bistabile)

### 4.1 Accensione (OFF → ON)
1. Display mostra brevemente "ON"
2. Cursor Led (7 LED): i led si accendono uno per uno in senso **orario** (100ms per LED)
3. Una volta completato il cerchio, il Cursor Led si spegne completamente
4. Il dispositivo entra in stato ON con valori salvati ripristinati

### 4.2 Spegnimento (ON → OFF)
1. Display mostra brevemente "OFF"
2. Ventola si spegne
3. Progress Led si spegne
4. Cursor Led (7 LED): si accende tutto, poi i led si spengono uno per uno in senso **antiorario** (100ms per LED)
5. Display si spegne
6. Il dispositivo entra in stato OFF

---

## 5. DISPLAY IDLE

Quando il dispositivo è in stato ON e nessun tasto è in fase di modifica, il display cicla automaticamente tra le seguenti schermate con intervallo di **2 secondi**:

1. **Nome fragranza**: letto via NFC. Se nessuna cartuccia: "No cart". Scrolling orizzontale se il testo è troppo lungo
2. **Tempo rimanente**: formato MM:SS. "PERM" se tempo = 0 (permanente)
3. **Velocità ventilazione**: velocità corrente in %

---

## 6. TASTO WIND (bistabile)

### 6.1 Attivazione
Alla pressione del tasto Wind il display mostra la velocità corrente della ventola.

### 6.2 Modifica velocità
- **PLUS**: +1%
- **MINUS**: -1%
- Range: **0% – 100%**
- **ESC**: esce senza modifiche, display lampeggia (3x, 20ms on/200ms off)
- **OK**: conferma, display lampeggia, applica velocità

---

## 7. TASTO TIME (bistabile)

### 7.1 Attivazione
Alla pressione del tasto Time il display mostra il tempo rimanente corrente.

### 7.2 Modifica tempo
- **PLUS**: +1 minuto
- **MINUS**: -1 minuto
- Range: **0 – 60 minuti** (0 = permanente)
- **ESC**: esce senza modifiche + blink
- **OK**: conferma + blink, avvia countdown

### 7.3 Comportamento countdown
- Il tempo scende di 1 secondo ogni secondo (display MM:SS)
- Allo scadere: ventola si spegne, dispositivo rimane ON, Progress LED si spegne
- Se tempo = 0: ventola accesa indefinitamente

---

## 8. PROGRESS LED

20 LED NeoPixelBus WS2812 GRB su GPIO7, circolari.

### 8.1 Effetto "Progresso" (custom)
- 20 led accesi = tempo pieno
- Led si spengono in senso antiorario proporzionalmente: `led_accesi = round(rimanente / impostato * 20)`
- Quando tempo = 0 (permanente): effetto **pulse**
- Quando ventola = 0%: Progress LED **spento**

### 8.2 Effetti disponibili
Progresso, Random, Pulse, Strobe, Flicker, Addressable Rainbow, Addressable Color Wipe, Addressable Scan, Addressable Twinkle, Addressable Random Twinkle, Addressable Fireworks, Addressable Flicker

### 8.3 Intensità
Livelli discreti 1–10

### 8.4 Colore
Spettro continuo HSV, step 10° per pressione, preview in tempo reale durante modifica

---

## 9. TASTO LIGHT (bistabile)

### 9.1 Menu con 3 sottomenu
1. **FX** (Effetto)
2. **Brt** (Intensità)
3. **Color** (Colore)

### 9.2 Navigazione menu
PLUS/MINUS ciclano, OK entra, ESC esce

### 9.3-9.5 Sottomenu
Ciascun sottomenu usa PLUS/MINUS per modificare, OK per confermare, ESC per annullare. Il sottomenu Colore ha preview in tempo reale sul Progress LED.

---

## 10. NFC

PN532 su I2C a 400kHz. Lettura NDEF text record per nome fragranza. "No cart" se nessuna cartuccia inserita.

---

## 11. CURSOR LED

7 LED NeoPixelBus WS2811 GRB su GPIO18. Esclusivamente funzionale, non configurabile.

| Evento | Comportamento |
|---|---|
| Power ON | 7 led clockwise da LED 0, poi spento (100ms/LED) |
| Power OFF | 7 led tutti accesi, poi off anti-clockwise da LED 0 (100ms/LED) |
| PLUS/MINUS | 3 led progressivi da posizione corrente, bianco |
| OK | 3 led progressivi, verde |
| ESC | 3 led progressivi, rosso |
