Here's the explanation in simple terms:

Rc<T> (Reference Counted): It is used to share ownership of a value between parts of your program. Every time you call clone on an Rc, it increases a counter to track how many owners the value has. When an owner is done using it, the counter goes down. If the counter reaches zero, the value is dropped (freed from memory).

Problem with Rc<T> in Threads:
Rc<T> is not designed to work safely with multiple threads at the same time because it doesn't use any safety mechanisms to prevent two threads from modifying the reference count at the same time.
Imagine two threads both trying to increase or decrease the counter at the exact same moment. This can lead to:

Incorrect counts: The counter could become wrong, leading to memory being freed too early (causing crashes) or never being freed (causing memory leaks).


What We Need:
We need a type like Rc<T> that works safely across threads. This is where Arc<T> (Atomic Reference Counted) comes in.



---

Arc<T> Solution:

Arc<T> works just like Rc<T>, but it uses atomic operations to manage the counter.

Atomic operations are special kinds of operations that ensure no two threads can change the counter at the same time.


This makes Arc<T> thread-safe, so you can use it safely when sharing data between threads.


In short, use Arc<T> instead of Rc<T> whenever you need to share ownership of data between threads.
