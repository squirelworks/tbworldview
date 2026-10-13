# Ledger

Stable print: 1.0.0r1. Edits after that tag are experimental until the next release.
Written 2026-10-13. The engine notes cite TBpipeline at `a106ef0` unless a claim says otherwise.

| Id | Grade | Statement |
|---|---|---|
| WV-001 | Known | These lesson files are ToolBook II 8.1 shells, book version `01 00 00 08`. |
| WV-002 | Known | The book stream starts after a `0xC00` PE stub, at `ZZZTBKBOOKLOC` and magic `0x22222222`. |
| WV-003 | Known | Page records begin `80 65 00 05 00 02 02`. Widgets inside them begin `14 00 00`. |
| WV-004 | Known | Polygon kind `04 00` is decoded, and P430 draws from it. |
| WV-005 | Known | The clean-room reader is not the engine. |
| WV-006 | Known | The engine host has not opened a lesson book. |
| WV-007 | Thought | `CDBFILEOPEN` expands the disk record and writes the live object. |
| WV-008 | Thought | A real Win16 return is the only way the Unicorn host gets further. |
| WV-009 | Conjecture | ToolBook 11.5 can export an 8.1 lesson as `.TBKX`. |
| WV-010 | Not true | On-disk `14 00 00 01` becomes `14 00 00 11` in memory. |
| WV-027 | Known | ToolBook 8.1 calls 16-bit `USER`, `GDI`, and `KERNEL`, not `USER32`. |
| WV-028 | Known | Windows 98 can step this engine. 32-bit XP can run it. 64-bit Windows cannot. |
| WV-029 | Known | Published users are training shops and the Navy lessons on disk, not a living authoring market. |
| WV-030 | Thought | Someone outside this set who still holds a native 8.x book has the same problem. |

## Known

### WV-001

The courses in this work are Nida Corporation electronics lessons, written as Click2learn ToolBook II 8.1 shells. The book version word is `01 00 00 08`. This Nida is not the drug-abuse institute that shares the letters.

Evidence: the shell header, the version word, and the lesson text. Logic Families is `9e954d01.tbk`.
Falsify: a lesson in the set whose book stream is a different version, or whose text is not the electronics course.

### WV-002

A lesson file begins with a `0xC00` PE stub that has no useful code. The book stream is marked by `ZZZTBKBOOKLOC` and magic `0x22222222`, then `03 4A 42 4F`. The opener is `tbload.exe` through `MINSTB32.dll`.

Evidence: the same layout on the lesson files that have been opened, including `9e954d01.tbk` at file offset `0x9ec`.
Falsify: a lesson in the set whose JBO stream is not behind that marker.

### WV-003

The only page records found start with `80 65 00 05 00 02 02`. Inside a page, widget chunks start with `14 00 00`. A common property header is `14 00 00 01`. Field text is a length-prefixed `Text` property. Property ids recovered from `TB80BAS` resources include bounds `0x4004`, text `0x402e`, script `0x4023`, and vertices `0x4034`.

Evidence: the marker search, and NE resource type `0x2d1` / id `0x2d0` in `TB80BAS`.
Falsify: a page that displays in the real runtime and does not carry that marker, or a property id in that resource that does not match the name assigned here.

### WV-004

Polygon records use kind `04 00`. The count and the twip pairs are at fixed offsets in that record. Screen position is `(stored * 96 + 720) / 1440`, which is twips / 15 at 96 dpi. P430, the chip, draws from this, including the pins. In the live object, bounds are at `+0x0F`, the point count at `+0x30`, and the point array at `+0x32`. The painter is in `TB80BAS` segment `0x82`.

Curves on P420 can be drawn as separate files. They are not placed on the page. Neither are the polygons.

Evidence: the record layout, a live `GDI` `Polygon` trace, and the reader output for P430 and P420.
Falsify: a P430 point that the real runtime paints and this layout misses, or a live object whose count is not at `+0x30`.

### WV-005

