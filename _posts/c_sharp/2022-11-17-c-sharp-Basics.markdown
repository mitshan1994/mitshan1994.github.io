---
layout: post
title:  "C# Basics"
categories: c#
---

### Modifiers
#### readonly
**readonly** modifier can be used in a field declaration. Under this circumstance, assignment to the field can only occur as part of the declaration or in a constructor in the same class. The field can't be assigned after the constructor exits. But the instance it references is mutable, except for value types.


### Nullable Type

#### Null Forgiving Operator (!)
If a variable is a nullable type, and you are sure it's not null when referencing it, **null forgiving operator !** can be used to supress warnings from compiler. For example:
```
string? name;
...
name!.Length;
```

#### Null-Conditional Operator (?.  ?\[\])
A null-conditional operator applies a member access, or element access, operation to its operand only if that operand evaluates to non-null. Otherwise, it returns null.

Null-conditional operator is thread-safe, for the operand is only evaluates once and used.

#### Nullable Value Types
Value types can't be `null`, so C# introduces a feature called `nullable value types` (syntactic sugar, in fact) to satisfy the need.

`??` is called **null-coalescing** operator, which can be used with nullable types to set default values to some variables. It returns the value of left-side nullable type instance if it's not null, or the right-side one.

#### Nullable Reference Types
**Nullable reference types** complement reference types, just as nullable value types complement value types. It can help you express the intent more clearly: some variables must have a value, while some may be null.

Nullable reference types aren't new class types, but rather annotations on existing refrence types. The compiler uses these annotations to help you find potential null reference errors in your code. There's no runtime difference between a non-nullable reference types and a nullable reference type. The benifits are in compile-time analysis.

The compiler tracks the null-state of each reference variable as either *not-null* or *maybe-null*.

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

#### Pre-defined Delegates
There are 17 **Action** delegates defined, like this:
```c#
public delegate void Action();
public delegate void Action<T>(T arg);
public delegate void Action<T1, T2>(T1 arg1, T2 arg2);
...
```
And 17 delegates with return type:
```c#
public delegate TResult Func<TResult>();
public delegate TResult Func<T, TResult>(T arg);
public delegate TResult Func<T1, T2, TResult>(T1 arg1, T2 arg2);
...
```

