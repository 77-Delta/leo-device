# Contributing

The project is at design stage. Until the first schematics and the first firmware are
published (milestone M1), the most useful contribution is an issue: a use case, a part
suggestion with a datasheet link, a measurement from a similar board, or a correction.

Rules that apply to every contribution:

- Hardware files go under `hardware/` and are licensed CERN-OHL-S 2.0.
- Firmware and hub code go under `firmware/` and `hub/` and are licensed AGPL-3.0-or-later.
- Documentation goes under `docs/` and is licensed CC BY-SA 4.0.
- Every figure (current, latency, price, battery life) must say how and when it was
  measured or where it was read. Figures that are not measured are marked "not measured".
- Patents: by contributing you agree not to assert patents on your contribution.
- Plain language, one idea per sentence. Use "-" as the only dash.

Speech engines (speech to text, text to speech, wake word) are not part of this
repository. The device speaks the Wyoming protocol; add your engine as an external
service and document how you connected it in `docs/`.