The clean-room reader walks `80 65` pages, pulls field text, and follows quoted page ids such as `P400`. On `9e954d01.tbk` that is 129 pages. It also extracts 66 Windows BMPs from the book and one tied to P1290, and it draws 18 pages of shapes (23 files, because a page can have more than one curve). The pictures and the shapes are not placed. Navigation is the quoted id, not the bytecode. Quizzes are scraped text, not scored.

Evidence: a TBpipeline `1.0.0r4` run on that book. The debug folder's `render.html` is this reader. Stage 2a runs because the engine did not open the book.
Falsify: a handler whose target is only a bytecode operand and is still followed correctly, or a page whose bitmap is placed by this reader.

### WV-006

`TB80RTM` is entered at segment 2 offset `0x35A`. As of TBpipeline `a106ef0`, the experimental host gets past the old `0x20:0x36c` fault, opens `TB80.INI` and `TB80enu`, binds 580 ToolBook DLL entries, and then exits through DOS with code 0. The book is not among the files it opened. `USER.266` is `SetMessageQueue`. Reaching it is not startup finishing.

An earlier integrated run, before that commit, died 18 bytes after the entry. The instruction was `les ax, [di-66h]`. The address was unmapped. No Win16 call had run.

Evidence: `nehost/rtm - pass 06/PASS06.txt` at `a106ef0`, and the `1.0.0r4` launch log from the earlier run.
Falsify: a host log from this tree whose open list contains the lesson `.tbk` and whose fault is not an exit.

### WV-011

These ordinals are fixed in the unpacked images. `TB80BAS`: `CDBINIT` 1, `CDBFILEOPEN` 8, `CDBFILESAVE` 9, `CDBVIEWPAGE` 15, `CDBVIEWBKGND` 18. `TB80CMP`: `COMPILERCOMPILETEXT` 1, `COMPILEREXPANDTEXT` 3, `CFUNEXECUTE` 8, `CFUNEVALUATE` 9. `TB80JPG` still carries the version-resource name `TB60JPG.DLL`. `TB80LNL` is the Lionel media engine. `TB80RTM` window classes include `EGYPT*`, `TBKMain`, `TBKClient`, and `SchedulerWndTB80`.

Evidence: export tables, version resources, and window-class strings.
Falsify: an unpacked 8.1 image in which one of these ordinals is a different export.

### WV-012

The call at `TB80RTM` segment 22 offset `0x8AA` is `TB80UTL` ordinal 3, `GETSTRMGRSTRING`, five Pascal words. It is not `KERNEL.3` `GetVersion`. String id `0x0FBD` is the product-name id the startup checks. An empty buffer at that check is one path into the C runtime exit.

The words `"ToolBook II"` written by the host are a non-empty stub. They were not read out of the DLL.

Evidence: the reloc at that site, and the host note in TBpipeline `a106ef0`.
Falsify: a disassembly of that site that is `KERNEL.3`, or a capture of id `0x0FBD` whose text is the stub's sentence and was taken from the binary.

### WV-013

`.TBKX`, from about ToolBook 9.5, is a ZIP of the media plus an XML description of pages, objects, bounds, layers, and properties. JohnDoe1 described a hidden command in ToolBook 11.5 that writes it. 11.5 is 32-bit, needs MSXML 4, and the trial cannot be activated. A book saved by the trial forever shows the trial banner in the runtime. Versions before 9.0 are 16-bit and do not run on 64-bit Windows. The steps for 8.1 to 11.5 had not arrived on 2026-10-12, and no lesson has been opened in 11.5 here.

Evidence: JohnDoe1's description of the format and the trial, recorded in `SME/README.md`. The personal name and the site are not in this repository.
Falsify: a 11.5 that writes `.TBKX` as something other than ZIP plus that XML, or a trial save that does not set the banner.

### WV-014

The 1.5 `PHYSSIZE` index is not present in these 8.1 books. `TB80RMT` is not on the path that opens a lesson. The NE images are SLR Optloader wrappers. OpenScript keyword tables are `TB80CMP` resources `0x77` and `0x78`. A decoder can turn some handlers into text, including `go to page` and `send next`. It is not an interpreter.

Evidence: the search of the 8.1 books, the opener's import set, the unpacker, and the decoder notes.
Falsify: an 8.1 lesson that contains a `PHYSSIZE` index, or a lesson open that loads `TB80RMT` before the first page.

