# Leo: a voice-first pocket computer (working name)

A libre pocket device, about the size of an old MP3 player, that you operate by voice.
It runs its own system with a small AI model on the device, so the basics work offline,
at zero cost per request, and it can hand the hard questions to the cloud model you
choose. Plug it into any screen with an HDMI cable when you want to see what it made:
a laptop without a screen, a screen without a laptop.

You press and hold one button, speak, and let go. Leo plays your podcasts, music and
radio, reads your calendar, mail and messages, and talks to the services you already
use or run yourself. Audio goes to any Bluetooth earbuds or to the built-in speaker.
Setup and integrations happen once from your computer, by scanning a QR code; after
that the device needs neither a phone nor an account with us.

It is for the hours when you want to leave the phone at home and still have your
audio, your agenda and an assistant with you, and for the desk where you want to see on
a screen what you asked for by voice.

Status: design stage. No board has been bought or measured yet. Prototypes on
off-the-shelf boards are the next step. Nothing here is a product. Everything in this repository is work in progress and will change.
"Leo" is a working name and may change; the repository name will follow.

## Why

Pocket voice devices exist today only as closed products tied to one vendor's servers
and one vendor's AI. The open stack has servers (Nextcloud, Castopod, Funkwhale),
speech software (OpenVoiceOS, Rhasspy, Home Assistant Voice, Whisper, Piper), small
open language models that run on a phone-class NPU, and open hardware (MNT Reform,
ZSWatch, Precursor), but no libre pocket device that joins them. This project is that
missing piece. See `docs/comparison.md` for the comparison with other efforts.

Three design decisions set it apart:

1. **Hybrid AI, local by default.** A small speech and language stack runs on the
   device (speech to text, a sub-1B language model, text to speech). Offline use and
   the everyday commands cost nothing and send nothing. When a request needs more, the
   device routes it to the cloud model the user configured: Claude, an open model on
   the user's own server, or any other. The device is AI-agnostic; no model is tied to
   it.
2. **Screen optional, by cable.** No screen of its own to work on: only a small
   status display for what is playing, the battery and the pairing code. Plug an HDMI
   cable, with the device powered by the cable, and the same system opens a viewing
   session: one full-screen window that shows what Leo generated (a web page, a
   document, the transcript, what is playing), still driven by voice, with an optional
   keyboard. Video over USB-C is an alternative that the first work package
   evaluates. A conventional desktop for keyboard and mouse is a possible later step,
   outside the current plan, and not decided.
3. **Own system, open.** Leo runs its own Linux-based system image, built from open
   components, with the voice agent as the primary interface and the viewing session
   as the secondary one. Hardware, firmware, system and hub are published under free
   licences.

## Architecture in one picture

```
+-------------------------------------------------------------------------+
|  Device                                                                  |
|                                                                          |
|  nRF54L15 (always on)          RK3576 with NPU (on demand)               |
|  - push-to-talk button         - Leo OS (Linux), audio pipeline          |
|  - Bluetooth LE / audio        - local models: STT, small LLM, TTS       |
|  - battery and power           - router: local first, cloud on demand    |
|  - status display              - playback: podcasts, radio, music        |
|  - wake of the SoC             - agent runtime with MCP tools            |
|                                - HDMI out (USB-C video to evaluate)      |
+-------------------------------------------------------------------------+
     |  Wi-Fi / USB-C            ^  Bluetooth A2DP / HFP        |  HDMI
     v                           |                              v
+------------------------+   +--------------+          +------------------+
|  Hub (your computer)   |   | any earbuds  |          | any screen, TV   |
|  - pairing by QR code  |   +--------------+          | or monitor       |
|  - OAuth 2.1 to your   |                             +------------------+
|    services            |          +------------------------------------+
|  - MCP tool catalogue  |          | Cloud model of your choice         |
|  - cloud model keys    |  <-----> | (Claude, your own server, others)  |
+------------------------+          +------------------------------------+
```

Full block diagram, data flows, model placement and protocol choices:
`docs/architecture.md`.

Four parts, four directories:

- `hardware/` - reference design: schematics and PCB in KiCad, 3D printable enclosure,
  bill of materials, assembly guide and measurements. Licence CERN-OHL-S 2.0.
- `firmware/` - code for the nRF54L15 companion (button, Bluetooth, display, power,
  wake of the SoC). Licence AGPL-3.0-or-later.
- `os/` - Leo OS: the Linux system image for the SoC, the audio pipeline, the local
  model runtime and router, the agent runtime, the playback services and the viewing
  session for the external screen. Licence AGPL-3.0-or-later. (Directory to be created
  with the first image; today the OS notes live in `docs/architecture.md`.)
