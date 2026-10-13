# Chapter: Understanding the ToolBook 8.0 Reverse-Engineering Effort

This chapter summarizes the state of the work on the Nida Corporation ATT Master_3 ToolBook courses (ToolBook II 8.1 shells, book version 01 00 00 08). It is written so that a reader who has not followed every SoftICE session or Ghidra pass can still see what was learned, what already works, and what remains open.

## 1. What the books are

The course files are classic Asymetrix/Click2learn ToolBook books. Each `.tbk` begins with a short PE stub (0xC00 bytes, no real code) followed by the book body. The magic `ZZZTBKBOOKLOC` and the value `0x22222222` mark the start of the real book data. The opener is `tbload.exe` via `MINSTB32.dll`. The runtime engine that actually interprets the book is the 16-bit NE binary `TB80RTM.EXE` (unpacked from its SLR Optloader wrapper). Supporting DLLs (`TB80BAS`, `TB80CMP`, `TB80BMP`, `TB80UTL`, `TB80W16`, and others) supply object types, painting, OpenScript compilation/expansion, bitmaps, and windowing.

The courses themselves are Navy electronics CAI (not the drug-abuse institute of the same name). `courses.xml` is a menu export that lists 0_MASTER3 plus twenty rating tracks. On disk there are hundreds of lesson `.tbk` files; the work has concentrated on one well-understood example, `9e954d01.tbk` (Logic Families), plus the shared shell `nida81.tbk` and the system books (`nc81base.sbk`, `nc81recs.sbk`, `nc81test.sbk`, `nc81trnr.sbk`, `tb80r.sbk`).

## 2. On-disk structure that is now understood

Page objects are the only records that begin with the marker `80 65 00 05 00 02 02`. Inside each page the widgets are nested chunks that begin `14 00 00`. A typical property block starts `14 00 00 01`. Field text is stored as a `Text` property (length-prefixed, often with a leading CRLF). Standard property IDs (bounds = 0x4004, text = 0x402e, script = 0x4023, vertices = 0x4034, etc.) were recovered from NE resource type 0x2d1 / id 0x2d0 in `TB80BAS`.

Object-type names (Page, Button, Field, Picture, Polygon, Arc, Curve, Rectangle, etc.) live in a classification table inside `TB80UTL.DLL` and are also present as resources in the system `.sbk` files. The system books are ordinary books. Their scripts have not been fully read. An earlier sentence that called them resource containers with no handlers is retired as WV-024.

OpenScript bytecode is stored in the page records. The keyword tables are NE resources 0x77 and 0x78 in `TB80CMP`. A decoder now correctly expands both the keyword lookups and the 0xC0–0xFF back-references, producing readable handlers (“go to page "P400"”, “send next”, etc.). For `9e954d01.tbk` this yielded dozens of usable handlers.

Polygon geometry (the case that has been solved) is stored in `14 00 00` subtype-0 records whose kind field is `04 00`. The point count is a word at a known offset, followed by x,y twip pairs. Screen coordinates are obtained by the scale `(stored * 96 + 720) / 1440` (equivalently stored/15 when the DPI factors are 96). Fill color is a dword in the same record. This layout was confirmed both statically and by live GDI!POLYGON traces.

## 3. What the modern readers already do

Several generations of clean-room readers exist:

- Early HTML/XML viewers walk the page marker, extract text and quoted jumps, and present a frame-plus-book UI.
- Lessonshell readers hold pages, Next/Back items, quiz choices, and decoded script. The shell walks `gotoPageList`, `setupQuizPage`, and answer handling. Logic Families is the best rendering achieved so far.
- Python and C++ packages parse a book and emit HTML or drive a simple player. System-book stubs supply the path manager, bookmarking, and popup behavior that lessons expect.
- A self-contained GUI serves either a raw `.tbk` or a lessonshell JSON, showing page lists, field text, and quoted jumps. Graphics are still omitted.

All of these can already display the textual content, navigation, and quizzes of books that resemble Logic Families. They do not yet paint arbitrary polygons, arcs, curves, or placed bitmaps for the rest of the corpus.

## 4. Dynamic analysis and the live object model

SoftICE + ICEDUMP on a Windows 98 VM has been the primary dynamic tool. It confirmed:

- Window classes owned by TB80RTM (`TBKMain`, `TBKClient`, `SchedulerWndTB80`, \ldots).
- The live polygon painter in `TB80BAS` segment 0x82 (object+0x30 = count, object+0x32 = point array).
- Twip-to-screen scaling in segment 2.
- Arc and rectangle painters and their object layouts.
- That many “dumps” were actually SoftICE console captures rather than target memory; later sessions with expert mode and careful addressing produced real object and page dumps.

A parallel effort unpacked every Optloader-wrapped NE image, imported them into Ghidra, and began systematic renaming. More than one hundred functions now have meaningful names (PaintPolygon, ScaleTwipsToScreen, SetProperty, SetPointArray, various BitBlt and string helpers, compiler expand routines, etc.). Coverage is still only ~17 % of the unique functions, but the paint and property paths are no longer anonymous.

