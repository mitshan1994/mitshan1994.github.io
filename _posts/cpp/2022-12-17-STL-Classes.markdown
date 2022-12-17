---
layout: post
title:  "STL Classes"
categories: cpp
---

Mainly about some STL classes and usage.

### Function Adaptors
A function adaptor takes a function as input and returns a function object that can be used to invoke the original function.

#### std::function
A `std::function` is specified by the return type and arguments type. The following can be passed to construct a `function`:
* function pointer
* callable object
* lambda function object


