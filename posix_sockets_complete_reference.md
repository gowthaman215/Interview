= POSIX Sockets — Complete Reference with Examples
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== Introduction

POSIX sockets provide a standardized API for inter-process communication
over networks and within a single machine.

They are the foundation of:
* TCP/IP networking
* UDP communication
* UNIX domain IPC
* High-performance servers
* Libraries like Boost.Asio, libevent, and nginx

Sockets are a **kernel-level abstraction**.

---

== What Is a Socket?

A socket is an endpoint for communication.
It represents a file descriptor managed by the OS kernel.

Sockets support:
* Stream-based communication (TCP)
* Datagram-based communication (UDP)
* Local IPC (UNIX domain sockets)

---

== Socket Domains (Address Families)

=== AF_INET (IPv4)

* Internet Protocol version 4
* Uses IP address + port

[source,c]
----
struct sockaddr_in {
    sa_family_t sin_family;
    uint16_t sin_port;
    struct in_addr sin_addr;
};
----

---

=== AF_INET6 (IPv6)

* Internet Protocol version 6
* Larger address space

[source,c]
----
struct sockaddr_in6 {
    sa_family_t sin6_family;
    uint16_t sin6_port;
    struct in6_addr sin6_addr;
};
----

---

=== AF_UNIX (UNIX Domain Sockets)

* Local machine IPC
* Faster than TCP loopback

[source,c]
----
struct sockaddr_un {
    sa_family_t sun_family;
    char sun_path[108];
};
----

---

== Socket Types

| Type | Description | Protocol |
|----|------------|----------|
| SOCK_STREAM | Reliable, ordered | TCP |
| SOCK_DGRAM | Unreliable, unordered | UDP |
| SOCK_SEQPACKET | Reliable datagrams | SCTP |
| SOCK_RAW | Raw packet access | Custom |

---

== Creating a Socket

[source,c]
----
int sockfd = socket(AF_INET, SOCK_STREAM, 0);
----

Arguments:
1. Address family
2. Socket type
3. Protocol (0 = default)

---

== Binding a Socket

Associates socket with IP and port.

[source,c]
----
bind(sockfd,
     (struct sockaddr*)&addr,
     sizeof(addr));
----

Used by servers.

---

== Listening for Connections (TCP)

[source,c]
----
listen(sockfd, backlog);
----

* backlog = max pending connections

---

== Accepting Connections (TCP)

[source,c]
----
int client_fd = accept(sockfd, NULL, NULL);
----

Creates a new socket for each client.

---

== Connecting to a Server (TCP Client)

[source,c]
----
connect(sockfd,
        (struct sockaddr*)&server_addr,
        sizeof(server_addr));
----

---

== Sending and Receiving Data

=== send / recv

[source,c]
----
send(sockfd, buffer, size, 0);
recv(sockfd, buffer, size, 0);
----

---

=== write / read (also valid)

[source,c]
----
write(sockfd, buffer, size);
read(sockfd, buffer, size);
----

Sockets are file descriptors.

---

== TCP Server Example (Complete)

[source,c]
----
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
----

---

== UDP Communication

=== UDP Socket Creation

[source,c]
----
int sockfd = socket(AF_INET, SOCK_DGRAM, 0);
----

---

=== sendto / recvfrom

[source,c]
----
sendto(sockfd, buf, len, 0,
       (struct sockaddr*)&addr, sizeof(addr));

recvfrom(sockfd, buf, len, 0,
         (struct sockaddr*)&addr, &addrlen);
----

UDP is connectionless.

---

== UNIX Domain Sockets (Local IPC)

=== Server Example

[source,c]
----
int sockfd = socket(AF_UNIX, SOCK_STREAM, 0);

struct sockaddr_un addr = {};
addr.sun_family = AF_UNIX;
strcpy(addr.sun_path, "/tmp/mysock");

bind(sockfd, (struct sockaddr*)&addr, sizeof(addr));
listen(sockfd, 5);
----

Used for fast local IPC.

---

== Blocking vs Non-Blocking Sockets

=== Blocking (default)

* Calls block until completion

---

=== Non-Blocking Mode

[source,c]
----
int flags = fcntl(sockfd, F_GETFL, 0);
fcntl(sockfd, F_SETFL, flags | O_NONBLOCK);
----

Used in event-driven servers.

---

== I/O Multiplexing

Allows handling many sockets with few threads.

---

=== select

[source,c]
----
select(maxfd, &readfds, NULL, NULL, NULL);
----

Limitations:
* FD limit
* O(n) scanning

---

=== poll

[source,c]
----
poll(fds, nfds, timeout);
----

Better than select.

---

=== epoll (Linux)

[source,c]
----
epoll_create1(0);
epoll_ctl(epfd, EPOLL_CTL_ADD, sockfd, &ev);
epoll_wait(epfd, events, maxevents, timeout);
----

High-performance event notification.

---

== Socket Options

=== setsockopt

[source,c]
----
setsockopt(sockfd, SOL_SOCKET, SO_REUSEADDR,
           &opt, sizeof(opt));
----

Common options:
* SO_REUSEADDR
* SO_KEEPALIVE
* TCP_NODELAY
* SO_RCVBUF / SO_SNDBUF

---

== Graceful Shutdown

=== shutdown

[source,c]
----
shutdown(sockfd, SHUT_RDWR);
----

Closes read/write independently.

---

== Closing a Socket

[source,c]
----
close(sockfd);
----

Releases file descriptor.

---

== Error Handling

Most socket calls return:
* -1 on error
* errno is set

[source,c]
----
if (ret < 0) {
    perror("socket error");
}
----

---

== Performance Considerations

* Avoid blocking calls
* Use epoll for scalability
* Tune socket buffers
* Disable Nagle for low latency
* Minimize copies
* Reuse connections

---

== POSIX Sockets vs POSIX IPC

| Feature | Sockets | POSIX IPC |
|------|--------|----------|
| Scope | Local + Network | Local only |
| Speed | Fast | Very fast |
| Complexity | Moderate | High |
| Scalability | High | Limited |

---

== POSIX Sockets and Boost.Asio

Boost.Asio is built on:
* POSIX sockets
* epoll / kqueue
* Non-blocking I/O

Boost.Asio abstracts these details.

---

== Common Interview Questions

=== Are sockets file descriptors?

Yes. Sockets are treated as file descriptors.

---

=== TCP vs UDP?

| TCP | UDP |
|----|----|
| Reliable | Unreliable |
| Ordered | Unordered |
| Slower | Faster |
| Stateful | Stateless |

---

=== What happens on close()?

Kernel releases FD, connection teardown begins.

---

== One-Line Takeaway

POSIX sockets are the low-level, kernel-backed communication mechanism that power networking, IPC, and modern asynchronous frameworks like Boost.Asio.

