---
layout: post
title:  "ZLMediaKit Learning Notes"
categories: media
---

This is a note on zlmediakit, reading/learning its code.

## zltoolkit & zlmediakit

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

##### Async Task Implementation
The `async()`, `async_first()` and `async_l` function will push the task into task queue, and write to inner pipe to wake up task executing event in main loop. Pipe reading event handler is **epolled** and executed.

##### Delayed Task Implementation
Delayed tasks have a map container with delayed time as key and callback as value. Manipulation of this container is always in the it's thread, so  protection of mutex is unnecessary (Pushing task into it is executed asynchronously when necessary).

#### EventPollPool
`EventPollPool` inherits `TaskExecutorGetterImp`, serving as the container of `EventPoller`s. The number of `EventPoller` instances is controlled by it.

It is used as Singleton in zlmediakit.

#### WorkThreadPool
`WorkThreadPool` inherits `TaskExecutorGetterImp`, too. The difference between it and `EventPollPool` is that, the priority is lower for `WorkThreadPool`. And it's mainly used to initiate some tasks, instead of listening and responding.

### Network Classes

* `SockNum`: consisting of a socket file descriptor and socket type.
* `SockFD`: consisting of a `SockNum` and an `EventPoller`.

#### Socket Class
`Socket` class is a big one, consisting of `SockFD`, buffers, `EventPoller`, all kinds of event handlers, and some utilities to record some statistics.

There are two groups of event handlers: inner event handlers and others set by client.

#### Server Class
`Server` class contains nothing but an `EventPoller`. `TcpServer` and `UdpServer` are derived from `Server`.

`Server` extends `mINI` class, which adds parsing config file functions to it and subclasses.

#### SocketHelper Class
`SocketHelper` class extends `SockSender`, `SockInfo` and `TaskExecutorInterface`. So it can send data, get socket information, and execute task sync/async.

`SocketHelper` owns a pointer to an `EventPoller`, so tasks are delegated to the event poller.

#### Session Class
`Session` class extends `SocketHelper`. Main interfaces for sub-classes is:
* `onRecv` for network data reading.
* `onError` for error handding.
* `onManager` for timeout management.

Since `SocketHelper` is extended, it can also send data to remote endpoint.

Subclasses of `Session` includes:
* `RtspSession`
* `HttpSession`
* `RtpSession`
* `WebRtcSession`


## MediaServer
**MediaServer** uses **zltoolkit** and **zlmediakit** library, as a standalone media server process, with various functions.

### Starting Procedures

