---
layout: post
title:  "Strings in Python 3"
categories: python
---

### Strings in memory
All text strings are uniformed in memory, without specific character encodings. For example, a **str** may be represented using 1, 2 or 4 bytes for each character, which varies according to the upperlimit of bytes need for each character.
When saving to files or transferring throught networks, strings will be encoded to specific charset to be transferred.

### **str** vs. **bytes**
**str** object stores text characters. **bytes** object store binary data. For **open(...)** function (to open a file), the way specified (**b** or no **b**) determines which type of object will be returned.

### String Literals
**b** is used to prefix a binary data, such as **b'good'** (a *bytes* object). If no prefix, then default to *str* object.