### WV-027

ToolBook II 8.1 does its work as Win16. The 32-bit launcher hands the book to `TB80RTM`. Painting goes through 16-bit `GDI`. The startup list of missing `USER32`, `GDI32`, and `KERNEL32` is not a broken install. Those are not the DLLs this runtime calls.

Evidence: the NE images, the ordinal imports, and the pipeline startup log that reports those 32-bit names absent and continues. The longer account is `environments.md`.
Falsify: a lesson open on Windows 98 or XP that calls `USER32` for `SetMessageQueue` or `Polygon`.

### WV-028

Windows 98 is the decompile floor because SoftICE can step the 16-bit segments there. 32-bit Windows XP runs the same lessons inside `NTVDM`, and it is a poor place to single-step `TB80RTM` from the 32-bit launcher. 64-bit Windows has no `NTVDM` and cannot run 8.1 at all. ToolBook 9.0 replaced this engine with a 32-bit one. 11.5 is that later program.

Evidence: the project's own choice of machines, Microsoft's description of `NTVDM`, and the version history (9.0 in 2007, 11.5 in December 2012, support ended 31 December 2021).
Falsify: a 64-bit system that starts `TB80RTM` without emulating a 16-bit CPU, or a single-step of this engine on XP that sees object `+0x0F` as clearly as Windows 98.

### WV-029

The books in this project were written by Nida Corporation for Navy electronics training. Outside that set, published accounts name other training users of the authoring tools: Northrop Grumman on Air Force aircrew lessons, a 1997 Navy demonstration aboard the USS Princeton, a Pensacola course-designer pilot, an Air Force course-management pilot at Randolph, Buckman Laboratories' learning center, and a UK user conference in 1994. A marketing line that lists AT&T, Lucent, MetLife, and Boeing is not a customer list. There is no present market for new ToolBook courses. Support ended in 2021.

Evidence: the lesson copyright pages, and the sources in `audience.md`.
Falsify: a current vendor offering ToolBook 8.1 authoring, or one of those marketing names documented as an 8.1 runtime site.

## Thought

### WV-007

Disk records are the compact form. `CDBFILEOPEN` (`TB80BAS` ordinal 8) is what expands them. Bounds end up at object `+0x0F`. The six words after `14 00 00 01` are not those bounds. The instruction that performs the write has not been found, which is why placement still fails.

Evidence for the shape of the claim: live bounds at `+0x0F`, and no bounds in the six words. The expansion itself has not been traced.
Falsify: a trace of ordinal 8 that never writes `+0x0F`, or those six words turning out to be the rectangle.

### WV-015

A click does not call a function pointer stored in the book. The runtime sends a message and walks until a handler takes it: object, group, page, background, book, then the system books in list order, then `TB80RTM`. `forward` continues the walk. Reader and Author are a mode, not a second engine. `TB80EDT.DLL` is named by the runtime and is not in this set.

This is the published ToolBook model, and it matches the export names. It has not been single-stepped through a lesson click.

Falsify: a click that jumps to an address stored in the page record, or a lesson that runs with no system book and still draws the Nida chrome.

### WV-016

The system books (`tb80r.sbk`, `tb80hyp.sbk`, `tb80anm.sbk`, and the four `nc81*.sbk` files) are ordinary books. They hold the shell: path, quiz, and status behavior that is not in `TB80RTM`. Their handlers have not been fully read. `linkDLL` modules (`TBDLG`, `TBWIN`, `TBDOS`, `TBREG`, `TBDB3`, `TBPDX`, `TBCBT`) are not loaded unless a handler asks.

Falsify: a system book whose stream is not a book, or a lesson open that loads `TBCBT` before any script runs.

### WV-008

The Unicorn host is a CPU with a fake Win16. A stub that returns `AX=1` is not a successful call. Callers use that value as a handle, a pointer, or an `HRESULT`, and the next fault is the lie. Getting further means returning a real object for each imported ordinal, and entering `TB80RTM` only after the loader has set the data segment, the stack, and the startup parameters. ToolBook's own DLLs should stay real code. `KERNEL`, `USER`, and `GDI` have to be implemented for the ordinals the imports name.

