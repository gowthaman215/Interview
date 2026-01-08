= POSIX IPC (Inter-Process Communication) — Complete Reference with Examples
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== Introduction

POSIX IPC (Inter-Process Communication) provides standardized mechanisms for
processes to communicate and synchronize with each other on UNIX-like systems.

POSIX IPC is primarily used for:
* Local process communication
* High-performance shared data exchange
* Synchronization between processes

It is fundamentally different from socket-based networking.

---

== Categories of POSIX IPC

POSIX IPC consists of three main mechanisms:

1. POSIX Shared Memory
2. POSIX Message Queues
3. POSIX Semaphores

These are defined in POSIX.1b and POSIX.1c standards.

---

== POSIX Shared Memory

=== What Is Shared Memory?

Shared memory allows multiple processes to map the **same physical memory**
into their virtual address spaces.

It is the **fastest IPC mechanism** because:
* No kernel data copying
* Direct memory access

---

=== Shared Memory Workflow

1. Create shared memory object
2. Set size
3. Map into process address space
4. Read/write memory
5. Synchronize access
6. Unmap and unlink

---

=== Example: Shared Memory Producer

[source,c]
----
#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>

int main() {
    int fd = shm_open("/myshm", O_CREAT | O_RDWR, 0666);
    ftruncate(fd, 1024);

    char* data = mmap(NULL, 1024,
                      PROT_READ | PROT_WRITE,
                      MAP_SHARED, fd, 0);

    strcpy(data, "Hello from shared memory");
    return 0;
}
----

---

=== Example: Shared Memory Consumer

[source,c]
----
#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = shm_open("/myshm", O_RDONLY, 0666);

    char* data = mmap(NULL, 1024,
                      PROT_READ,
                      MAP_SHARED, fd, 0);

    printf("%s\n", data);
    shm_unlink("/myshm");
    return 0;
}
----

---

== POSIX Message Queues

=== What Are Message Queues?

Message queues allow processes to exchange **discrete messages**
with priorities.

Features:
* Kernel-managed
* Ordered delivery
* Priority support

---

=== Message Queue Workflow

1. Create/open queue
2. Send messages
3. Receive messages
4. Close and unlink

---

=== Example: Message Queue Sender

[source,c]
----
#include <mqueue.h>
#include <string.h>

int main() {
    mqd_t mq = mq_open("/myqueue", O_CREAT | O_WRONLY, 0666, NULL);
    mq_send(mq, "Hello MQ", 8, 1);
    mq_close(mq);
}
----

---

=== Example: Message Queue Receiver

[source,c]
----
#include <mqueue.h>
#include <stdio.h>

int main() {
    char buffer[128];
    mqd_t mq = mq_open("/myqueue", O_RDONLY);
    mq_receive(mq, buffer, 128, NULL);
    printf("%s\n", buffer);
    mq_unlink("/myqueue");
}
----

---

== POSIX Semaphores

=== What Are Semaphores?

Semaphores provide **synchronization**, not data transfer.

Used to:
* Protect shared memory
* Coordinate processes
* Control access to resources

---

=== Semaphore Types

* Named semaphores (inter-process)
* Unnamed semaphores (in shared memory)

---

=== Example: Named Semaphore

[source,c]
----
#include <semaphore.h>
#include <fcntl.h>

int main() {
    sem_t* sem = sem_open("/mysem", O_CREAT, 0666, 1);
    sem_wait(sem);
    // critical section
    sem_post(sem);
    sem_close(sem);
    sem_unlink("/mysem");
}
----

---

== Synchronization with Shared Memory

Shared memory **must always** be protected using:
* Semaphores
* Mutexes (placed inside shared memory)

Failing to synchronize leads to race conditions.

---

== POSIX IPC vs Sockets

| Feature | POSIX IPC | Sockets |
|------|----------|--------|
| Scope | Local machine | Local + Network |
| Speed | Very fast | Slower |
| Data copy | Minimal | Kernel copy |
| Scalability | Limited | High |
| Complexity | High | Moderate |

---

== POSIX IPC vs Boost.Asio

| Aspect | POSIX IPC | Boost.Asio |
|-----|---------|-----------|
| Communication | Shared memory / MQ | Sockets |
| Async model | Manual | Event-driven |
| Network capable | No | Yes |
| Real-time trading | Shared state | Market data / orders |

Boost.Asio does **not** replace POSIX IPC.
They solve different problems.

---

== When POSIX IPC Is Used

POSIX IPC is typically used when:

* Processes run on the same host
* Extremely low latency is required
* Shared state must be accessed
* Copying data is too expensive

Examples:
* Matching engines
* Shared risk engines
* Market data fan-out

---

== When POSIX IPC Is Avoided

Avoid POSIX IPC when:

* Communication spans machines
* Fault isolation is required
* Complexity must be minimized
* Debugging ease matters

---

== Common Pitfalls

* Forgetting to unlink IPC objects
* Missing synchronization
* Resource leaks
* Permission issues
* Hard-to-debug crashes

---

== Interview-Grade Explanation

> “Boost.Asio does not use POSIX IPC primitives.  
> It is built on top of sockets and OS I/O multiplexing mechanisms like epoll.  
> POSIX IPC mechanisms such as shared memory, message queues, and semaphores are used for local, high-performance inter-process communication and solve a different class of problems.”

---

== One-Line Takeaway

POSIX IPC provides ultra-fast local inter-process communication using shared memory and synchronization primitives, while Boost.Asio focuses on scalable, asynchronous socket-based I/O.

