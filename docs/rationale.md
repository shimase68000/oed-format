# OED Format Rationale

## 1. Background

In traditional FM music workflows, tone data is not distributed as an independent resource.

Instead, tone data is typically embedded within performance data formats such as:

- MDX
- MML-based formats
- tracker modules

As a result, tone data exists only as a part of music data, and is not treated as a reusable library.

---

## 2. Problem

This structure leads to several limitations:

- Tone data is difficult to reuse independently
- There is no standard format for tone libraries
- Extracting and organizing tones requires manual effort
- Tone collections are fragmented across different music files

In practice, performance data itself becomes an implicit tone library.

---

## 3. Design Goal

OED is designed to invert this relationship.

Instead of treating tone data as a secondary component,
OED treats tone data as a primary resource.

The goal is to enable:

- independent tone collections
- reusable tone libraries
- accumulation of tones over time
- exchange of tone data between tools

---

## 4. Library-Oriented Design

OED is not just a file format, but a structure intended to naturally evolve into a tone library.

Users do not need to explicitly manage a database.

Instead, they:

- collect tones from various sources
- merge tone files
- refine and reuse existing tones

Over time, this process results in a structured tone library.

---

## 5. Supporting Structure

To support this behavior, OED includes:

### Library-level information
- `file memo` — overall description of the library

### Tone-level information
- `tone name` — title of each tone
- `tone memo` — notes and annotations per tone

### Context information
- `author`
- `toolname`
- `environment`
- `datetime`

### Device information
- `targetdevice`

These fields allow tone data to be understood, organized, and reused across different contexts.

---

## 6. Tool Independence and Private Data

OED is designed to be independent of any specific tool.

At the same time, a small tool-specific area is provided:

- `tooldata` (8 bytes)

This area may be used by the tool identified in `toolname`
to store implementation-specific data.

The interpretation of `tooldata` depends on the tool.

If a different tool processes the file, the existing `tooldata`
may be considered undefined and reinitialized.

This design allows flexibility for tools without compromising
the portability of tone data.

This behavior acts as a safety mechanism.

Tool-specific internal states (such as undo history or preview settings)
may not be meaningful across different tools.

Reinitializing `tooldata` prevents unexpected behavior when a file is
processed by a different tool.

---

## 7. Intended Usage

OED is designed to support multiple usage patterns for tone data.

### 7.1 Tone Patch Usage

OED can be used similarly to patch data in analog or digital synthesizers.

Each tone represents an independent sound unit that can be edited,
stored, and reused.

---

### 7.2 External Tone Reference

OED is intended to be used as an external tone library.

Sequencers, MML systems, or other tools can reference tones stored in OED files,\
instead of embedding tone data directly into performance data.

This enables separation between:

- tone data (sound design)
- performance data (music)

---

### 7.3 Library and Database Usage

OED is also designed for database-like usage.

Users may:

- search tones across multiple OED files
- extract selected tones
- merge tone collections
- build custom tone libraries

This allows OED to function as a foundation for tone database systems.

---

## 8. Format vs Implementation

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

OED000 uses a fixed-length structure by design.

This choice prioritizes:

- efficient processing on retro or resource-constrained systems
- fast random access (seeking)
- simplicity of implementation

Future versions (e.g., OED001) may introduce variable-length fields,\
but OED000 intentionally adopts a fixed layout for robustness and performance.

---

## 9. Long-Term Vision

OED is intended to serve as a core format for an FM tone database.

It may support future workflows such as:

- shared tone libraries
- searchable tone databases
- integration with sequencers and composition tools
- cross-device tone conversion
- use of higher-level descriptors such as OTG (OED Operator Topology Glyph)
  to describe device algorithms

This enables comparison and search of tone characteristics across different devices,
beyond raw binary data.

Future versions (e.g., OED001) may extend the format to support:

- variable-length fields
- richer metadata
- additional device types

However, the core idea remains unchanged:

**tone data should be a first-class resource.**

---

## 10. Summary

OED redefines how tone data is treated:

- from embedded data → independent resource
- from incidental usage → structured accumulation
- from tool-specific → tool-independent

It provides a foundation for building and sharing tone libraries,\
and for treating sound design as a reusable asset.
