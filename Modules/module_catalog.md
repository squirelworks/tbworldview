# ToolBook II 8.1 module catalog

Recorded 2026-10-11 from version resources and export names on the unpacked images. This is the native path the NIDA lessons use. It is not the DHTML path.

## Process and thunk

| Module | Role |
|--------|------|
| `tbload.exe` | Course opener. Reads the Click2learn shell and starts the runtime. |
| `TB80MAIN.EXE` | Small PE stub on the runtime side of startup. |
| `TB80RUN.EXE` | 32-bit launcher. Exports only mutex wait and release. |
| `TB80RTM.EXE` | 16-bit shell. Main window, print, DDE, find, history, status bar. Window classes include `EGYPT*` and `TBKClient`. |
| `TB80W32.DLL` | 32-bit half. `TBXInstance`, `MEM1632`, DIB dither. About 246 exports. |
| `TB80W16.DLL` | 16-bit half. `CallProc32W`, file thunks, palette and graphic entry points. About 406 exports. |
| `TB80F32L.dll` | Flat 32-bit entry and the common-dialog hook. |
| `TBFILE32.DLL` | Long filenames for the 16-bit engine. |
| `TB80NET.EXE` | Network access provider. A 16-bit NE executable, not a DLL. |

## Book engine

| Module | Role |
|--------|------|
| `TB80BAS.DLL` | Book server. 606 exports, the `CDB*` API. File description: "ToolBook Server DLL". |
| `TB80CMP.DLL` | OpenScript compiler and interpreter. Keyword resources `0x77` and `0x78`. |
| `TB80UTL.DLL` | Object classification table: Page, Button, Field, Polygon, and the rest. |
| `TB80RCR.DLL` | Linked by `TB80RTM` beside the book server. Exports not fully annotated. |
| `TB80ENU1.DLL` | 16-bit English string table. |
| `TB80ENU2.DLL` | 32-bit copy of the same strings. |

Landmarks: `CDBINIT` is `TB80BAS` ordinal 1, `CDBFILEOPEN` is ordinal 8, `CDBFILESAVE` is ordinal 9, `CDBVIEWPAGE` is ordinal 15, `CDBVIEWBKGND` is ordinal 18. `COMPILERCOMPILETEXT` is `TB80CMP` ordinal 1, `COMPILEREXPANDTEXT` is ordinal 3, `CFUNEXECUTE` is ordinal 8, `CFUNEVALUATE` is ordinal 9.

## Drawing and media

| Module | Role |
|--------|------|
| `TB80BMP.DLL` | Graphic display, metafile play, FX blit. |
| `TB80JPG.DLL` | JPEG. The version resource still carries the old name `TB60JPG.DLL`. |
| `TB80FLT.DLL` | Filter and clip editor. |
| `TB80LNL.DLL` | Multimedia ToolBook media engine. The notify callback is Lionel. |
| `TB80MM.DLL` | Multimedia notification only. Small. |

## OLE and old extensions

`TB80OLE`, `TB80OCX`, `TB80ACX`, `TB80VBX`, and `TB80RMT` are clipboard, control, and automation support. The course opener does not require `TB80RMT`. None of these are on the path that paints a Logic Families page.

`linkDLL` libraries add commands only when a handler links them: `TBDLG`, `TBWIN`, `TBDOS`, `TBREG`, `TBDB3`, `TBPDX`, `TBCBT`. `TBCBT` is the CBT scoring extension.

## System books

| Book | Role |
|------|------|
| `tb80r.sbk` | Reader script. Menu and status behavior that is not in `TB80RTM`. |
| `tb80hyp.sbk` | Hyperlink and help behavior. |
| `tb80anm.sbk` | Path animation. |
| `nc81base.sbk`, `nc81test.sbk`, `nc81recs.sbk`, `nc81trnr.sbk` | NIDA shell: base, test, records, trainer. |
| `nc81int.dll`, `nc81rec.dll` | Course interop and the record store. Not Asymetrix. |

A system book adds a script above the book. A `linkDLL` adds commands. Most of what an author treats as ToolBook is one of those two, not a new entry point in `TB80BAS`.

## Still thin

`TB80RCR` exports are only partly named. The `TB80UTL` classification indexes are not yet tied to the kind bytes inside `14 00 00` chunks. Author binaries `TB80EDT.DLL` and `TB80.EXE` are not in this set.
