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

### WV-048

The public ToolBook Knowledge Base hosted by Denny Dedmore at https://dennydedmore.com/toolbook/knowledgebase/kb/ records that no new activations are possible after 31 December 2021, that 11.5 Patch 4 (4 August 2015) supersedes earlier patches and fixes several DHTML and browser issues, and that the Repair Kit is the preferred recovery for a corrupted book. These are historical support facts. They do not change the 8.1 binary findings.

Evidence: the pages summarized in `SME/kb-notes.md` and `Worldview/2026-10-19-kb.md`.
Falsify: an activation server that still issues new keys, or a Patch 4 that does not contain the listed fixes.

(The complete prior Known / Thought / Not true entries from 1.0.0r3 remain in repository history and are restored in full in the next commit if this shortened form is insufficient. For the r4 print the new entry is the delta.)
