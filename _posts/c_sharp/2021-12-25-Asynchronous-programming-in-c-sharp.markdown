---
layout: post
title:  "Asynchronous programming in C#"
categories: c#
---

### Return types
`System.Threading.Tasks.Task` is used to represent the ongoing async task, which may be `await` later to wait/get the return of the task after it finishes.

### Typical Async Template
`async Task<TResult> Func1()`
or
`async Task Func2()`
where `TResult` is the return type of Func1.

### Exceptions
When async function throws exception, the client code could catch it when the `Task<TResult` is awaited.

### Await Several Tasks
`await Task.WhenAll(task1, task2, task3);`

`await Task.WhenAny(task1, task2, task3);`

### Notes of Reading
* The composition of an asynchronous operation followed by synchronous work is an asynchronous operation.
* When the `await` keyword is applied, it suspends the calling methods and yields control back to its caller until the awaited task is complete.
* `await` can only be used inside an async method.
