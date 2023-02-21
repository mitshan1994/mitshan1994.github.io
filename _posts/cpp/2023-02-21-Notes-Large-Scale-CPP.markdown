---
layout: post
title:  "Notes on Large Scale CPP"
categories: cpp
---

This is basically reading notes on *Large-Scale C++ Software Design*, by John Lakos.

### Physical Design Rules

* (Major) Logical entities declared within a component should not be defined outside that component.
* (Major) The .c file of every component should include its own .h file as the first substantive line of code.
* (Guideline) Clients should include header files providing required type definitions directly; Except for non-private inheritance, avoid relying on one header file to include another.
* (Major) Avoid definitions with external linkage in the .c file of a component that are not declared explicitly in the corresponding .h file.

### Principles
The DependOn relation for component is transitive.

A component defining a function will usually have a physical dependency on any component defining a type used by that function.

A component defining a class that IsA or HasA user-defined type always has a compile-time dependency on the component defining that type.


### Some Definitions
We can define **implementation dependency** for functions loosely by saying that a function depends on a component if that component is needed in order to compile and link the body of that function.

So we have the definition: **A component y *DependsOn* a component x if x is needed in order to compile or link y.**

**A component y exhibits a *compile-time* dependency on component x if x.h is need in order to compile y.c.**

**A component y exhibits a *link-time* dependency on component x if the object y.o contains undefined symbols for which x.o may be called upon either directly or indirectly to help resolve at link time.**
