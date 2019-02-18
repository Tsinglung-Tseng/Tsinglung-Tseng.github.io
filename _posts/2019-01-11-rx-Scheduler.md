---
title: rx Scheduler
key: 20190111
tags: RX
---

Introduce multithreading into your cascade of Observable operators

## SubscribeOn and ObserveOn

In the Rx world, there are generally two things you want to control the concurrency model for:

* The invocation of the subscription 对订阅的调用
* The observing of notifications 对通知的观察

The SubscribeOn operator changes this behavior by specifying a different Scheduler on which the Observable should operate. 

The ObserveOn operator specifies a different Scheduler that the Observable will use to send notifications to its observers.

subscribeOn()操作符不管在操作链的哪个位置被调用，都将指出被观察者开始执行的那个线程。observeOn()操作符则相反，影响被观察者随后将使用的操作符所在线程。为此，可能在操作链不同的地方调用observeOn()操作符多次，来改变那些操作符执行的线程。

```python
rx.Observable.start_async(work)
```