- `hub/` - the program that runs on the user's computer: pairing by QR code, OAuth 2.1
  to the user's services, the MCP tool catalogue and the keys for cloud models.
  Licence AGPL-3.0-or-later.

Speech and language models are integrated, not trained here. The device exposes the
standard satellite interfaces (Wyoming protocol, as used by Rhasspy, Home Assistant and
OpenVoiceOS) so the user can swap any engine, on the device, on the hub or remote.

## Chips and main parts

| Part | Role | Status |
|------|------|--------|
| Rockchip RK3576, SoC with NPU (Luckfox Core3576 development module for the first prototype) | Leo OS, local models, audio, playback, display out | Selected for the first prototype, not measured yet. An abstraction layer for the NPU keeps a second SoC possible; Axera AX630C and Qualcomm QCS6490 are followed. Power in idle and in inference, and tokens per second, are measured on the same run and published only when measured |
| Nordic nRF54L15 | Always-on companion: button, Bluetooth, status display, power, wake | Selected for prototype A |
| Two MEMS microphones (and an XMOS front end in the prototype) and a speaker | Voice in, audio out. Push-to-talk, no always-listening microphone | Prototype |
| Small status display | What is playing, battery, pairing QR code | Prototype |
| HDMI output | External screen, viewing session, powered by the cable | The RK3576 lists HDMI in its product brief; to be verified on the development module. Video over USB-C (DisplayPort Alt Mode) is an alternative evaluated in WP1 |
| Li-Po battery, USB-C charging | Power. Target: one day with 30 interactions and 2 hours of Bluetooth music | Prototype |

Target figures of the project, pocket mode: idle under 10 mW with the SoC off
and the companion and Bluetooth alive; a local turn (button release to first audio)
between 1.5 and 2.5 s; a cloud turn under 2 s; surface under 48 C; weight under 50 g and
size under 60 x 40 x 15 mm; one day of battery. Each target has a written fallback
(smaller models, shorter answers, suspend with memory retention, routing the request
to the hub). Screen mode targets are proposed, not fixed: a stable 1080p image and the
viewing session up within a few seconds of plugging in, with power and temperature
measured in that mode. The full measurement
protocol is in `docs/bring-up.md`. No figure is published before it is measured.

## Plan: twenty-four months, eight work packages

| # | Months | Work package | Deliverable |
|---|--------|--------------|-------------|
| WP1 | 1-3 | Requirements, architecture and test plan | Specification v1, usage profile for battery life, test plan, baseline measured on the development module |
| WP2 | 2-10 | Own hardware: schematics, PCB, power, audio, antenna, thermal, video output and enclosure | Working rev B board, thermal report, enclosure |
| WP3 | 4-14 | nRF54L15 firmware (Zephyr) and power management | Firmware with wake, power states and the switch between pocket mode and screen mode, idle power report |
| WP4 | 6-16 | Local AI on the device: quantisation, adaptation and evaluation of STT, LLM and TTS on the NPU | Open local AI stack, evaluation sets, optimisation report |
| WP5 | 10-18 | Hybrid architecture with privacy, and the viewing session on a monitor | Local and cloud router, threat model, privacy test report, full-screen viewing session |
| WP6 | 14-21 | Design for manufacturing and a batch of 50 prototypes | DFM package, 50 units with end-of-line test records, three manufacturer quotes |
| WP7 | 19-24 | Pre-conformity and pilot with users | Pre-conformity report (diagnostic, radio and EMC), report of a pilot with 10 users |
| WP8 | 1-24 | Management, audit, intellectual property and dissemination | Milestone reports, formal prior-art search before month 4 |

Formal certification (RED, EN 18031, FCC, UN 38.3) and the first production series come
after this plan.

The project is self-funded and prepared for public R&D support in Spain and Europe.
No investors.

Hardware, firmware, system and hub are published under the free licences below, which
grant patent rights to every user. No patent has been filed. A formal prior-art search
is done at the start of the project, before any part of the power and thermal design is
published. If it finds something patentable, the application is filed before that part
is published; if not, open publication works as defensive disclosure. The manufacturing
test system and the pilot data are not published. Target price: 249 EUR including VAT,
to be confirmed with the manufacturer quotes.

## Licences

- Hardware (schematics, PCB, enclosure, BOM): CERN-OHL-S 2.0, see `LICENSE-HARDWARE`.
- Software, firmware and system image: GNU AGPL-3.0-or-later, see `LICENSE`.
- Documentation: CC BY-SA 4.0, see `docs/LICENSE-DOCS`.

## Who

Started by Diego Torres at Next Gen Academy SL (77 Delta), Barcelona. Contributions
are welcome once the first schematics and the first OS image are published; until
then, open an issue to say hello. See `CONTRIBUTING.md`.
