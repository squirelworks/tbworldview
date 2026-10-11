# ToolBook 11.5 and the TBKX export

Recorded 2026-10-12 from correspondence with JohnDoe1.

JohnDoe1 spent 24 years at SumTotal Systems as a ToolBook technical writer, support technician, trainer, and programmer, created the official ToolBook Knowledge Base, and kept hosting it after the product was ended in late 2021. JohnDoe1 also kept an archive of the installers that were shipped, including 8.1 and 11.5.

The personal name, email address, and website are not in this repository.

## What JohnDoe1 reported

ToolBook 9.5 and later can store a book as `.TBKX`. That file is a ZIP. Inside it are the images and other media, and an XML description of the book: pages, objects, bounds, layers, and properties.

JohnDoe1 said ToolBook 11.5 has a hidden command that writes this ZIP. If an 8.1 lesson opens in 11.5, the export is a documented structure. The 8.1 on-disk records are not.

Two format notes match what is already known about these files:

- An 8.x book stream begins `03 4A 42 4F`.
- Later ToolBook files use a PE wrapper. The NIDA 8.1 shells already do. The first `0xC00` bytes are a PE stub, and the JBO stream starts at `ZZZTBKBOOKLOC`.

## Running 11.5

- 11.5 is 32-bit and runs on 64-bit Windows. ToolBook before 9.0 is 16-bit and does not.
- 11.5 requires MSXML 4. MSXML 6 is not a substitute.
- The last release is the 2012 installer, `TB115.zip`. Patch 4 is applied after install. JohnDoe1 distributes both from the Knowledge Base.
- The first launch starts a fully functional 30-day trial. An activation code is no longer available.
- The trial clock belongs to that Windows installation. The method JohnDoe1 described is a virtual machine: save a clean base image, install 11.5 as the working image, and when the trial ends restore the base and install again.
- A book saved by the trial forever shows "This was created using the Trial Version Of ToolBook" when the runtime opens it. Only a save from an activated copy clears that message. Exporting an existing book was not tested here, so it is not known whether an export counts as that save.

## Status on 2026-10-12

JohnDoe1 had found the archival notes for the path from 8.1 through 11.5 to `.TBKX`, and was testing them before sending the steps. The steps were not in hand when this note was written. No NIDA lesson has been opened in 11.5 yet.

The 8.1 work is unchanged. Page markers, the OpenScript subset, and the polygon painter still stand. `.TBKX` is a second way to read the same books, if 11.5 will open them.

When a machine is free, the test is: install 11.5 in a snapshotted virtual machine, open `9e954d01.tbk`, find the hidden save command, and compare the XML with the `80 65` records.
