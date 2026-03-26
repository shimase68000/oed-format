# OED File Format

OED is a tone library file format designed for FM synthesis environments.

It is designed not just as a data container, but as a format that naturally evolves into a tone library through everyday use.

---

## Overview

OED (OPM Editor Data) is a binary format for storing collections of FM tone data.

It is designed to:

- store large collections of tones
- preserve editing-related state
- support interoperability across tools
- remain independent from any specific application

Originally developed for **OPM Tone Editor 'N'**,  
but intended to be used as a standalone format.

---

## Key Features

- Fixed binary structure (OED000)
- Encoding-aware (Shift-JIS / UTF-8)
- Metadata support (author, tool, environment, datetime)
- Per-tone metadata (name, memo)
- Template tone support
- Forward-compatible payload design
- Device-independent structure with common tail layout
- Tool-specific private storage (tooldata, 8 bytes)

---

## Design Concept

OED is designed as a format that **naturally becomes a tone library**.

Instead of explicitly managing a database, users:

- pick tones from various sources
- merge tone files
- reuse and modify existing tones

Over time, this process naturally forms a structured tone library.

---

### Supporting Structure

The format includes fields that support this concept:

#### Library-level information
- `file memo` — overall description of the library

#### Tone-level information
- `tone name` — title of each tone
- `tone memo` — notes and annotations per tone

#### Metadata
- `author`
- `toolname`
- `environment`
- `datetime`

#### Device interoperability
- `targetdevice`

---

## Format vs Implementation Limits

The OED format itself supports:

- tone count: `0` to `0xffffffff`
- template tone count: `0` to `0xffffffff`

However, individual tools may impose practical limits.

For example:

- OPM Tone Editor 'N'
  - maximum tones: 200 (tones beyond this limit are ignored)
  - maximum template tones: 8 (templates beyond this limit are ignored)

Data beyond these limits is not considered an error,
but is simply ignored by the tool.

---

## Specification

The full specification is available here:

→ [OED Specification](spec/oed000_spec_v1.01.md)

---

## Rationale

The design background and philosophy of OED are described here:

→ [Rationale](docs/rationale.md)

---

## Versioning

| Version | Status  | Description |
|--------|--------|------------|
| OED000 | Stable | Fixed-length payload (current) |
| OED001 | Planned | Extended format (in development) |

---

## Future Direction

Future versions such as OED001 are expected to extend device description.

In particular, OED001 is planned to adopt  
**OTG (OED Operator Topology Glyph)**  
for describing target-device algorithms.

OTG provides a compact representation of FM operator connections,
enabling consistent expression of tone structures across different devices.

---

## Design Philosophy

OED is designed with the following goals:

- Simple and explicit binary layout
- Forward compatibility
- Tool independence
- Natural accumulation of tone data
- Minimal assumptions about usage

---

## License

MIT License
