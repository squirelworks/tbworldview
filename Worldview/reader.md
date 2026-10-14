# The reader and the three hosts

Written 2026-10-14 for the `1.0.0r2` print. The detail lives in [TBpipeline](https://github.com/squirelworks/TBpipeline) at `93ce117`, in `documentation/`. This file is the claim. That directory is the contract. Do not keep a second copy of it here.

## Two questions

They do not have the same answer, so they do not share one program.

What is in the book? Pages, field text, pictures that are already Windows bitmaps, curves, and the script a click runs. That is the reader, `tbpipeline_bf` at `1.0.0r6`. It does not need Windows.

What does the real runtime do? Where a widget sits, how a compressed picture becomes a bitmap, and what a handler does past a page jump. The hosts exist to call those functions. A crash log is not an answer. `AX = 1` is not an answer.

The reader is the product. The host is the instrument. A window that is ToolBook is still the long goal. A normal run is not supposed to produce it.

## What a click does now

`--open` does not start the CPU. It expands each handler against the whole file. A lesson stores `to handle` as text. A system book stores the name as keyword opcodes: `80 81` for `to handle`, `80 90 AD` for `to get`.

`nida81.tbk` is 43 pages and 100 handlers whose names are still plain text. `P20000` is:

```
to handle buttonClick
  system lastbook
  lastbook = name of this book
  system nextpage
  nextpage = name of this page
  go to page "P20001" of book "nida81.tbk"
end
```

The runner follows that. The click lands on `P20001` because the handler says so. A later page that says `go to page (nextpage) of book (lastbook)` comes back the same way. That is the button in `render.html`. It is not the rest of OpenScript. A quiz still does not score.

`render.html` is written only with `--dbgrender`. A launch that exits does not draw a page. The `1.0.0r1` print said otherwise, because the reader of that week wrote the page on every run.

## The bytes, in one table

| Range | Meaning |
|---|---|
| `00`–`7F` | A literal byte |
| `80`–`BF` | A keyword from `TB80CMP` resource `0x77` |
| `C0`–`DF` | A back-reference into the compressed file |
| `E0`–`FE` | A newline or a run of spaces |
| `FF` | The next byte, repeated |

A back-reference copies bytes from the compressed input. The distance often reaches into an earlier handler. That is how the compressor stores a second copy of `INT ` or `linkDLL "`. It is not a call. The call, when there is one, is still the name written in the source. A `?` left in the text is the character `?`.

## Three hosts, and what each is allowed to claim

| Tree | What it may say |
|---|---|
| `tbpipeline_bf` | The reader, plus a host that returns `AX = 1` for a call it does not know and keeps going |
| `tbpipeline_newl` | The same modules on a CPU. An unknown `KERNEL` or `USER` call stops the run |
| `tbpipeline_neww` | One address space. An ordinal with no contract stops the run. The pop size is not guessed |

They stay separate. A shared Windows table would make a lie in one look like a fix in the others.

`bf` pass 06 still asks for `TB80.INI` and `TB80enu`, then DOS exit 0. The entry never asks for the book. `newl` dies earlier, inside startup, on a scan of an empty block. `neww` has done the least of the actual startup, and that is intentional.

`TB80RTM` is a self-loading NE image. Segment 1 is the unpacker. The host runs that stub once and then enters at segment 2 offset `0x35A`. Before that jump, pass 06 calls six DLL entries, in the order they can be called: `TB80RCR`, `TB80LNL`, `TB80OLE`, `TB80W16`, `TB80UTL`, then `TB80BAS`.

## Still missing

Place one widget. P430 is decoded and not on the page. Stop or finish the runtime open: either `OpenFile` returns the book, or the call that refuses it is named. One compressed bitmap: `TB80BMP` ordinal 3 still stops at the far call `0x1FCD`.

[openscript_analysis](https://github.com/squirelworks/openscript_analysis) is a sibling repository. It is not a fourth tree.
