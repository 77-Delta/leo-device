# Firmware

Licence: AGPL-3.0-or-later (see `../LICENSE`).

Two targets, two subdirectories (created with the first code in M1 and M2):

- `companion/` - Zephyr application for the nRF54L15: button handling, power states,
  battery gauge, SPI display (status and QR screens), wake of the RK3576, framed UART
  protocol, Bluetooth (LE now, LE Audio later), playback control.
- `app/` - Linux side for the RK3576: image recipe (vendor kernel first, mainline
  tracked), audio pipeline (PipeWire, BlueZ A2DP / HFP), playback (RSS, Funkwhale, Radio
  Browser), Wyoming satellite, device side of the pairing protocol, UART protocol peer.

Power states of the device (design):

| State | Companion | RK3576 | Expected use |
|-------|-----------|--------|--------------|
| Off | off | off | storage |
| Standby | on, display off | suspended | most of the day |
| Playing | on, display dim | on, audio only | podcasts, music |
| Listening | on, display on | on, microphone open | button held |
| Pairing | on, QR on display | on | first setup |

No speech model is part of the firmware. The satellite speaks Wyoming to an engine
chosen by the user.
