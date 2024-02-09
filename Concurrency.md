# Concurrency Concepts #

- [Volatile vs Atomic variables](#volatile-atomic)
- [Thread Local](#thread-local)
- [Parallelism vs Concurrency](#parallelism-concurrency)
- [Locks](#locks)
- [Stopping the thread(s) after a timeout](#stopping-timeouts)


<a id="volatile-atomic"></a>
## Volatile vs Atomic variables ##
````
Volatile: When value of the variable should be shared between different threads then we should use volatile.
For example in case of shared counters.

Atomic : When any operation of the shared variable has to be done atomically to avoid race conditions we should use Atomic variables.
For Example when incrementing and decrements or setting shared variables. Since for normal variables this operation is split between three steps :
 - Read the variable value
 - Modify the value
 - Write the value

For atomic variables all three steps are done in one go in a thread safe manner.
````
<a id="thread-local"></a>
## Thread Local ##
````java
Whenever we want to keep certain variables and its values confined to a particular threads context then we should use Thread Local variables. These variables will be local to a Thread and will remain active till a thread is active.

Ex - private static final ThreadLocal<RequestAttributes> requestAttributesHolder = new NamedThreadLocal<>("Request attributes");

Examples from Spring :
  - RequestContextHolder : Stores values per request thread
  - SecurityContextHolder
````
<a id="parallelism-concurrency"></a>
## Parallelism vs Concurrency ##
````
Parallelism : When we use multiple threads to multiple tasks at the same time.
Concurrency : When we coordinate between multiple threads to access a shared variable or execute a shared task.
````
<a id="locks"></a>
## Locks ##
  - ### ReentrantLock ###
    ````
      - Lock objects in java are explicite compared to synchronized keyword
      - Lock objects can be acquired and released in different scopes(methods)
      - Lock objects provide methods to tryLock() and tryLock(timeout) methods to avoid infinite waiting.
      - ReentrantLock can be acquired multiple times on same object. For example in case of recursive method calls
      - ReentrantLock gives option of fairness or locks, meaning longest waiting thread will get the lock.
    ````
  - ### RewriteReentrantLock ###
    ````
      - Two types of locks can be acquired by RewriteReentrantLock Read Lock and Write lock
      - Any number of read locks can be acquired at a time.
      - Only one thread will be allowed to acquire the write lock.
    ````
<a id="stopping-timeouts"></a>
## Stopping the thread(s) after a timeout #
````
  - Threads are not guaranteed to stop directly by any mechanism once they are scheduled/started.
  - ExecuterService.shutDown() calls interrupt() internally on each thread currently running which does not guarantee stopping of thread.
  - ExecuterService.shutDownNow() calls interrupt() internally on running/scheduled threads which does not guarantee stopping of thread.
  - Even stopping can be attempted by calling interrupt() directly on thread.
  - Timeout can be set by using overloaded schedule() methods by providing the timeout value and unit.
````
