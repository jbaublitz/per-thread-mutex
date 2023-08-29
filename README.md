# per-thread-mutex

Synchronization lock intended for thread unsafe C libraries.

## Rationale

When working with certain C libraries, concurrent accesses are not safe. It can be problematic
to model this at the Rust level largely because language level support can't enforce everything
that's necessary to maintain safety in all cases.

`Send`/`Sync` can ensure that data structures are not used and sent across
threads which provides part of the puzzle. However for certain cases thread-unsafe libraries
can be used in a multithreaded context provided two conditions are upheld.

1. Data structures are thread-localized, meaning any resource that is created in a thread is
   never sent or used by another thread. This can be handled by `Send`/`Sync`.
2. There can be no concurrent calls into the library. This is not addressed by Rust language
   level features.

This crate aims to address requirement 2.

## How is it used?

The intended use of this mutex is with `lazy_static` as a global variable in Rust bindings for
thread-unsafe C code. The mutex should be locked before each call into the library. This
ensures that there are never any concurrent accesses from separate threads which could lead to
unsafe behavior.

## How does it work?

The lock keeps track of two pieces of data: the thread ID of the thread that currently has the
lock acquisition and the number of acquisitions currently active on the lock. Acquisitions from
the same thread ID are allowed at the same time and the lock available once all acquisitions
of the lock are released.

## Why is the same thread permitted to acquire the mutex multiple times?

This largely stems from C's heavy use of callbacks. If a callback is built into a C API, it is
typical in Rust bindings to write the callback in Rust and to write a C shim to convert from C
to Rust data types. Consider the case of an API call that, in its implementation, calls a
callback where the callback also calls a Rust-wrapped API call. This is a safe usage of the
library, but would result in a double acquisition of a traditional mutex guarding calls into
the library. This lock allows both of those acquisitions to succeed without blocking,
preventing the deadlock that would be caused by a traditional mutex while still guard against
unsafe accesses of the library.