The book-open sequence itself has been sketched (Tbload → ZZZTBKBOOKLOC → JBO signature check → page allocation → CDBSENDCREATE / CDBNEWOBJECT → bounds written at object+0xF) but the exact instruction that reads a `14 00 00` record and fills the object has not been isolated under the debugger. That missing translation step is why most non-polygon widgets and most other books still render poorly.

An early working note claimed that the on-disk property header `14 00 00 01` is rewritten in memory as `14 00 00 11`. That rewrite was not confirmed. Later live `80 65` objects used `14 00 00 02`. Do not treat `11` as the expanded form.

## 5. Graphics status

- Embedded Windows BMPs have been carved from the course books (hundreds of unique pictures and icons).
- The shared near-start graphic is a 581×157 8 bpp gold plate; the nida81 splash is a known 323×344 bitmap.
- Polygons on pages such as P430 (the chip) can be extracted and drawn correctly, including pin tips and body.
- Curves (the flame on P420), arcs, connecting lines, and content rectangles are only partially understood. Some point runs exist in the page blobs but lack a clear header or are computed from bounds at paint time rather than stored.
- Picture widgets and their placement rectangles are not yet bound to the carved BMPs.

## 6. What remains open

The single highest-leverage gap is the record-to-runtime-object mapping: given an arbitrary `14 00 00` chunk, produce the object type, the bounding box at +0xF, and any type-specific geometry. Once that mapping exists, the polygon renderer can be generalized, arcs and rectangles can be drawn, and the already-carved bitmaps can be placed.

Secondary gaps are:

- Full OpenScript execution (the current readers interpret only the navigation and quiz subset).
- Robust handling of system-book and cross-book jumps for every lesson, not just Logic Families.
- A convenient way to log the fast open path (SoftICE `BPX \ldots DO` logging, a Watcom debugger session, or a thin logging proxy in the style of the existing TB80BMP proxy).

## 7. Work recorded after the first binder

### Module catalog

Export names and version resources from the unpacked images are written up in `Modules/module_catalog.md`. The lesson path is `tbload` to `TB80RUN`, through the W32/W16 thunk, into `TB80RTM` and then `TB80BAS`. `CDBFILEOPEN` is ordinal 8. `COMPILEREXPANDTEXT` is ordinal 3. `TB80JPG` still carries the version-resource name `TB60JPG`. `TB80LNL` is the Lionel media engine. The `linkDLL` extensions are not loaded unless a handler asks for them.

### A second way to read a book

JohnDoe1, a former SumTotal ToolBook writer and support programmer, described a hidden ToolBook 11.5 command that saves a book as `.TBKX`. That file is a ZIP of the media plus an XML description of pages, objects, bounds, layers, and properties. It was introduced around 9.5. The constraints (32-bit 11.5, MSXML 4, the unresettable 30-day trial, and the permanent trial banner on any book the trial saves) are in `SME/README.md`.

As of 2026-10-12 JohnDoe1 was testing the 8.1 to 11.5 to `.TBKX` steps and had not sent them. No NIDA lesson has been opened in 11.5. The personal name and the Knowledge Base address are not in this repository.

## 8. What is in this repository

This repository is the written record. It does not contain the binaries, the course books, or the reader source.

| Area | Where |
|------|--------|
| Status, architecture, naming | `01_Overview.pdf`, `02_Architecture.pdf`, `03_Naming_Progress.pdf`, and the Markdown beside them |
| Confirmed function names | `Modules/tb80*_knownnames/` |
| On-disk records and geometry | `BookFormat/` |
| OpenScript decoder notes | `OpenScript/` |
| SoftICE sessions | `SoftICE/` |
| Clean-room reader status | `Runtimes/` |
| Module roles and export ordinals | `Modules/module_catalog.md` |
| ToolBook 11.5 and the `.TBKX` export | `SME/` |
| Claim ledger (draft, not the `v1.0.0` release) | `Worldview/` |
| Double-sided binder | `_production/TBWorldview_printable.pdf` |

Code that used to be cited by local folder name lives in other repositories:

- Unpacking the Optloader-wrapped NE images: [squirelworks/toolbookunpacker](https://github.com/squirelworks/toolbookunpacker)
- The modern viewer: [squirelworks/Foolbook](https://github.com/squirelworks/Foolbook)
- Hosting the 8.0 runtime: [squirelworks/TBpipeline](https://github.com/squirelworks/TBpipeline)

Rename scripts, carved bitmaps, polygon experiments, and the SoftICE kit are working files. They are not published here. The names those scripts confirmed are the lists under `Modules/`.

The work has already extracted the readable content and the navigation logic from the books and has produced usable modern viewers for the best-understood lessons. The remaining task is to finish the geometry translation so that the same viewers can paint the diagrams that the original 16-bit runtime drew.
