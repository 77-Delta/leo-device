# Open Pocket Companion (working name: Leo)

A libre pocket device, about the size of an old MP3 player, that you control by voice
and connect to your own services by scanning a QR code. No screen to stare at, no
vendor cloud, no account with a manufacturer.

You press and hold one button, speak, and let go. The device plays your podcasts and
music, reads your calendar and messages, and talks to the services you already run or
trust: Nextcloud, CalDAV, CardDAV, IMAP and Matrix. Audio goes to any Bluetooth earbuds
or to the built-in speaker. A small 2.8" status display shows what is playing, the
battery level and the pairing QR code.

It is a complementary device, for the hours when you want to leave the phone at home
and still have your audio, your agenda and your messages with you.

Status: design stage, pre-funding. Nothing here is a product. Everything in this
repository is work in progress and will change. "Leo" is a working name and may change;
the repository name will follow.

## Why

Pocket voice devices exist today only as closed products tied to one vendor's servers.
The open stack has servers (Nextcloud, Castopod, Funkwhale), speech software
(OpenVoiceOS, Rhasspy, Home Assistant Voice) and open hardware (MNT Reform, ZSWatch,
Precursor), but no libre pocket client that joins them. This project is that missing
piece. See `docs/comparison.md` for the detailed comparison with other efforts.

## Architecture in one picture

```
+------------------------------------------------------------------+
|  Device                                                           |
|                                                                   |
|  nRF54L15 (always on)        RK3576 (on demand)                   |
|  - push-to-talk button       - Linux, audio pipeline              |
|  - Bluetooth LE / audio      - playback: podcasts, radio, music   |
|  - battery and power         - speech satellite (Wyoming / OVOS)  |
|  - 2.8" SPI status display   - service clients via the hub        |
|  - wake of the RK3576                                             |
+------------------------------------------------------------------+
            |  Wi-Fi / USB-C                       ^  Bluetooth A2DP / HFP
            v                                     |
+------------------------+                 +--------------+
|  Hub (your computer)   |                 | any earbuds  |
|  - pairing by QR code  |                 +--------------+
|  - OAuth 2.1 to your   |
|    services            |
|  - MCP for tools       |
|  - Nextcloud, CalDAV,  |
|    CardDAV, IMAP,      |
|    Matrix clients      |
+------------------------+
```

Full block diagram, data flows and protocol choices: `docs/architecture.md`.

Three parts, three directories:

- `hardware/` - reference design: schematics and PCB in KiCad, 3D printable enclosure,
  bill of materials, assembly guide and measurements. Licence CERN-OHL-S 2.0.
- `firmware/` - code for the nRF54L15 companion (button, Bluetooth, display, power)
  and the Linux image and services for the RK3576 (audio pipeline, playback, speech
  satellite, device side of the pairing protocol). Licence AGPL-3.0-or-later.
- `hub/` - the program that runs on the user's computer: pairing by QR code,
  OAuth 2.1 to the user's services, MCP for tools, and the clients for Nextcloud,
  CalDAV, CardDAV, IMAP and Matrix. Licence AGPL-3.0-or-later.

Speech to text and text to speech are not developed here. The device exposes a
standard satellite interface (Wyoming protocol, as used by Rhasspy, Home Assistant and
OpenVoiceOS) so that the user plugs in the free speech engines of their choice, on the
device or on their own computer, and can replace them at any time. The project develops
or integrates no AI models.

## Chips and main parts

| Part | Role | Why this one |
|------|------|--------------|
| Rockchip RK3576 (Luckfox Core3576 module) | Linux, audio, playback, speech satellite | Mainline Linux work in progress, free NPU driver effort (Teflon), low power for its class, available in small quantities |
| Nordic nRF54L15 | Always-on companion: button, Bluetooth, display, power | Very low sleep current, Bluetooth LE Audio capable, Zephyr support |
| 2.8" IPS display, SPI | Status, QR code for pairing | Cheap, low power, no touch needed |
| Two MEMS microphones and a speaker | Voice in, audio out | Push-to-talk, no always-listening microphone |
| Li-Po battery, USB-C charging | Power | Target: a full day of mixed use |

Candidate development kits, their prices and the bill of materials: `hardware/README.md`.
Exact part numbers and measured power figures are published there as they are
confirmed. No figure is published before it is measured.

## Plan: twelve months, six milestones

Each milestone ends with something published and testable. Effort is given in hours
because the project is planned for an NLnet (Restack) application at 60 EUR per hour.

| # | Months | Deliverable | Hours | Cost (EUR) |
|---|--------|-------------|-------|------------|
| M1 | 1-2 | Architecture, schematics v0, bring-up of both chips on development kits; block diagram, power budget and bring-up notes published | 95 | 5,700 + 2,000 hardware |
| M2 | 2-5 | Always-on companion firmware on the nRF54L15 (Zephyr): push-to-talk, wake of the RK3576, status and QR screens, Bluetooth to earbuds, playback of RSS podcasts and Funkwhale / Radio Browser streams; measured idle current and wake time | 110 | 6,600 |
| M3 | 4-7 | Audio path and voice satellite: microphone and speaker drivers, Bluetooth A2DP/HFP on Linux, Wyoming / OpenVoiceOS satellite; published latency and consumption measurements | 119 | 7,140 |
| M4 | 6-9 | Hub on the user's computer: OAuth 2.1 pairing by QR, MCP server, clients for Nextcloud, CalDAV, CardDAV, IMAP and Matrix, command grammar; test suite and threat model | 130 | 7,800 |
| M5 | 9-11 | Interoperability and upstream work: OpenVoiceOS and Home Assistant Wyoming satellites, protocol documentation, user and developer guides (CC BY-SA 4.0) | 60 | 3,600 |
| M6 | 10-12 | Hardware v1: interconnect PCB in KiCad, enclosure, assembly guide, one assembled unit, final measurements | 119 | 7,140 + 1,000 hardware |

Totals: 633 hours x 60 EUR = 37,980 EUR; prototype hardware 3,000 EUR (one development
unit); total 40,980 EUR. No overhead, no certification, no production batch, no
marketing. Documentation is included in the hours of each milestone. Team: Diego Torres
(550 h, lead, firmware of the companion, hub, protocols, documentation) and one hardware
integration engineer (83 h, board image, display and audio drivers, PCB, enclosure,
measurements).

## Licences

- Hardware (schematics, PCB, enclosure, BOM): CERN-OHL-S 2.0, see `LICENSE-HARDWARE`.
- Software and firmware: GNU AGPL-3.0-or-later, see `LICENSE`.
- Documentation: CC BY-SA 4.0, see `docs/LICENSE-DOCS`.

No patents are filed or planned on anything in this repository.

## Who

Started by Diego Torres at Next Gen Academy SL (Palau-solità i Plegamans, Barcelona).
Contributions are welcome once the first schematics are published; until then, open an
issue to say hello. See `CONTRIBUTING.md`.
