# TBWorldview

Written record of the ToolBook 8 reverse-engineering work.
The books are Navy electronics lessons that ran in Click2learn ToolBook II 8.1 shells.
The engine is the 16-bit Asymetrix runtime `TB80RTM`.

This repository is the notes. It does not contain the ToolBook binaries, the course books, or the reader source.

## Print

[`_production/TBWorldview_printable.pdf`](_production/TBWorldview_printable.pdf) is the double-sided binder. Each major section starts on a right-hand page. Print it duplex, flip on the long edge.

## In this directory

- `00_Index.pdf` — package map
- `01_Overview.pdf` — status of the work
- `02_Architecture.pdf` — runtime architecture
- `03_Naming_Progress.pdf` — function-naming coverage
- `ToolBook_Architecture.md`, `ToolBook_Chapter.md`, `naming_summary.md`, `toolbook_naming_progress.md` — source notes

## Subdirectories

- `Modules/tb80*_knownnames/` — confirmed renames and nameable lists, one directory per DLL
- `BookFormat/` — on-disk page and widget records
- `OpenScript/` — bytecode decoder notes
- `SoftICE/` — dynamic analysis
- `Runtimes/` — clean-room reader status

## Related repositories

- [toolbookunpacker](https://github.com/squirelworks/toolbookunpacker) — unpacks the Optloader-wrapped NE images
- [Foolbook](https://github.com/squirelworks/Foolbook) — modern viewer
- [TBpipeline](https://github.com/squirelworks/TBpipeline) — hosts the 8.0 runtime
