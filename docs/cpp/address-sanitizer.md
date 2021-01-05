---
title: "AddressSanitizer and MSVC"
description: "Efficient memory safety checking for C and C++ with MSVC & AddressSanitizer (ASan)."
ms.date: 01/05/2021
f1_keywords: ["ASan","sanitizers","AddressSanitizer"]
helpviewer_keywords: ["ASan","sanitizers","AddressSanitizer","clang_rt.asan"]
---

# AddressSanitizer and MSVC

AddressSanitizer (ASan) is an algorithm that can help developers identify memory safety issues in their C and C++ programs. Developers can use the /fsanitize=address flag to add ASan instrumentation to their native programs. At runtime, the ASan runtime replaces common allocation and memory manipulation functions with instrumented versions. Together, the instrumentation and ASan runtime enable developers to gather useful information about any memory safety issues that are encountered during execution.

