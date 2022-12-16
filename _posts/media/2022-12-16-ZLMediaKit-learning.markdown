---
layout: post
title:  "ZLMediaKit Learning Notes"
categories: media
---

This is a note on zlmediakit, reading/learning its code.

## zltoolkit

### Some Classes

#### AnyStorage
`AnyStorage` is an unordered map, whose key is `std::string` and value is `Any`. It's mainly used to store some attributes.

#### NoticeCenter
`NoticeCenter` class is used as an event dispatcher, synchronously. The event type is `std::string`. Interested events can be listened, and registered function callbacks will be called when the event emitted.

#### TaskCancelableImp
`TaskCancelableImp` is a template class, which represents a cancelable task (with a `cancel()` interface). The task can be canceled by setting the strong shared_ptr to nullptr, which normally store the callback function.
```c++
using TaskIn = std::function<void()>;
using Task = TaskCancelableImp<void()>;
using DelayTask = TaskCancelableImp<uint64_t(void)>;
```
`TaskIn` is a normal function object and used to wrap a function object/pointer. `Task` is the scheduling unit in zltoolkit. zltoolkit will convert `TaskIn` into `Task`, and schedule it to correct thread. `DelayTask` is a task with a time period which represents after how much time is should be executed.

#### TaskExecutor
`TaskExecutor` inherits `ThreadLoadCounter` and `TaskExecutorInterface`. It can execute tasks and calculate loads.

#### TaskExecutorGetterImp
`TaskExecutorGetterImp` is a schedule class, which contains multiple `TaskExecutor`. It creates `TaskExecutor`, gets loads and info of each `TaskExecutor`, and chooses the idle one for new tasks.

`EventPollerPool` inherits this class.

#### **EventPoller**
`EventPoller` is one kind of `TaskExecutor` (in another word, inherits it), inherits `AnyStorage`.

`EventPoller` adds support for **I/O events**, to support the network needs. Since it extends `TaskExecutor`, tasks can be executed (maybe asynchronously) by it. `DelayTask` support is added.

In conclusion, `EventPoller` do 3 things:
* Execute tasks
* Execute tasks in a later time
* Network I/O event listening (and call callbacks when event triggered).

#### EventPollPool
`EventPollPool` inherits `TaskExecutorGetterImp`, serving as the container of `EventPoller`s. The number of `EventPoller` instances is controlled by it.

It is used as Singleton in zlmediakit.

#### WorkThreadPool
`WorkThreadPool` inherits `TaskExecutorGetterImp`, too. The difference between it and `EventPollPool` is that, the priority is lower for `WorkThreadPool`. And it's mainly used to initiate some tasks, instead of listening and responding.

## MediaServer
**MediaServer** uses **zltoolkit** and **zlmediakit** library, as a standalone media server process, with various functions.

### Starting Procedures

