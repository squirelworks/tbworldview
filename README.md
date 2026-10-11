# TBWorldview

Printable documentation for the ToolBook 8 reverse-engineering work.
The books are Navy electronics lessons that ran in Click2learn ToolBook II 8.1 shells.
The engine is the 16-bit Asymetrix runtime `TB80RTM`.

Snapshot 2026-10-11.

## Binder

[`.production/TBWV1.0.0_printable.pdf`](.production/TBWV1.0.0_printable.pdf) is the double-sided binder PDF (42 pages). Each major section starts on a recto page. Print it duplex, flip on the long edge.

## This directory

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
