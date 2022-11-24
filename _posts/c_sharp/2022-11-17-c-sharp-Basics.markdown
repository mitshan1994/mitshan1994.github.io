---
layout: post
title:  "C# Basics"
categories: c#
---

### Nullable Type

#### Null Forgiving Operator (!)
If a variable is a nullable type, and you are sure it's not null when referencing it, **null forgiving operator !** can be used to supress warnings from compiler. For example:
```
string? name;
...
name!.Length;
```
