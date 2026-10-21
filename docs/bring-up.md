# Bring-up notes (milestone M1)

Status: not started. This file is the template that M1 fills in. No value below is a
measurement until the "measured on" column is filled.

## Boards on the bench

| Board | Role | Status |
|-------|------|--------|
| Luckfox Core3576 (RK3576) with carrier | Application processor | not acquired |
| nRF54L15 DK | Companion | not acquired |
| 2.8" SPI IPS display | Status and QR | not acquired |
| I2S audio codec breakout, 2x MEMS microphones, small speaker | Audio path | not acquired |
| Li-Po cell, USB-C charger and fuel gauge breakout | Power | not acquired |

## Steps

1. Boot the Core3576 on the vendor image; note kernel version and what works (display,
   audio, Wi-Fi, suspend).
2. Flash a Zephyr "hello" on the nRF54L15 DK; measure sleep current with the DK's
   power profiler.
3. Wire the wake line and UART between the two boards; measure button-to-microphone time.
4. Bring up the SPI display from Linux, then from Zephyr; decide who owns it.
5. Audio: capture from the microphones, play to the speaker, then A2DP and HFP to two
   earbud models.
6. Publish the power budget and this table.

## Results

| Quantity | Value | Unit | Measured on | Method |
|----------|-------|------|-------------|--------|
| Companion sleep current | not measured | uA | | |
| RK3576 suspend current | not measured | mW | | |
| RK3576 idle current | not measured | W | | |
| RK3576 resume time | not measured | ms | | |
| Button press to microphone open | not measured | ms | | |
| Button release to first spoken word (engine on device) | not measured | ms | | |
| Button release to first spoken word (engine on hub) | not measured | ms | | |
| A2DP playback, hours on battery | not measured | h | | |
