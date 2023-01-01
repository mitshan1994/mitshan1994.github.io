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

### Events
The common language runtime's(CLR) event model is based on *delegates*. A delegate is a type-safe way to invoke a callback method.

Here are basic steps to define an event in a type:
1. Define a type that will hold any additional information which will be sent to receivers. By convention, `System.EventArgs` is extended, and the name of class should be suffixed with `EventArgs`.
2. Define the event member, using C# keyword **event**. Accessibility, a type of delegate indicating the prototype of methods, and a name should be given when defining. For example: `public event EventHandler<NewMailEventArgs> NewMail;`
3. Define a method to raise the event.
4. Define a method that translates the input into the desired event.

### Delegates
.NET exposes a callback function mechanism by using *delegates*.

Delegates can also be used to callback instance methods. The instance object will be remembered, and passed as implicit **this** to the instance method when invoking delegates.

