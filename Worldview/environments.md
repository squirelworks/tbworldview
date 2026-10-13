# What ToolBook 8.1 thinks the machine is

A tangent on Win16, Windows 98, and Windows XP. Graded claims from it are WV-027 and WV-028. This file can wander. The ledger cannot.

## The split personality

ToolBook II 8.1 is not one program. `TB80RUN` is a 32-bit process. It starts `TB80NET`, which loads `TB80RTM`, and the book is interpreted by 16-bit NE code. The 32-bit half exists to satisfy a 32-bit shell. The lesson runs in the 16-bit half.

That is why a startup inventory can list `USER32`, `GDI32`, and `KERNEL32` as absent and still be a normal run. Those are the 32-bit names. The engine calls `USER`, `GDI`, and `KERNEL` by 16-bit ordinal. `USER.266` is `SetMessageQueue` in that table. A Win32 `SetMessageQueue` is a different function in a different DLL, and 8.1 does not call it.

## Win16, as the runtime sees it

Windows 3.x, and the Win16 layer that survived inside later Windows, gives a program three modules and a loader.

`KERNEL` owns the module list, the global heap, selectors, files, and resources. `USER` owns windows, classes, and the message queue. `GDI` owns pens, brushes, bitmaps, and the calls that actually paint. ToolBook's own DLLs sit beside those, not instead of them. `TB80BAS` can draw a polygon only by asking `GDI` to draw it. The live trace of P430 is a `GDI` `Polygon`, not a private framebuffer.

The loader's contract matters more than any single ordinal. Before the NE entry runs, a real Windows has already done `InitTask`: the data segment is `DS`, the stack is the one in the NE header, and the parameters are the instance, the previous instance, the command tail, and `nCmdShow`. The entry at segment 2 offset `0x35A` was written for that moment. Jumping there with empty registers is not starting ToolBook. It is starting the CPU at an address.

Inside that world, pointers are selector:offset. A local heap lives in the automatic data segment. A global allocation returns a handle that is not a pointer until it is locked. An ordinal that returns `1` has not returned a selector. The caller will load it and fault, and the fault will look like a new discovery about ToolBook. It is a discovery about the stub.

16-bit Windows is cooperatively scheduled, and on the systems that still have it, the Win16 programs share one box. One of them that does not pump messages stalls the others. ToolBook's message walk (object, group, page, background, book, system books) sits on top of that `USER` queue. It is not a second scheduler.

The files it looks for are also from that era. The host has seen it open `C:\TOOLBOOK\TB80.INI` and `C:\WINDOWS\asym\runtime\TB80.INI`, and the language file `TB80enu`. Those are Windows locations, not arguments the pipeline passed. On a real machine they are the install. In the host they are strings the code built because it believes it is running under Windows.

## Windows 98

Windows 98 is still a DOS machine with the 16-bit user and GDI in the same world as the 32-bit thunks. A 16-bit segment is a segment the CPU is using, not a program being interpreted by `NTVDM`. SoftICE can set a breakpoint on `TB80BAS` and see the object at `+0x0F`. That is why this project treats Windows 98 as the decompile floor. It is not nostalgia. It is the last common system where the runtime and a 16-bit debugger occupy the same machine without a second kernel in between.

The 32-bit launcher still matters there. `TB80W32` and `TB80W16` are the two ends of a flat thunk. On Windows 98 that thunk is the system's own. The lesson does not become a 32-bit program because the launcher was one.

## Windows XP

Windows XP is a different kernel. 16-bit code runs only inside `NTVDM`. Microsoft describes that process as one DOS environment, plus a Windows 3.x face: 16-bit `KERNEL`, `USER`, and `GDI`, and BIOS calls the subsystem answers. On a 32-bit XP the old instructions still execute on the CPU. `NTVDM` is not a full PC emulator. It is a virtual machine for the 16-bit environment.

From the book's point of view the ordinals did not change. `Polygon` is still `GDI`. `SetMessageQueue` is still `USER.266`. `TB80.INI` is still a file under the Windows directory. What changed is everything around the call. The message queue is the WOW queue, not the Windows 98 queue. A debugger attached to `TB80RUN` is attached to a 32-bit process. The instruction pointer it can single-step is not the one inside `TB80RTM`. That matches the working rule here: XP is the preferred machine for running a course, and it is a bad machine for stepping the engine. Windows 98 is the other way around.

64-bit Windows, including 64-bit XP, has no `NTVDM`. The long mode of x86-64 does not run 16-bit code beside 64-bit code. ToolBook 8.1 cannot start there. No compatibility tab fixes it. This is why a WinXP virtual machine, 32-bit, is the practical place to open these lessons, and why a Linux or Win11 host cannot host the engine by loading the DLLs.

ToolBook 9.0, in 2007, dropped that 16-bit engine and shipped as a 32-bit product. ToolBook 11.5 can run on 64-bit Windows because it is not this runtime. Opening an 8.1 book in 11.5 would be a different program reading an old file. It would not be `TB80RTM` on a newer desktop.

## What not to conclude

A course that runs on XP has not proved that XP and Windows 98 present the same object at `+0x0F`. The painter was confirmed on Windows 98. The pipeline's Unicorn host is a third environment, and it is the least faithful of the three: it has the instruction set and a growing set of lies about `KERNEL`. A behavior seen only in the host is a behavior of the host until it is seen on Windows 98 or XP.
