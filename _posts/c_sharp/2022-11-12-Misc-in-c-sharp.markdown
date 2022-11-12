---
layout: post
title:  "C# Miscellaneous"
categories: c#
---

### Base64
```
// Convert to base64.
var plainBytes = System.Text.Encoding.UTF8.GetBytes("1");
string base64Str = System.Convert.ToBase64String(plainBytes);

// Convert from base64.
var base64Bytes = System.Convert.FromBase64String(strBase64Encoded);
string result = System.Text.Encoding.UTF8.GetString(base64Bytes);
```
