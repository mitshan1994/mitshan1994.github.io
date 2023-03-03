---
layout: post
title:  "Practices When Writing C++"
categories: cpp
---

Some practices of writing C++ code.

### Classify Member Functions
When a class interface is big, with many member functions, `#pragma region [text]` and `#pragma endregion [text]` can help to classify them, with collapse support from IDE or editor.

When compiling, these two *pragma*s are just ignored by the compiler.

