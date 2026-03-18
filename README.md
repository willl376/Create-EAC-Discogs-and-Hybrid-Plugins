# EAC Metadata Plugins — Discogs COM Plugin + Modern C‑ABI Plugin

This repository contains **two different metadata plugin interfaces** for
Exact Audio Copy (EAC):

1. **A legacy Discogs plugin** using a reverse‑engineered COM interface  
2. **A modern, clean C‑ABI plugin** designed for hybrid metadata engines  
   (Discogs + MusicBrainz WS/1 + MusicBrainz WS/2 + more)

Both plugins are MIT‑licensed and fully documented here.

---

# 1. Legacy Discogs Plugin (COM‑based)

This is the original plugin, rebuilt from reverse‑engineering FreeDB.dll.
It communicates with EAC through an **undocumented COM interface** that
EAC exposes internally.

## Key characteristics

- Exported functions:
  - `GetCDInformation`
  - `GetCDInformationFromXml`
- Communicates with EAC via a COM object implementing:
  - `set_AlbumTitle`
  - `set_Title`
  - `set_genre`
  - `set_CoverImageURL`
  - `set_CoverImage`
  - `set_barcode`
  - `set_SearchTerm`
  - `set_CDDBID`
  - and many more
- Uses `IUnknown` + direct vtable casting fallback
- Uses BSTR for all strings
- Uses COM setters to deliver metadata back to EAC
- Logging system writes to `%TEMP%\eac_discogs_v2.log`
- No external dependencies (kernel32 + user32 only)
- Fully functional and stable

## Why this plugin exists

EAC originally supported metadata through FreeDB, which used a COM‑style
interface. This plugin mimics that interface so EAC can load it without
modification.

It works — but it is:

- undocumented  
- reverse‑engineered  
- limited in metadata fields  
- difficult to extend  
- tightly coupled to EAC’s internal COM object  

This plugin remains in the repository for compatibility and historical
purposes.

---

# 2. Modern C‑ABI Metadata Plugin

This is a **clean, documented, future‑proof** plugin interface designed
from scratch. It uses a simple C ABI with packed structs and explicit
function exports.

It is ideal for:

- Discogs
- MusicBrainz WS/1
- MusicBrainz WS/2
- Cover Art Archive
- Internet Archive
- Hybrid metadata engines
- C++/CLI bridges into C#

## Key characteristics

- Exported functions:
  - `EAC_GetPluginInfo`
  - `EAC_Init`
  - `EAC_SearchMetadata`
  - `EAC_GetResultCount`
  - `EAC_GetResultTitle`
  - `EAC_GetResultMetadata`
  - `EAC_Configure`
  - `EAC_Shutdown`
- Uses packed structs (`#pragma pack(push, 1)`)
- Supports:
  - full album metadata
  - full track metadata
  - cover art bytes
  - source URL
  - disc numbers
  - catalog numbers
  - barcodes
  - MCN
  - ISRC
- Versioned API (`EAC_PLUGIN_API_VERSION`)
- Clean separation between:
  - native plugin layer
  - managed metadata engine

## Why this plugin exists

The COM plugin cannot support modern metadata needs.  
This C‑ABI plugin:

- is fully documented  
- is stable  
- is extensible  
- is easy to bridge to C#  
- supports multiple results  
- supports rich metadata  
- supports artwork bytes  
- supports hybrid metadata sources  

This is the **future** of the project.

---

# 3. Comparison: COM Plugin vs. C‑ABI Plugin

| Feature | COM Plugin | C‑ABI Plugin |
|--------|------------|--------------|
| Interface | Reverse‑engineered COM | Clean C ABI |
| Stability | Works but fragile | Fully stable |
| Documentation | None (reverse‑engineered) | Full documentation |
| Metadata richness | Very limited | Full album + tracks + art |
| Multiple results | No | Yes |
| Artwork bytes | No | Yes |
| Hybrid metadata | No | Yes |
| C# integration | Difficult | Easy |
| Future‑proof | No | Yes |

---

# 4. Roadmap

### Phase 1 — Document both plugins  
✔ COM plugin documented  
✔ C‑ABI plugin documented  
✔ Comparison + architecture written

### Phase 2 — Build the Unified Metadata Engine (C#)  
- Discogs provider  
- MusicBrainz WS/1 provider  
- MusicBrainz WS/2 provider  
- URL relation normalization  
- Artwork fallback chain  
- Unified models (`UnifiedRelease`, `UnifiedTrack`, etc.)

### Phase 3 — Build the C++/CLI bridge  
- Implement all `EAC_*` exports  
- Forward calls into the C# engine  
- Fill `EAC_ALBUM_METADATA` structs  
- Provide multiple results  
- Provide cover art bytes

### Phase 4 — Optional retirement of the COM plugin  
- Only when the new engine is fully stable  
- COM plugin remains available for compatibility

---

# 5. License

MIT License — free to use, modify, and distribute.
