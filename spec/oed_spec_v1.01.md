# OED File Format Specification

OED Version: OED000\
Status: Stable (Spec Version 1.01)\
Author: UG.\
Last Updated: 2026-03-26  

---

## 1. Overview

OED is a tone library file format designed for FM synthesis environments.

It is intended to:
- store collections of tone data
- preserve editing-related state
- support interoperability across tools

Originally developed for OPM Tone Editor 'N',
but designed as a tool-independent format.

This document defines version **OED000**.

---

## 2. File Structure

OED file layout (in order):


[Header (16 bytes)]\
[File Memo (96 bytes)]\
[Extended Information (160 bytes)]\
[Channel Volume (16 bytes)]\
[Reserved Area 0 (32 bytes)]\
[Tone Count (4 bytes, big-endian)]\
[Tone Payload Size (4 bytes, big-endian)]\
[Tool Data (8 bytes)]\
[Tone Data Array]\
[Template Tone Count (4 bytes, big-endian)]\
[Template Payload Size (4 bytes, big-endian)]\
[Tool Data (8 bytes)]\
[Template Data Array]


All fields are stored as raw byte arrays without null termination.

---

## 3. Header

The header is a 16-byte ASCII string.

### OED:
- `"OED000          "` → Shift-JIS encoding
- `"OED000U8        "` → UTF-8 encoding

### TED:
- `"TED000          "`
- `"TED000U8        "`

The suffix `U8` indicates UTF-8 encoding.  
Otherwise, Shift-JIS is assumed.

---

## 4. Encoding Rules

Text fields are encoded according to the header:

- SJIS variant → Shift-JIS
- U8 variant → UTF-8

Applicable fields:
- file memo
- author
- toolname
- tone name
- tone memo

Fields such as `environment`, `datetime`, and `targetdevice` use ASCII.

---

## 5. Extended Information Block

The extended information block is 160 bytes:


author : 32 bytes\
toolname : 32 bytes\
environment : 32 bytes\
datetime : 32 bytes\
targetdevice : 32 bytes

The `targetdevice` field identifies the intended synthesis device
or compatible tone model.

### Examples

The following are examples of typical field values:

- `toolname`: `"OPM Tone Editor 'Ｎ' v1.10"`
- `environment`: `"Human68k"`, `"Windows"`, `"Linux"`
- `datetime`: `"2026-03-26 18:46:00"`

Examples of `targetdevice` values:

- `"YM2151"`
- `"YM2203"`
- `"DX7"`

---

## 6. Fixed-Length Field Rules

All string fields in OED000 are fixed-length byte arrays.

- No null termination is used
- Unused bytes should be filled with 0x00
- Strings must not exceed the defined byte length

This rule applies to:
- file memo
- author
- toolname
- environment
- datetime
- targetdevice
- tone name
- tone memo

---

## 7. Tone Data Model

### 7.1 Tone Count


uint32 tonecount (big-endian)


### 7.2 Tone Payload Size


uint32 tonesize (big-endian)


For OED000, the payload size is fixed:


tonesize = 146 bytes


Other FM devices or future versions may use different payload sizes.

---

## 8. Tone Payload Structure

Each tone is stored as a payload of `tonesize` bytes.

---

### 8.1 Device-Specific Section

(Reserved for device-dependent tone parameters)

This section is not specified in v1.01.

---

### 8.2 Common Tail Structure (v000 Rule)

All tone payloads must end with the following structure:


chmode : 1 byte\
chflag : 1 byte\
mark : 1 byte\
tone volume : 1 byte\
tone name : 16 bytes\
tone memo : 64 bytes


Total tail size:


4 bytes + 16 bytes + 64 bytes = 84 bytes


This layout allows extraction from the end of the payload:


payload_end - 64 → tone memo\
payload_end - (64+16) → tone name\
payload_end - (64+16+4) → control fields


This rule ensures compatibility across different device payload formats.

The field names "tone name" and "tone memo" correspond to\
`name` and `memo` in reference implementations.

---

## 9. Flags

### 9.1 chflag

Bit assignment:


bit0 = channel A\
bit1 = channel B\
...\
bit7 = channel H


### 9.2 mark

The `mark` field is a raw flag field.

Bit assignment is not defined in this version and may change in future revisions.

---

## 10. Template Section (TED)

Template tones are stored after the main tone array:


uint32 template_tonecount\
uint32 template_tonesize\
template payload array


Template payload structure follows the same rules as tone payloads.

---

## 11. OED vs TED Differences

| Field              | OED | TED |
|-------------------|-----|-----|
| channelvol        | Yes | No  |
| reserve2          | No  | Yes |
| template section  | Yes | No  |

---

## 12. Limits and Compatibility

- Tone count is not limited by the format
- Editor implementations may impose limits (e.g., 200 tones)

Readers may ignore tones beyond supported capacity.

All multi-byte integers are stored in big-endian format.

---

## 13. Reserved and Tool Data Areas

Reserved fields are intended for future expansion.

All reserved bytes should be set to `0x00` when unused.

### reserve0

`reserve0` is reserved for future format-level extensions.  \
Tools should not assign private meanings to this area.

### tooldata

Two `tooldata` fields (8 bytes each) are present:\
one before the tone data array and one before the template data array.

A tool may interpret and use `tooldata` only when the value of\
`toolname` identifies that same tool.

If `toolname` identifies a different tool, the contents of `tooldata`\
should be treated as undefined for the current tool, and may be\
reinitialized before use.

The meaning of `tooldata` is not defined by OED000.\
Compatibility of `tooldata` contents across different tools is not guaranteed.

---

## 14. Design Principles

OED000 is based on the following design principles:

- Fixed layout with explicit sizes
- Forward compatibility via payload size field
- Common tail structure for cross-device interoperability
- Tool-independent data representation

Future versions (e.g., OED001) may extend or relax these constraints.

---

## 15. Reference C Structures (Non-Normative)

This section provides reference definitions for implementation.

The normative specification is defined above.

```c
typedef struct {
    uint8_t author[32];
    uint8_t toolname[32];
    uint8_t environment[32];
    uint8_t datetime[32];
    uint8_t targetdevice[32];
} OED_EX;

(Additional structures omitted)
