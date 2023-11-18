# Global Interpreter Lock

= mutex

A mutex is an object in programming that acts as a lock that is used to negotitate the mutual exclusion among threads
--> Only one thread can hold the python interpreter at a time
--> This leads to difficulties for CPU-bound and multithreaded programms

## Memory management in python
- Python uses reference counting for memory managment:
    - Objects in python have a reference counting value
    - If the counting value reaches zero, python releases the memory occupied by the object
````python
import sys

a = 5
sys.getrefcount(a)
````
- This count variable needs protection from race conditions, where two threads increase or decrease its value simultaneously
- This can lead to:
    - leaked memory that is never released
    - or worse, incorrectly release the memory while a reference to that object still exists (-> leads to crashes or weird bugs)

- The reference count can be kept safe by adding a lock to every data structure that are shared across threads, so the counts are not modified inconsistently.

- But this leads to another problem: Deadlocks (= multiple locks)
-> side effects: Decreased performance through permanent acquisition and release of locks


The __GIL__ is a lock on the interpreter itself, that means whenever a thread ones to execute python byte code it has to acquire the interpreter lock

Also used in Ruby

Other tools for thread safe memory managment is e.g. garbage collection.

## Reason to use the GIL in python
- for existing C libraries different python extension were written to provide needed functionality to the python developers
- to prevent incosistent changes, these extensions needed a thread-safe memory management (which the GIL provides)

## Multi-threading in python
- Differ between CPU-bounded tasks which require a lot of cpu like mathematical computations 
- and I/O-bounded programms like file, database or network operations

CPU-bounded tasks:
- multithreading leads to same or even higher runtimes compared to single threaded execution since the execution of the programm becomes single-thread due to the lock
- the higher runtimes result from the acquistion and release overhead of the lock

I/O-bounded tasks:
- GIL does not have impact on the performance on I/O-multi-threaded programs as the lock is shared between the locks while waiting for the I/O.

## Removing the GIL?
- it is possible and it has been done multiple times in the past
- but they always broke the existing C extensions
- there are other solutions then the GIL but the result in a performance drop for single-threaded and mulithreaded I/O-bounded programs

## Dealing with the GIL
### Multiprocessing
Multiprocessing defines a way of creating several processes with each having its own python interpreter and memory

Advantage:
- CPU-bounded tasks can be run more effecient

Disadvantage:
- process management leads to overheads -> multiple processes are heavier than multiple threads

### Alternative python interpreters
- Python has several interpreter implementations like CPython, Jython, IrohPython PyPy
- GIL only exists in CPython
