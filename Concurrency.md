# Concurrency Concepts #

- [Volatile vs Atomic variables](#volatile-atomic)
- [Thread Local](#thread-local)
- [Parallelism vs Concurrency](#parallelism-concurrency)


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
