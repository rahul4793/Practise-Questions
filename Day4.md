## Q1. What is the difference between creating a thread using Thread, Runnable, and ExecutorService?

Creating a thread by extending the Thread class tightly couples the task logic with the thread itself. Since Java supports single inheritance, extending Thread prevents the class from extending any other class. It is a simple approach but not flexible or reusable, and thread management such as lifecycle control and pooling must be handled manually by the developer.

Using the Runnable interface separates the task from the thread. The Runnable contains only the task logic, while the Thread object is responsible for execution. This approach is more flexible than extending Thread and is preferred when the class needs to extend another class. However, thread creation and management are still manual, and creating too many threads can cause performance issues.

ExecutorService provides a higher-level abstraction for managing threads. Instead of creating threads manually, tasks are submitted to a thread pool, and the ExecutorService decides when and how to execute them. This approach improves performance, resource utilization, and scalability. In real applications, ExecutorService is preferred because it handles thread pooling, task scheduling, lifecycle management, and graceful shutdown automatically, reducing the chances of memory leaks and thread exhaustion.

ExecutorService solves problems such as uncontrolled thread creation, poor resource management, and complex error handling that commonly occur with manual thread creation. It also allows better control over concurrency by limiting the number of active threads.
```
ExecutorService executor = Executors.newFixedThreadPool(2);
executor.execute(() -> System.out.println("Task executed"));
executor.shutdown();
```
## Q2. You submit a task using ExecutorService.submit().

The submit() method returns a Future object. The Future represents the result of an asynchronous computation and acts as a handle to track the task’s execution state. It allows checking whether the task is complete, cancelled, or still running.

The result of the task is retrieved using the Future.get() method. If the task returns a value, get() returns that value once the task finishes execution. If the task does not return anything, get() returns null. The get() method can also throw exceptions if the task execution fails.

If Future.get() is called before the task is complete, the calling thread blocks and waits until the task finishes. This blocking behavior ensures thread safety but can cause performance issues if used improperly, especially on the main thread.

ExecutorService executor = Executors.newSingleThreadExecutor();

Future<Integer> future = executor.submit(() -> {
    Thread.sleep(2000);
    return 10;
});

System.out.println(future.get()); // waits until task completes
executor.shutdown();

## Q3. Why is synchronization required in multi-threaded programs?

Synchronization is required to prevent multiple threads from accessing and modifying shared resources simultaneously, which can lead to race conditions and data inconsistency. Without synchronization, threads may overwrite each other’s changes, resulting in unpredictable behavior and incorrect results.

The wait() and notify() methods are used for inter-thread communication. The wait() method causes the current thread to release the lock and go into a waiting state until another thread invokes notify() or notifyAll() on the same object. The notify() method wakes up one waiting thread, allowing coordinated execution between threads.

These methods must be called inside a synchronized block because they operate on the object’s intrinsic lock. Calling them outside synchronization would break the locking mechanism and lead to IllegalMonitorStateException.

class SharedResource {
    synchronized void produce() throws InterruptedException {
        wait();
        System.out.println("Produced item");
    }

    synchronized void consume() {
        System.out.println("Consumed item");
        notify();
    }
}

## Q4. Why are collections like ArrayList and HashMap not thread-safe?

ArrayList and HashMap are not thread-safe because they do not use synchronization internally. When multiple threads modify them simultaneously, their internal data structures can become corrupted, leading to inconsistent data or runtime exceptions such as ConcurrentModificationException.

One example of a thread-safe collection is ConcurrentHashMap. It allows concurrent read and write operations by using fine-grained locking instead of locking the entire collection.

ConcurrentHashMap is preferred over Collections.synchronizedMap() when high concurrency and performance are required. synchronizedMap() locks the entire map for every operation, which reduces scalability, whereas ConcurrentHashMap allows multiple threads to operate concurrently on different segments of the map.

ConcurrentHashMap<Integer, String> map = new ConcurrentHashMap<>();
map.put(1, "A");
map.put(2, "B");

## Q5. What is a deadlock in Java?

A deadlock occurs when two or more threads are permanently blocked because each thread is waiting for a resource held by another thread. Since none of the threads can proceed, the application comes to a standstill.

A real-world example of deadlock is two people holding one resource each and waiting for the other to release their resource. For instance, one person holds a pen and waits for paper, while the other holds paper and waits for the pen.

class DeadlockExample {
    static final Object lock1 = new Object();
    static final Object lock2 = new Object();

    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            synchronized (lock1) {
                synchronized (lock2) {
                    System.out.println("Thread 1");
                }
            }
        });

        Thread t2 = new Thread(() -> {
            synchronized (lock2) {
                synchronized (lock1) {
                    System.out.println("Thread 2");
                }
            }
        });

        t1.start();
        t2.start();
    }
}


One way to prevent deadlocks is to enforce a consistent lock ordering. If all threads acquire locks in the same order, circular waiting conditions are avoided. Other strategies include using timeouts, minimizing synchronized blocks, and using higher-level concurrency utilities such as locks from java.util.concurrent.
