# TBWorldview

Written record of the ToolBook 8 reverse-engineering work.
The books are Navy electronics lessons that ran in Click2learn ToolBook II 8.1 shells.
The engine is the 16-bit Asymetrix runtime `TB80RTM`.

This repository is the notes. It does not contain the ToolBook binaries, the course books, or the reader source.

## Print

[`_production/TBWorldview_printable.pdf`](_production/TBWorldview_printable.pdf) is the double-sided binder for `v1.0.0r2`. Each major section starts on a right-hand page. Print it duplex, flip on the long edge. Rebuild it when cutting a release, not when the notes change.

## In this directory

- `00_Index.pdf` — package map
- `01_Overview.pdf` — status of the work
- `02_Architecture.pdf` — runtime architecture
- `03_Naming_Progress.pdf` — function-naming coverage
- `ToolBook_Architecture.md`, `ToolBook_Chapter.md`, `naming_summary.md`, `toolbook_naming_progress.md` — source notes

## Subdirectories

- `Modules/tb80*_knownnames/` — confirmed renames and nameable lists, one directory per DLL
- `Modules/module_catalog.md` — what each runtime module does, and the export ordinals that matter
- `BookFormat/` — on-disk page and widget records
- `OpenScript/` — bytecode decoder notes
- `SoftICE/` — dynamic analysis
- `Runtimes/` — clean-room reader status
- `SME/` — the ToolBook 11.5 `.TBKX` export, from correspondence with JohnDoe1
- `Worldview/` — the ledger, the reader notes, the Win16 tangent, and who used ToolBook. The binder is rebuilt only for a release. The current print is `v1.0.0r2`.

## Related repositories

- [toolbookunpacker](https://github.com/squirelworks/toolbookunpacker) — unpacks the Optloader-wrapped NE images
- [Foolbook](https://github.com/squirelworks/Foolbook) — modern viewer
- [TBpipeline](https://github.com/squirelworks/TBpipeline) — the reader and the 16-bit hosts. `documentation/` at `93ce117` is the record this print cites.
