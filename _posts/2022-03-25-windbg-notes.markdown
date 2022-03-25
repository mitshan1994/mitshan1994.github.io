---
layout: post
title:  "Windbg Notes"
categories: debug
---

## Synopsis
This post contains some notes on windbg and some basic debug commands and techniques.

## Debug Commands
### !peb
Show information of the computer where the dump file was generated.

### k*
The **k\*** commands display the stack frame of the given thread.
#### kv
Displays frame pointer omission(FPO) information. On x86-based processors, the display also includes calling convertion information.
