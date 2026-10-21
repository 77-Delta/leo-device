# Comparison with other efforts

Short version of the comparison written for the funding application. Dates and facts
are as known on 2026-10-17; corrections welcome as issues.

Closed devices. Amazon Echo, Google Nest and Apple's Siri devices are the only widely
used voice clients; the whole stack is closed and tied to one vendor's cloud. Humane AI
Pin (2024, discontinued 2025) and Rabbit R1 (2024) were closed pocket devices that
depended on a proprietary cloud. Plaud, Limitless pendant, Bee and Omi are closed
recorders and "AI pendants" that upload everything to a vendor cloud.

Open software. OpenVoiceOS is a voice stack for Raspberry Pi class boards and desktops,
with no pocket hardware or battery-first design; this device is a satellite of it. Home
Assistant Voice Preview Edition (2024) is a mains-powered ESP32-S3 table unit that uses
the Wyoming protocol; it shows the satellite architecture works but is not portable.
Rhasspy is the origin of Wyoming and is now part of Home Assistant. Mycroft Mark II was
a desk device and the company closed in 2023.

Open hardware. ZSWatch is an open smartwatch on nRF chips with Zephyr; we follow its
approach for the always-on companion. Precursor / betrusted is a text-first pocket
device with a keyboard. MNT Reform and MNT Pocket Reform are open laptops built around
existing modules; we reuse that lesson. Qryptr exchanges data with a phone through QR
codes; we borrow the QR pairing pattern. PineTime and PineBuds are open wearables
without service integration.

What is new here is the combination: a battery-first pocket client with a Linux
application processor and an always-on companion chip, earbuds support, QR account
pairing, and a hub that exposes the user's own open services to voice through standard
protocols (Wyoming, OAuth 2.1, MCP).
