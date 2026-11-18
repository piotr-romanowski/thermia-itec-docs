# Thermia iTec Eco — protocol documentation

Vendor-independent documentation of the **Thermia iTec Eco** heat-pump RS485 bus (controller CM930,
software 2.3–2.4; reference unit: Eco 8). Enough to build your own client — reading, control, calendar,
and the online-gateway session — without the paid Thermia Online module.

## Contents

- **[PROTOCOL.md](PROTOCOL.md)** — how the bus works: framing, value encoding (scale, sign, enums,
  bitfields), reading, the two write paths (room-sensor emulation on `0x0A`; online-gateway session on
  `0x0F`), the `0708` mailbox and request bits, the write whitelist, status/alarm bitfields, and the pump
  calendar.
- **[REGISTERS.md](REGISTERS.md)** — the register map: a slim per-slave table (name, unit, scale, sign,
  status), generated from the maintained register map.

## Status and scope

- Facts are tagged `confirmed` (verified on an Eco 8), `candidate` (observed, not fully verified), or
  `unknown` (seen, meaning open). Treat `candidate`/`unknown` as hypotheses.
- Model-specific to **CM930 2.3–2.4 / Eco 8**. Related iTec models (XTR, ATEC) share the Connect profile but
  differ in topology and in which writes a controller accepts.

## Safety

Every write and every emulated response is **active transmission on a live heat-pump bus**. Never run two
responders on the same accessory slot, and never answer `0x0F` while a genuine online module is connected.
Writes change a real pump — keep ranges narrow and verify on the panel.

## Not included

The `0x0F` approval challenge/response algorithm and key are **not** part of this documentation. A client can
join an already-live session without them, but initiating a session after a pump restart requires an external
`approval(challenge[16]) -> response[16]` function that you supply.

## Register table

`REGISTERS.md` is generated from the maintainer's working register map (not published as such), so treat
`REGISTERS.md` as the reference and do not edit it by hand — corrections are welcome as issues. For the record,
the generator is `tools/generate_registers.py <map.yaml> -o REGISTERS.md` (requires PyYAML).

## Acknowledgements

- **ryckema** — iTec XTR M register map with certainty levels, live tests on the XTR and many cross-checks:
  https://github.com/ryckema/Thermia_itec
- **fclauson** — capture of a genuine DCM03 online module on an ATEC, which first showed the `0x0F` mailbox and
  page layout: https://github.com/fclauson/Thermia-ATEC-ESP-emulator-of-DCOM
- **An iTec Eco 5 owner** (who prefers not to be named) — a capture of a genuine Thermia Online gateway on an
  iTec, which located the gateway session on `0x0F` and its challenge window.

## License

MIT (see `LICENSE`). Not affiliated with Thermia, Danfoss or Samsung. Every write is your responsibility.
