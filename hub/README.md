# Hub

Licence: AGPL-3.0-or-later (see `../LICENSE`).

The hub runs on the user's own computer (laptop, home server). It is the only place
that holds long-lived credentials. Written in M4.

Responsibilities:

- Pairing: generate the pairing session, read the device's QR code, bind the device key.
- OAuth 2.1 with PKCE against the user's services; token storage and revocation.
- Clients: Nextcloud (files, notes), CalDAV (calendar), CardDAV (contacts), IMAP (mail,
  read and short replies), Matrix (messages).
- MCP server: exposes calendar, contacts, mail and messages as tools, so any assistant
  the user chooses can call them; the device's command grammar maps to the same tools.
- Short-lived capability tokens for the device; audit log of what the device asked.
- Test suite and threat model (`../docs/threat-model.md`).

Nothing in the hub depends on a specific speech engine or language model.
