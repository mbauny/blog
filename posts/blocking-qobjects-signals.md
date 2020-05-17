---
title: 'Blocking QObjects signals'
date: 2019-11-05
tags: [C++, Qt]
---
# Blocking QObjects signals

## Motivation

Blocking a QObject's signals is a thing that you seldom need to do but that will sometimes be your only valid choice. As trivial as it seems, this is something that my formers colleagues and I have, for a long time, done incorrectly. I hope this post will help you learn from our past mistakes!

## How to do it

### The bad way

What naturally comes to mind first is to do something like this:

```cpp
// SPOILER: This might be DANGEROUS, depending on the context
void myFunction() {
    QObject myObject;
    myObject.blockSignals(true);
    // ...
    // insert here some code that relies on signals being blocked
    // ...
    myObject.blockSignals(false);
}
```

It is quite unlikely that something goes wrong here because _myObject_ is a local object and you have a clear vision on its life-cycle. However, most of the time, you will be dealing with objects coming for another scope, like in the following example:

```cpp
// SPOILER: This is WRONG
void myFunction(QObject* object) {
    object->blockSignals(true);
    // ...
    // insert here some code that relies on signals being blocked
    // ...
    object->blockSignals(false);
}
```

Still feels natural, right? But what would happen if someone were to use our function and block signals in the same way that we do?

```cpp
// SPOILER: This is WRONG
QObject myObject;
myObject.blockSignals(true);
// ...
// insert here some code that relies on signals being blocked
// ...
myFunction(&myObject);
// ...
// insert here some code that relies on signals STILL being blocked
// although they are NOT blocked anymore!
// ...
myObject.blockSignals(false);
```

Once _myFunction_ returns, we continue to work with _myObject_, assuming that its signals are still blocked whereas the function has unblocked them behind our back! 😱

### The good way

Qt has one of the best [documentation](https://doc.qt.io/qt-5/reference-overview.html#) around; let's see what it has to say about [_QObject::blockSignals(bool block)_](https://doc.qt.io/qt-5/qobject.html#blockSignals) (emphasis mine):

> _If block is true, signals emitted by this object are blocked (i.e., emitting a signal will not invoke anything connected to it). If block is false, no such blocking will occur._
>
> **_The return value is the previous value of signalsBlocked()._**

You see here that _blockSignals_ has a return value. It tells you whether signals where blocked before your call and thus to what state you are expected to restore the object once you are done.

The correct way to implement _myFunction_ would therefore be something like that:

```cpp
// SPOILER: This is RIGHT
void myFunction(QObject* object) {
    const bool blockedBefore = object->blockSignals(true);
    // ...
    // insert here some code that relies on signals being blocked
    // ...
    // restore previous signals state
    object->blockSignals(blockedBefore);
}
```

This may seem a bit fastidious but since Qt 5.3, we can take advantage of the very handy [_QSignalBlocker_](https://doc.qt.io/qt-5/qsignalblocker.html) class:

```cpp
// SPOILER: This is even MORE RIGHT
void myFunction(QObject* object) {
    const QSignalBlocker blocker(object);
    // ...
    // insert here some code that relies on signals being blocked
    // ...
    // no need to manually unblock signals
}
```

Naturally _myFunction_'s callers should also rely on these same tools:

```cpp
QObject myObject;
const QSignalBlocker(myObject);
// ...
// insert here some code that relies on signals being blocked
// ...
myFunction(&myObject);
// ...
// insert here some code that relies on signals being blocked
// ...
```

## Take away

1. Avoid blocking signals manually and use _QSignalBlocker_;
2. Consistently refer to Qt's documentation;
3. _QSignalBlocker_ has an [_unblock_](https://doc.qt.io/qt-5/qsignalblocker.html#unblock) and a [_reblock_](https://doc.qt.io/qt-5/qsignalblocker.html#reblock) method that you might find useful.
