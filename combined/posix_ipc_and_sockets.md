# POSIX IPC and Sockets

This file combines the following references into one document:

1. [POSIX IPC (Inter-Process Communication) — Complete Reference with Examples](#part-1) — from `posix_ipc_complete_reference.md`
2. [POSIX Sockets — Complete Reference with Examples](#part-2) — from `posix_sockets_complete_reference.md`

---

<a id="part-1"></a>

*Part 1 — source: `posix_ipc_complete_reference.md`*

## POSIX IPC (Inter-Process Communication) — Complete Reference with Examples

### Introduction

POSIX IPC (Inter-Process Communication) provides standardized mechanisms for
processes to communicate and synchronize with each other on UNIX-like systems.

POSIX IPC is primarily used for:
- Local process communication
- High-performance shared data exchange
- Synchronization between processes

It is fundamentally different from socket-based networking.

---

### Categories of POSIX IPC

POSIX IPC consists of three main mechanisms:

1. POSIX Shared Memory
2. POSIX Message Queues
3. POSIX Semaphores

These are defined in POSIX.1b and POSIX.1c standards.

---

### POSIX Shared Memory

#### What Is Shared Memory?

Shared memory allows multiple processes to map the **same physical memory**
into their virtual address spaces.

It is the **fastest IPC mechanism** because:
- No kernel data copying
- Direct memory access

---

#### Shared Memory Workflow

1. Create shared memory object
2. Set size
3. Map into process address space
4. Read/write memory
5. Synchronize access
6. Unmap and unlink

---

#### Example: Shared Memory Producer

```c
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
```

---

#### Example: Shared Memory Consumer

```c
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
```

---

### POSIX Message Queues

#### What Are Message Queues?

Message queues allow processes to exchange **discrete messages**
with priorities.

Features:
- Kernel-managed
- Ordered delivery
- Priority support

---

#### Message Queue Workflow

1. Create/open queue
2. Send messages
3. Receive messages
4. Close and unlink

---

#### Example: Message Queue Sender

```c
#include <mqueue.h>
#include <string.h>

int main() {
    mqd_t mq = mq_open("/myqueue", O_CREAT | O_WRONLY, 0666, NULL);
    mq_send(mq, "Hello MQ", 8, 1);
    mq_close(mq);
}
```

---

#### Example: Message Queue Receiver

```c
#include <mqueue.h>
#include <stdio.h>

int main() {
    char buffer[128];
    mqd_t mq = mq_open("/myqueue", O_RDONLY);
    mq_receive(mq, buffer, 128, NULL);
    printf("%s\n", buffer);
    mq_unlink("/myqueue");
}
```

---

### POSIX Semaphores

#### What Are Semaphores?

Semaphores provide **synchronization**, not data transfer.

Used to:
- Protect shared memory
- Coordinate processes
- Control access to resources

---

#### Semaphore Types

- Named semaphores (inter-process)
- Unnamed semaphores (in shared memory)

---

#### Example: Named Semaphore

```c
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
```

---

### Synchronization with Shared Memory

Shared memory **must always** be protected using:
- Semaphores
- Mutexes (placed inside shared memory)

Failing to synchronize leads to race conditions.

---

### POSIX IPC vs Sockets

| Feature | POSIX IPC | Sockets |
|------|----------|--------|
| Scope | Local machine | Local + Network |
| Speed | Very fast | Slower |
| Data copy | Minimal | Kernel copy |
| Scalability | Limited | High |
| Complexity | High | Moderate |

---

### POSIX IPC vs Boost.Asio

| Aspect | POSIX IPC | Boost.Asio |
|-----|---------|-----------|
| Communication | Shared memory / MQ | Sockets |
| Async model | Manual | Event-driven |
| Network capable | No | Yes |
| Real-time trading | Shared state | Market data / orders |

Boost.Asio does **not** replace POSIX IPC.
They solve different problems.

---

### When POSIX IPC Is Used

POSIX IPC is typically used when:

- Processes run on the same host
- Extremely low latency is required
- Shared state must be accessed
- Copying data is too expensive

Examples:
- Matching engines
- Shared risk engines
- Market data fan-out

---

### When POSIX IPC Is Avoided

Avoid POSIX IPC when:

- Communication spans machines
- Fault isolation is required
- Complexity must be minimized
- Debugging ease matters

---

### Common Pitfalls

- Forgetting to unlink IPC objects
- Missing synchronization
- Resource leaks
- Permission issues
- Hard-to-debug crashes

---

### Interview-Grade Explanation

> “Boost.Asio does not use POSIX IPC primitives.
> It is built on top of sockets and OS I/O multiplexing mechanisms like epoll.
> POSIX IPC mechanisms such as shared memory, message queues, and semaphores are used for local, high-performance inter-process communication and solve a different class of problems.”

---

### One-Line Takeaway

POSIX IPC provides ultra-fast local inter-process communication using shared memory and synchronization primitives, while Boost.Asio focuses on scalable, asynchronous socket-based I/O.

---

<a id="part-2"></a>

*Part 2 — source: `posix_sockets_complete_reference.md`*

## POSIX Sockets — Complete Reference with Examples

### Introduction

POSIX sockets provide a standardized API for inter-process communication
over networks and within a single machine.

They are the foundation of:
- TCP/IP networking
- UDP communication
- UNIX domain IPC
- High-performance servers
- Libraries like Boost.Asio, libevent, and nginx

Sockets are a **kernel-level abstraction**.

---

### What Is a Socket?

A socket is an endpoint for communication.
It represents a file descriptor managed by the OS kernel.

Sockets support:
- Stream-based communication (TCP)
- Datagram-based communication (UDP)
- Local IPC (UNIX domain sockets)

---

### Socket Domains (Address Families)

#### AF_INET (IPv4)

- Internet Protocol version 4
- Uses IP address + port

```c
struct sockaddr_in {
    sa_family_t sin_family;
    uint16_t sin_port;
    struct in_addr sin_addr;
};
```

---

#### AF_INET6 (IPv6)

- Internet Protocol version 6
- Larger address space

```c
struct sockaddr_in6 {
    sa_family_t sin6_family;
    uint16_t sin6_port;
    struct in6_addr sin6_addr;
};
```

---

#### AF_UNIX (UNIX Domain Sockets)

- Local machine IPC
- Faster than TCP loopback

```c
struct sockaddr_un {
    sa_family_t sun_family;
    char sun_path[108];
};
```

---

### Socket Types

| Type | Description | Protocol |
|----|------------|----------|
| SOCK_STREAM | Reliable, ordered | TCP |
| SOCK_DGRAM | Unreliable, unordered | UDP |
| SOCK_SEQPACKET | Reliable datagrams | SCTP |
| SOCK_RAW | Raw packet access | Custom |

---

### Creating a Socket

```c
int sockfd = socket(AF_INET, SOCK_STREAM, 0);
```

Arguments:
1. Address family
2. Socket type
3. Protocol (0 = default)

---

### Binding a Socket

Associates socket with IP and port.

```c
bind(sockfd,
     (struct sockaddr*)&addr,
     sizeof(addr));
```

Used by servers.

---

### Listening for Connections (TCP)

```c
listen(sockfd, backlog);
```

- backlog = max pending connections

---

### Accepting Connections (TCP)

```c
int client_fd = accept(sockfd, NULL, NULL);
```

Creates a new socket for each client.

---

### Connecting to a Server (TCP Client)

```c
connect(sockfd,
        (struct sockaddr*)&server_addr,
        sizeof(server_addr));
```

---

### Sending and Receiving Data

#### send / recv

```c
send(sockfd, buffer, size, 0);
recv(sockfd, buffer, size, 0);
```

---

#### write / read (also valid)

```c
write(sockfd, buffer, size);
read(sockfd, buffer, size);
```

Sockets are file descriptors.

---

### TCP Server Example (Complete)

```c
#include <sys/socket.h>
#include <netinet/in.h>
#include <unistd.h>
#include <string.h>

int main() {
    int server_fd = socket(AF_INET, SOCK_STREAM, 0);

    struct sockaddr_in addr = {};
    addr.sin_family = AF_INET;
    addr.sin_port = htons(8080);
    addr.sin_addr.s_addr = INADDR_ANY;

    bind(server_fd, (struct sockaddr*)&addr, sizeof(addr));
    listen(server_fd, 5);

    int client_fd = accept(server_fd, NULL, NULL);

    char msg[] = "Hello Client";
    send(client_fd, msg, strlen(msg), 0);

    close(client_fd);
    close(server_fd);
}
```

---

### UDP Communication

#### UDP Socket Creation

```c
int sockfd = socket(AF_INET, SOCK_DGRAM, 0);
```

---

#### sendto / recvfrom

```c
sendto(sockfd, buf, len, 0,
       (struct sockaddr*)&addr, sizeof(addr));

recvfrom(sockfd, buf, len, 0,
         (struct sockaddr*)&addr, &addrlen);
```

UDP is connectionless.

---

### UNIX Domain Sockets (Local IPC)

#### Server Example

```c
int sockfd = socket(AF_UNIX, SOCK_STREAM, 0);

struct sockaddr_un addr = {};
addr.sun_family = AF_UNIX;
strcpy(addr.sun_path, "/tmp/mysock");

bind(sockfd, (struct sockaddr*)&addr, sizeof(addr));
listen(sockfd, 5);
```

Used for fast local IPC.

---

### Blocking vs Non-Blocking Sockets

#### Blocking (default)

- Calls block until completion

---

#### Non-Blocking Mode

```c
int flags = fcntl(sockfd, F_GETFL, 0);
fcntl(sockfd, F_SETFL, flags | O_NONBLOCK);
```

Used in event-driven servers.

---

### I/O Multiplexing

Allows handling many sockets with few threads.

---

#### select

```c
select(maxfd, &readfds, NULL, NULL, NULL);
```

Limitations:
- FD limit
- O(n) scanning

---

#### poll

```c
poll(fds, nfds, timeout);
```

Better than select.

---

#### epoll (Linux)

```c
epoll_create1(0);
epoll_ctl(epfd, EPOLL_CTL_ADD, sockfd, &ev);
epoll_wait(epfd, events, maxevents, timeout);
```

High-performance event notification.

---

### Socket Options

#### setsockopt

```c
setsockopt(sockfd, SOL_SOCKET, SO_REUSEADDR,
           &opt, sizeof(opt));
```

Common options:
- SO_REUSEADDR
- SO_KEEPALIVE
- TCP_NODELAY
- SO_RCVBUF / SO_SNDBUF

---

### Graceful Shutdown

#### shutdown

```c
shutdown(sockfd, SHUT_RDWR);
```

Closes read/write independently.

---

### Closing a Socket

```c
close(sockfd);
```

Releases file descriptor.

---

### Error Handling

Most socket calls return:
- -1 on error
- errno is set

```c
if (ret < 0) {
    perror("socket error");
}
```

---

### Performance Considerations

- Avoid blocking calls
- Use epoll for scalability
- Tune socket buffers
- Disable Nagle for low latency
- Minimize copies
- Reuse connections

---

### POSIX Sockets vs POSIX IPC

| Feature | Sockets | POSIX IPC |
|------|--------|----------|
| Scope | Local + Network | Local only |
| Speed | Fast | Very fast |
| Complexity | Moderate | High |
| Scalability | High | Limited |

---

### POSIX Sockets and Boost.Asio

Boost.Asio is built on:
- POSIX sockets
- epoll / kqueue
- Non-blocking I/O

Boost.Asio abstracts these details.

---

### Common Interview Questions

#### Are sockets file descriptors?

Yes. Sockets are treated as file descriptors.

---

#### TCP vs UDP?

| TCP | UDP |
|----|----|
| Reliable | Unreliable |
| Ordered | Unordered |
| Slower | Faster |
| Stateful | Stateless |

---

#### What happens on close()?

Kernel releases FD, connection teardown begins.

---

### One-Line Takeaway

POSIX sockets are the low-level, kernel-backed communication mechanism that power networking, IPC, and modern asynchronous frameworks like Boost.Asio.
