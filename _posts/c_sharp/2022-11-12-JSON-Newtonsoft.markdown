---
layout: post
title:  "JSON Newtonsoft Related"
categories: c#
---

### String to JObject
```
JObject o = JObject.Parse("{'a': 3}");
string token = (string)o["token"];
```
