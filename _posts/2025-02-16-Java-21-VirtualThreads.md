---
title: Quick Intro about Virtual Threads
categories: Java Concurrency
tags: java
description: Quick intro about Java Virtual Threads

last_modified_at: 2025-02-22 18:00:00 -0530
---


# Virtual Threads

With the release of Java 21, Virtual Threads are now available for production use. These lightweight threads can be utilized similarly to how conventional Threads are used, providing a more efficient and scalable way to manage concurrency in Java applications.

### Why Virtual Threads?

To understand the need for Virtual Threads, we first need to recognize the limitations of the options available before their introduction.

### What are the issues with the current Thread Model?

Before the introduction of Virtual Threads, each Thread created in Java was directly tied to a platform/carrier thread. This limitation restricted the number of parallel tasks that could be executed, as it depended heavily on the underlying hardware and operating system constraints.


**Here’s a simple Java program that creates 2000 blocking threads:**
```java

	private static final int N_THREADS = 2000;
	public static void main(String[] args) throws Exception {

		var threads = IntStream.range(0, N_THREADS).mapToObj(i -> Thread.ofPlatform().unstarted(() -> {
			blockFor(60, ChronoUnit.SECONDS);
			System.out.println(i);
		})).toList();

		for (var thread : threads) {
			thread.start();
		}

		for (var thread : threads) {
			thread.join();
		}
		blockFor(1, ChronoUnit.MINUTES);
	}


```

**JVM status**

![VM Stats](/assets/img/vt/pt-img1.png){: width="700" height="400" }

**Quick Observations**

- Each thread is tied to platform/os thread.
- Memory & cpu usage is high 

### Alternatives to the Current Thread Model

Reactive programming is a alternate approach to handling many tasks at once. Unlike the traditional thread model, where each task needs its own thread and can quickly use up resources, reactive programming allows tasks to run without waiting for others to finish. This makes it more efficient and responsive, meaning the system can handle more work without slowing down. It also deals with errors better, keeping things running smoothly even when problems arise. But it comes with is own costs.

- **Complexity:** Writing and understanding code in reactive programming is more challenging than in the traditional approach.
- **Debugging:** Debugging applications written in a reactive flow is more challenging
- **Unit Testing:** Creating unit tests for reactive code can be difficult, as it often requires specific tools and techniques to handle asynchronous operations effectively.
- **Learning Curve:** Developers may face a steep learning curve when transitioning to reactive programming, as it requires understanding new concepts and patterns.
- **Developer Responsibility:** Developers are responsible for ensuring that their code is non-blocking, which requires careful design and implementation to maximize the benefits of reactive programming.


### Modifying the same program using virtual threads with 2 million virtual threads

```java

	private static final int N_THREADS = 2_000_000;
    ...
		var threads = IntStream.range(0, N_THREADS).mapToObj(i -> Thread.ofVirtual().unstarted(() -> {
			blockFor(60, ChronoUnit.SECONDS);
			System.out.println(i);
		})).toList();

    ...
```

**JVM status**

![VM Stats](/assets/img/vt/vt-img1.png){: width="700" height="400" }



**Quick Observations**

- Number of platform threads created during the entire process is very low.
- The same platform threads are re-used by virtual threads instead of creating new ones.
- CPU usage is a bit higher due to the fact that the JVM moves virtual threads between the heap and platform threads.

### Using an executor service for virtual threads.

Virtual threads behave like platform threads, but they shouldn't represent the same concept. Platform threads are limited resources, often managed with thread pools. In contrast, virtual threads are abundant and should represent individual tasks.

Managing platform threads involves deciding how many threads to pool. With virtual threads, the number of threads equals the number of concurrent tasks in your application, similar to how many strings you use to store user names in memory.

Converting platform threads to virtual threads won't add much benefit; instead, convert tasks into virtual threads. Avoid using shared thread pools for virtual threads. Instead, use a virtual thread executor like in the following 

```java

    try(ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()){
			executor.submit(() -> {
				System.out.println("Hello from a virtual thread - 1!");
			});
			executor.submit(() -> {
				System.out.println("Hello from a virtual thread - 2!");
			});
		}

```
The code still uses an ExecutorService, but the one returned from Executors.newVirtualThreadPerTaskExecutor() doesn't employ a thread pool. Rather, it creates a new virtual thread for each submitted tasks.

### Limit the concurrency while using virtual Threads

There may be situations where the external APIs we are using support only 10 concurrent calls at a time. Due to resource constraints, we need to ensure that no more than 10 virtual threads access the API simultaneously. This can be achieved using a semaphore.

```java
Semaphore sem = new Semaphore(10);
...
sem.acquire();
 //do something
sem.release();
```

### Pinned Virtual Threads

Usually, when threads are created in the traditional way, the OS handles context switching between the threads. In the case of virtual threads, the JVM takes care of mounting and unmounting virtual threads to and from platform threads. The JVM performs this mounting and unmounting when there is blocking code. However, in the following scenarios, the JVM doesn't unmount and instead pins the virtual thread until execution is complete:

- When the virtual thread runs code inside a synchronized block or method.
- When the virtual thread runs a native method or a foreign function.

Pinning the virtual thread won't make the application incorrect, but it can impact scalability. For example, the level of concurrency achieved may be less than with the traditional thread model. Synchronized blocks can be revisited and replaced with ReentrantLock. Don't rush into refactoring all the code; instead, observe performance. If a thread is pinned and truly needs improvement, refactor it as necessary.
