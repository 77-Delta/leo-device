# Hardware

Licence: CERN-OHL-S 2.0 (see `../LICENSE-HARDWARE`).

Status: no schematic yet. This directory will hold, in this order:

1. `block-diagram/` - exported from `../docs/architecture.md` (M1).
2. `schematics/` - KiCad project: interconnect board between the Luckfox Core3576 module
   and the nRF54L15, audio codec, microphones, charger, display connector (M1 v0, M6 v1).
3. `pcb/` - KiCad layout and fabrication outputs (M6).
4. `enclosure/` - 3D printable enclosure, source and STL (iterations from M1, final in M6).
5. `bom.md` - bill of materials with part numbers, supplier and date of the price.
6. `measurements.md` - power, thermal and acoustic measurements with method and date.
7. `assembly.md` - assembly guide for one unit (M6).

## Candidate development kits (design stage)

Prices as read on the supplier pages on 2026-10-16; they change. Nothing has been
purchased. Chosen parts are confirmed in `bom.md` when ordered.

| Board | Role | Price seen | Source | Note |
|-------|------|-----------|--------|------|
| Luckfox Core3576 (RK3576, 6 TOPS NPU, 4-8 GB) | Application processor, primary candidate | 178.99 USD | luckfox.com | RKLLM runtime mature, MIPI DSI, Debian / Buildroot |
| M5Stack LLM630 Compute Kit (Axera AX630C) | Application processor, reserve | 99.90 USD | shop.m5stack.com | Cheaper and smaller; runtime less documented, SPI display only |
| nRF54L15 DK (Nordic) | Companion | 39 USD | digikey | Bluetooth 5.4, Zephyr |
| Pi 5 + AI HAT+ 2 (Hailo-10H) | Reference only | 130 USD (HAT, official) | raspberrypi.com | Too large for a pocket |

Power figures for these boards are not published here until measured on the bench; see
`../docs/bring-up.md`.

## Design constraints

- Pocket volume: target the footprint of a classic MP3 player; exact dimensions after the
  first enclosure iteration.
- Push-to-talk only: no always-listening microphone path by design.
- Parts with mainline Linux drivers where a choice exists (SPI display, I2S codec).
- Everything printable or orderable in quantity one.
