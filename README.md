# Leo - a libre pocket companion for the open stack

Leo is a free and open hardware pocket device, about the size of an old MP3 player,
that you control by voice and connect to your own services by scanning a QR code.
No screen to stare at, no vendor cloud, no account with a manufacturer.

You press and hold one button, speak, and let go. Leo plays your podcasts and music,
reads your calendar and messages, and talks to the services you already run or trust:
Nextcloud, CalDAV, CardDAV, IMAP and Matrix. Audio goes to any Bluetooth earbuds or to
the built-in speaker. A small 2.8" status display shows what is playing, battery and the
pairing QR code.

Leo is a complementary device. It is for the hours when you want to leave the phone at
home and still have your audio, your agenda and your messages with you.

Status: design stage. Nothing here is a product yet. Everything published in this
repository is a work in progress and will change.

## Why

Pocket voice devices exist today only as closed products tied to one vendor's servers.
The open stack has servers (Nextcloud, Castopod, Funkwhale), speech software
(OpenVoiceOS, Rhasspy, Home Assistant Voice) and open hardware (MNT Reform, ZSWatch,
Precursor), but no libre pocket client that joins them. Leo is that missing piece.

## Architecture

```
+------------------------------------------------------------------+
|  Leo device                                                       |
|                                                                   |
|  nRF54L15 (always on)        RK3576 (on demand)                   |
|  - push-to-talk button       - Linux, audio pipeline              |
|  - Bluetooth LE / audio      - playback: podcasts, radio, music   |
|  - battery and power         - speech satellite (Wyoming / OVOS)  |
|  - 2.8" SPI status display   - service clients via the hub        |
|  - wake of the RK3576                                             |
+------------------------------------------------------------------+
            |  Wi-Fi / USB-C                       ^  Bluetooth A2DP
            v                                     |
+------------------------+                 +--------------+
|  Leo hub (your laptop) |                 | any earbuds  |
|  - pairing by QR code  |                 +--------------+
|  - OAuth 2.1 to your   |
|    services            |
|  - MCP for tools       |
|  - Nextcloud, CalDAV,  |
|    CardDAV, IMAP,      |
|    Matrix clients      |
+------------------------+
```

Three parts, three directories:

- `hardware/` - reference design: schematics and PCB in KiCad, 3D printable enclosure,
  bill of materials, assembly guide and measurements.
- `firmware/` - code for the nRF54L15 companion (button, Bluetooth, display, power)
  and the Linux image and services for the RK3576 (audio pipeline, playback, speech
  satellite, device side of the pairing protocol).
- `hub/` - the program that runs on the user's computer: pairing by QR code,
  OAuth 2.1 to the user's services, MCP for tools, and the clients for Nextcloud,
  CalDAV, CardDAV, IMAP and Matrix.

Speech to text and text to speech are not developed here. Leo exposes a standard
satellite interface (Wyoming protocol, as used by Rhasspy, Home Assistant and
OpenVoiceOS) so that the user plugs in the free speech engines of their choice, on
the device or on their own computer, and can replace them at any time.

## Chips and main parts

| Part | Role | Why this one |
|------|------|--------------|
| Rockchip RK3576 (Luckfox Core3576 module) | Linux, audio, playback, speech satellite | Mainline Linux work in progress, free NPU driver effort funded by NLnet (Teflon), low power for its class, available in small quantities |
| Nordic nRF54L15 | Always-on companion: button, Bluetooth, display, power | Very low sleep current, Bluetooth LE Audio capable, Zephyr support |
| 2.8" IPS display, SPI | Status, QR code for pairing | Cheap, low power, no touch needed |
| Microphone array (2 MEMS) and speaker | Voice in, audio out | Push-to-talk, no always-listening microphone |
| Li-Po battery, USB-C charging | Power | Target: a full day of mixed use |

Exact part numbers, the BOM and the measured power figures will be published in
`hardware/` as they are confirmed.

## Milestones

Twelve months, six milestones. Each one ends with something published and testable.

| # | Months | Deliverable |
|---|--------|-------------|
| M1 | 1-2 | Architecture, schematics v0, first bring-up of both chips on development kits |
| M2 | 2-5 | Companion firmware: always-on button, Bluetooth to earbuds, status display, playback control, pairing by QR code |
| M3 | 4-7 | Audio path and speech satellite with interchangeable free speech engines; published latency and power measurements |
| M4 | 5-9 | Hub: pairing, OAuth 2.1, MCP, clients for Nextcloud, CalDAV, CardDAV, IMAP and Matrix |
| M5 | 8-10 | Interoperability with OpenVoiceOS and Home Assistant Voice; documentation for users and developers; upstream contributions |
| M6 | 9-12 | Hardware v1: PCB, enclosure, assembly guide, one assembled unit and its measurements |

## Licences

- Hardware (schematics, PCB, enclosure, BOM): CERN-OHL-S 2.0, see `LICENSE-HARDWARE`.
- Software and firmware: GNU AGPL-3.0-or-later, see `LICENSE`.
- Documentation: CC BY-SA 4.0, see `docs/LICENSE-DOCS`.

No patents are filed or planned on anything in this repository.

## Who

Started by Diego Torres at Next Gen Academy SL, Barcelona. Contributions are welcome
once the first schematics are published; until then, open an issue to say hello.

## Name

"Leo" is a working name. It may change.