The host is already that design, done one crash at a time. That is why it looks busy and does not open a book.

Falsify: a host that returns `AX=1` for the remaining ordinals and still opens `9e954d01.tbk`.

### WV-017

Later live `80 65` objects were seen with `14 00 00 02`. The working idea is that the last byte is a flag, and `02` is a live value that is not the disk value `01`. What the flag means is not known.

Evidence: the live dumps that showed `02`. No dump has shown the old `11`.
Falsify: a live object whose header is `01` after `CDBFILEOPEN`, or a meaning for `02` that the painter ignores.

### WV-030

The practical audience now is whoever still has a native 8.x book and cannot open it on 64-bit Windows. That includes other CBT discs from the same era, not only these lessons. It does not include people choosing an authoring tool.

Evidence: WV-028, and the absence of a product. This remains a thought until a second corpus is actually in hand.
Falsify: a native 8.x book that opens on 64-bit Windows without a 16-bit emulator, or a second corpus that does not use the same shell.

## Conjecture

### WV-009

If an 8.1 lesson opens in ToolBook 11.5, the hidden save will write a `.TBKX` whose XML is the same pages, objects, bounds, and scripts the `80 65` records hold. That would document the property block the 8.1 parser does not yet expand. It has not been tried. JohnDoe1 had not sent the steps as of 2026-10-12.

Falsify: 11.5 refusing the lesson, or a `.TBKX` that has the media and does not have the object tree.

### WV-018

The DOS exit 0 in the `a106ef0` log is the C runtime leaving because some later init check failed. It is not a successful run that had nothing left to do. The product-name stub removed one empty-string exit. It did not finish startup.

Falsify: a log of that same path that paints a window or calls `CDBFILEOPEN` on the lesson before it exits.

### WV-019

Picture widgets can be tied to the carved BMPs from the disk records alone, once WV-007 is known. Until then, carving a BMP does not place it.

Falsify: a placement found without the expanded property block, or an expanded block that still has no picture id.

## Not true

These stay here so they are not discovered again.

### WV-010

On-disk `14 00 00 01` becomes `14 00 00 11` in memory.

Believed because an early note compared a disk record with a SoftICE view of object 0. Not confirmed. Later live objects used `14 00 00 02`. Do not treat `11` as the expanded form. Retired 2026-10-12.

### WV-020

`AX=1` from an unknown Win16 ordinal means the call worked.

It means the stub had nothing else to return. `CreateDispTypeInfo` failed because `1` is not a pointer. A pointer of `0000:0001` was called as code. Retired 2026-10-12.

### WV-021

An unmapped read can be continued by mapping the page and filling it with `0xCB`.

That filler is what `les` then loads as a selector, and the selector is not in the descriptor table. The `int 0xd` at `0x20:0x36c` was this, not a new fact about ToolBook. Retired 2026-10-12.

### WV-022

The files `bmp_bitmap.txt`, `dll_probe.txt`, and `scaffold.txt` inside a pipeline debug folder are a probe of the machine that just ran.

They are copies shipped in the package. One of them still names a path from the machine that first wrote it. Retired 2026-10-12.

### WV-023

Stage 2 opened the book whenever `render.html` exists.

Stage 2a runs because stage 2 did not open the book. The reader is the fallback. The line `engine opens none` is the engine result. Retired 2026-10-12.

### WV-024

The system books are only resource containers and hold no OpenScript.

They are books. Their scripts are unread, not absent. The claim that they contain no handlers is not supported. Retired 2026-10-13.

### WV-025

Passing the entry fault means the book opened.

The `a106ef0` host passes the old fault and exits without opening the lesson. A longer call list is not an open. Retired 2026-10-13.

### WV-026

The product-name string in the host was recovered from `TB80UTL`.

The host writes a non-empty placeholder so the empty-buffer check does not exit. The id `0x0FBD` is the claim. The letters in the placeholder are not. Retired 2026-10-13, at the same time the stub was written, so it would not be promoted later.
