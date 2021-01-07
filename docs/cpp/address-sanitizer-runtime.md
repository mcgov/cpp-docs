---
title: "AddressSanitizer and MSVC"
description: "Efficient memory safety checking for C and C++ with MSVC & AddressSanitizer (ASan)."
ms.date: 01/05/2021
f1_keywords: ["ASan","sanitizers","AddressSanitizer"]
helpviewer_keywords: ["ASan","sanitizers","AddressSanitizer","clang_rt.asan"]
---

# AddressSanitizer and MSVC

AddressSanitizer (ASan) is an algorithm that can help developers identify memory safety issues in their C and C++ programs. Developers can use the /fsanitize=address flag to add ASan instrumentation to their native programs. At runtime, the ASan runtime replaces common allocation and memory manipulation functions with instrumented versions. Together, the instrumentation and ASan runtime enable developers to gather useful information about any memory safety issues that are encountered during execution.


## Custom allocators and ASan

ASan provides interceptors for common allocator interfaces, malloc/free, new/delete, HeapAlloc/HeapFree (via RtlAllocateHeap/RtlFreeHeap). Many programs make use of custom allocators for one reason or another. An example would be any program using dlmalloc or a solution using the std::allocator interface and VirtualAlloc. ASan instrumentation does not automatically enlighten these allocators, so it falls to the programmer to 'enlighten' them when using ASan.

### Manual ASan poisoning interface

The interface for enlightening is simple but it imposes alignment restrictions on the user. The interface function prototypes:
`void __asan_poison_memory_region(void const volatile *addr, size_t size);`
`void __asan_unpoison_memory_region(void const volatile *addr, size_t size);`

These functions are not needed when ASan is disabled, the interface header provides wrapper macros that will check whether ASan is enabled during compilation and omit the functions when ASan instrumentation is not enabled. These macros should be used rather than using the above functions directly:
`#define ASAN_POISON_MEMORY_REGION(addr, size)`
`#define ASAN_UNPOISON_MEMORY_REGION(addr, size)`

### Manual ASan poisoning and alignment

ASan poisoning has alignment requirements: the user must add padding such that the padding ends on a byte boundary in the shadow memory. Since each bit in the ASan shadow memory encodes the state of a byte in real memory, this means that **the size + padding for each allocation must align on a 8 byte boundary.** If this requirement is not satisfied it can lead to incorrect bug reporting, including missed and false-positive reports.

See the included examples for a little background. One is a small program to show what can go wrong with manual shadow memory poisoning. The second is an example implementation of manual poising using the `std::allocator` interface.

To build and run the allocator example:

   `nmake allocator && allocator.exe`

To build the 'bad unpoisoning' example:

   `nmake unpoison && unpoison.exe`
