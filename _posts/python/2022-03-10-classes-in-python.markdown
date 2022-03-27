---
layout: post
title:  "Classes in Python"
categories: python
---

## Inheritance
Some key ideas behind the machinery of attribute inheritance:
* Superclasses are listed in parentheses in a class header.
* Classes inherit attributes from their superclasses.
* Instances inherit attributes from all accessible classes.
* Each *object.attribute* reference invokes a new, independent search.
* Logic changes are made by subclassing, not by changing superclasses.

## Intercept Operators
Quick rundown of the main ideas behind overloading operators:
* Methods named with double underscores (\_\_X\_\_) are special hooks. Python defines a fixed and unchangable mapping from each of these operation to a specially named method.
* Such methods are called automatically when instances appear in built-in operations.
* Classes may override most built-in type operations.
* There are no defaults for operator overloading methods, and none are required.
* New-style classes have some defaults, but not for common operations.
* Operators allow classes to integrate with Python's object model.
* The **__init__** method, which is known as the *constructor* method and is used to initialize objects' state. You should pay special attention to this method.


