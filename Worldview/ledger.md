# Ledger

Stable print: 1.0.0r4. The previous print was 1.0.0r3.
The engine notes cite TBpipeline at `93ce117` unless a claim says otherwise. The reader package there is `1.0.0r6`. The decompile notes cite TBdecompile at `9a7d2cf`.

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
| WV-031 | Known | `.tbk` and `.sbk` are the same container. The page name is at offset `0x0B`. |
| WV-032 | Known | OpenScript `C0`–`DF` is a back-reference, not a call. |
| WV-033 | Known | A `buttonClick` on `nida81.tbk` `P20000` goes to `P20001` because the handler says so. |
| WV-034 | Known | `render.html` is written only with `--dbgrender`. |
| WV-035 | Known | The three hosts are not one program, and they do not share a Windows table. |
| WV-036 | Known | Pass 06 still exits after `TB80.INI`. The entry does not ask for the book. |
| WV-041 | Not true | Reader navigation is only a quoted page id. |
| WV-042 | Known | A content rectangle is four twip words, a fill, and the marker `01 00 00 04`. |
| WV-043 | Known | `FUN_12c8_0ef8` copies four words that are not the raw disk record. |
| WV-044 | Known | A page offset table is a content index, not a Group. |
| WV-045 | Known | A gap in the Ghidra HTML is not proof the function is absent. |
| WV-046 | Known | 11.5 with `saveSpecial=true` writes a `.TBK TBX` folder. A `.TBKX` is that folder zipped. |
| WV-047 | Not true | ToolBook 8.1 is a 32-bit program. |
| WV-048 | Known | The public Knowledge Base at dennydedmore.com records post-EOL activation constraints, 11.5 Patch 4, and the Repair Kit. |

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

The clean-room reader walks `80 65` pages and pulls field text. On `9e954d01.tbk` that is 129 pages. It extracts Windows BMPs the book already holds, and it draws curves as separate files. The pictures and the shapes are not placed. Quizzes are scraped text, not scored.

As of TBpipeline `93ce117`, a click is no longer only a quoted page id. See WV-033. The `1.0.0r1` wording of this entry said navigation was the quoted id and not the bytecode. That sentence is retired as WV-041. The reader is still not the engine.

Evidence: a `1.0.0r4` run on `9e954d01.tbk`, and `documentation/openscript.md` at `93ce117`.
Falsify: a page whose bitmap is placed by this reader, or a quiz that scores.

### WV-048

The public ToolBook Knowledge Base hosted by Denny Dedmore at https://dennydedmore.com/toolbook/knowledgebase/kb/ records that no new activations are possible after 31 December 2021, that 11.5 Patch 4 (4 August 2015) supersedes earlier patches and fixes several DHTML and browser issues, and that the Repair Kit is the preferred recovery for a corrupted book. These are historical support facts. They do not change the 8.1 binary findings.

Evidence: the pages summarized in `SME/kb-notes.md` and `Worldview/2026-10-19-kb.md`.
Falsify: an activation server that still issues new keys, or a Patch 4 that does not contain the listed fixes.

## Thought

### WV-007

Disk records are the compact form. `CDBFILEOPEN` (`TB80BAS` ordinal 8) is what expands them. Bounds end up at object `+0x0F`. The six words after `14 00 00 01` are not those bounds. The instruction that performs the write has not been found, which is why placement still fails.

Evidence for the shape of the claim: live bounds at `+0x0F`, and no bounds in the six words. The expansion itself has not been traced.
Falsify: a trace of ordinal 8 that never writes `+0x0F`, or those six words turning out to be the rectangle.

## Not true

### WV-010

On-disk `14 00 00 01` becomes `14 00 00 11` in memory.

Believed because an early note compared a disk record with a SoftICE view of object 0. Not confirmed. Later live objects used `14 00 00 02`. Do not treat `11` as the expanded form. Retired 2026-10-12.

Full prior Known, Thought, and Not true entries from the 1.0.0r3 print are restored here along with WV-048. The complete text is the one in this file.
