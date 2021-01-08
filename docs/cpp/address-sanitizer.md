# Address Sanitizer

AddressSanitizer (ASan) is an algorithm that can help developers identify memory safety issues in their C and C++ programs. Developers can use the /fsanitize=address flag to add ASan instrumentation to their native programs. At runtime, the ASan runtime replaces common allocation and memory manipulation functions with instrumented versions. Together, the instrumentation and ASan runtime enable developers to gather useful information about any memory safety issues that are encountered during execution.

## Introduction  - Developer Message

The Address Sanitizer is a compiler based technology [introduced by Google](https://www.usenix.org/conference/atc12/technical-sessions/presentation/serebryany).  This compiler and runtime technology has become the "defacto standard" in the industry for finding memory safety issues. We now offer this technology as a fully supported feature in Visual Studio. We shipped this (and up streamed our ASan runtime changes) to accommodate our customers with a simple re-compile strategy. Testing your build from this simple re-compile, can dramatically increase correctness, portability and security. If your existing code compiles with our current Windows compiler, then it will compile with the flag -fsanitize=address under any level of optimization and all other compatible flags (e.g., /RTC and is not currently compatible)

Microsoft recommends using the Address Sanitizer in **three workflows**:

- **Developer inner loop**
    - Visual Studio - Command line
    - Visual Studio - Project system with integrated IDE error reporting support.

    
- **CI/CD** - integrate, build and run unaltered, existing test assets
    - CMake
    - MSBuild

- **Fuzzing** - Use the supported libFuzzer build
    - [Azure OneFuzz](https://www.microsoft.com/security/blog/2020/09/15/microsoft-onefuzz-framework-open-source-developer-tool-fix-bugs/)
    - Local Machine

You can customize your builds as covered in this section

- **Customization**
    - Enhance - Hooking your allocators
    - Remove - ASan at function of variable granularity

There's extensive documentation for these platform dependent implementations of the Address Sanitizer technology.

- [Google](https://clang.llvm.org/docs/AddressSanitizer.html)
- [Apple](https://developer.apple.com/documentation/xcode/diagnosing_memory_thread_and_crash_issues_early)
- [GCC](https://gcc.gnu.org/onlinedocs/gcc/Instrumentation-Options.html)

This document will cover support for the above workflows on the Microsoft Windows 10 platform. We start with a simple command line use of the compiler and linker. We build upon that simple introduction following the structure of the outline seen in the **three workflows** listed above.

For a complete indexed catalogue of errors this re-compile will find, see the [IDE support for errors](#ide-support-for-errors) section.

**NOTE:** Current support is limited to x86 and AMD64 on Windows10. There is no support for -fsanitize=thread, -fsanitize=leak, -fsanitize=memory or -fsanitize=hwaddress.

## Command line

Adding the flag -fsanitize=address to your command line (with support to emit debug information) is all you need to compile,link and run an .EXE.

             `cl -fsanitize=address /Zi file.cpp file2.cpp my3dparty.lib /Fe My.exe`

The flag -fsanitize=address is compatible with all existing C or C++ optimization flags (e.g., /Od, /O1, /O2, /O2 /GL and PGO)

**-fsanitize=address**

Enable the injection of instrumentation code that inter-ops with the Asan runtime binaries that are automatically linked to the binary you are producing. This is a fast memory safety detector that just requires a recompile. Loads, stores, scopes, alloca, and CRT functions are hooked to detect hidden bugs like out-of-bounds, use-after-free, use-after-scope etc.. See [Error reporting](#error-reporting) for a complete list of errors currently detected at runtime.

Unlike Clang/LLVM this option enables -fsanitize-address-use-after-scope by default and it can not be turned off at the command line or through the [Runtimes](#runtimes) ASAN_OPTIONS environment variable. The functionality for use-after-return requires code generation under an additional flag and environment variable.

By default (legacy reasons) the CL driver infers the linker flag [/MT](https://docs.microsoft.com/en-us/cpp/build/reference/md-mt-ld-use-run-time-library?view=msvc-160) and that will link **static versions** of both the Asan and CRT libraries. If you want to link to the **dynamic version** of the CRT then use the following:

             `cl -fsanitize=address /Zi /MD file.cpp file2.cpp my3dparty.lib /De My.exe`

See the [Linker](#linker) section for details on more complex build scenarios. See the [runtime](#runtimes) section for an inventory of the Asan runtime libraries.


**-fsanitize-address-use-after-return**

Create the coded generation that will create a dual stack frame in the heap.  The stack frame in the heap will linger after the return from the function that created it. If an address of a local, allocated to a slot in the frame in the heap, then the code generation can later determine a stack-use-after-return error.

             `cl -fsanitize=address -fsanitize-address-use-after-return /Zi file.cpp file2.cpp my3dparty.lib /De My.exe`

This is an opt in code generation which is much slower than just using -fsanize=address.  

## Visual Studio

### Project System

### Error reporting

## Compiler tool set

### Compiler

### Linker
[Notes on linker behavior](https://microsoft.sharepoint.com/teams/DD_VC/_layouts/OneNote.aspx?id=%2Fteams%2FDD_VC%2FShared%20Documents%2FVisual%20C%2B%2B%20Team&wd=target%28BE%20Team%2FSecurity%2FCompiler%20Security%20V-Team.one%7CC2A34F56-6B09-4AB1-869B-DFD77BFD7399%2FNotes%20about%20vcasan%20and%20%5C%2Finferasanlibs%7C6D1BD27A-F55A-44BC-BF7C-AF6404C4C5C1%2F%29)

## Address Sanitizer Runtimes

This implementation of AddressSanitizer makes use of the Clang ASan runtime libraries. The runtime library version packaged with Visual Studio may contain features that are not yet available in the version packaged with Clang.

### Runtime features overview
A complete overview of the features and design of the runtime is available here: [AddressSanitizer runtime overview](address-sanitizer-runtime.md)

### Static (x86,AMD64)
### Dynamic (x86,AMD64)
### Runtime Flags

Pick only the set we currently support
https://github.com/google/sanitizers/wiki/AddressSanitizerFlags#run-time-flags 

### Allocators

## Build tools

### CMake

### MSBuild

## Fuzing

### Azure

### Local Machine

## See Also - technical overview

This will cover a deep dive into the implementation and the runtime as a marketing tool to generate interest for the MSDN web page.

