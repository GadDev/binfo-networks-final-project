# Lesson 01 — Networking Fundamentals

> **Phase: Research & Discovery.** No implementation in this lesson.  
> **Goal:** Explain how two programs exchange data over TCP or UDP, and why packet size and message boundaries matter.

[Course index](README.md) · [Next: UDP reliability](02-reliability.md)

## Before we begin: the whole assignment in one picture

Imagine sending a 10 MB photograph from **Computer A** (the client) to **Computer B** (the server). The program first reads file data, then sends it over a network. The receiver collects the data, reconstructs the file, and checks whether it is identical to the original.

```text
      COMPUTER A                                   COMPUTER B
  +----------------+                           +------------------+
  | Client program |                           | Server program   |
  | Original file  |                           | Received file    |
  +-------+--------+                           +---------^--------+
          |                                              |
       File chunks                                   Reassembly
          |                                              |
     TCP or UDP  --------  IP network  ------------> TCP or UDP
                        delay / loss / reordering
```

**TCP** supplies a reliable, ordered byte stream; **UDP** supplies datagrams with no delivery or ordering guarantee. The assignment asks us to first compare them, then investigate what reliability costs when added to UDP.

### Why "the same number of bytes" is not enough

```text
Original file:  [A] [B] [C] [D]
Arrival order:  [A] [C] [B] [D]
Same 4 chunks, wrong order -> different file contents
```

The reordering experiment (Part B4, assignment p.4) requires both the incorrect reconstruction and the corrected version.

---

## 1. Client–server architecture

A **client** is the program initiating communication; a **server** listens for or receives requests/data. In this project the client sends a file, while the server receives and stores it. Neither role is inherently limited to sending or receiving: data may flow in both directions.

- **Analogy:** A sender mails a parcel to a recipient.
- **Assignment connection:** Both Part A (TCP) and Part B (UDP) require a client and a server.
- **Common mistake:** Believing a server is always the sender, or that two computers are necessary. They can communicate over loopback on one machine during learning.

## 2. IP addresses and ports

An **IP address** identifies a network endpoint. A **port number** identifies the application/service for transport-layer delivery. The pair tells a client where to send data.

**Example:** `127.0.0.1:5000` is a port on the local computer. `127.0.0.1` is the IPv4 loopback address, not another host on the internet.

- **Analogy:** IP address = building address; port = apartment number.
- **Assignment connection:** Host and port must be configurable through command-line options (assignment p.8).
- **Common mistake:** Mistaking a port for a separate IP address. TCP port 5000 and UDP port 5000 are distinct transport endpoints.

## 3. The TCP/IP layers

The layers separate responsibilities so each part of the system can evolve independently.

| Layer | Job in this project | Example |
| --- | --- | --- |
| Application | Decide file metadata, chunking, integrity, and control messages | File transfer client and server |
| Transport | Deliver bytes or datagrams between applications | TCP or UDP |
| Network | Address and route IP packets between hosts | IPv4 / IPv6 |
| Link | Deliver frames on a local link | Ethernet / Wi-Fi |

```text
Application     file contents + your own transfer metadata
Transport       TCP segment or UDP datagram
Network         IP packet with source/destination addresses
Link            Frame transmitted over a specific local link
```

- **Analogy:** Item → packaging → postal routing → physical vehicle.
- **Assignment connection:** In UDP, the 11-byte **application-level** header is additional to UDP and IP headers.
- **Common mistake:** Counting the IP and UDP header as part of the 11-byte custom header, or assuming the link-layer MTU includes the same fields as the application datagram.

## 4. Sockets

A **socket** is an operating-system programming interface that lets a process send and receive data through a network transport. TCP sockets provide a byte stream; UDP sockets handle individual datagrams.

Sockets do not automatically understand files, filenames, or file hashes. Those are application-level choices.

- **Analogy:** A communication endpoint, like a telephone line or mailbox.
- **Assignment connection:** The professor requires plain sockets, not HTTP, FTP, QUIC, ZeroMQ, or transport-hiding frameworks (pp.2, 10).
- **Common mistake:** Assuming the socket API automatically knows when one complete file has been received.

## 5. TCP byte stream vs UDP datagrams

### TCP: reliable ordered bytes, not application messages

TCP provides a continuous sequence of bytes to the receiving application. A sender may submit 1,000 bytes in one operation, yet the receiver might initially obtain only 300 bytes. More reads are needed to collect the rest. The receiver does not see the sender's original write boundaries.

```text
Application writes:     [ 1000 bytes ]
Receiver reads:         [300] [450] [250]
Meaning:                All 1000 bytes, delivered in several reads
```

Because TCP has **no inherent application message boundaries**, the assignment requires a header with filename, file size, and SHA-256 digest. A receiver must interpret that header and continue reading until the expected number of file bytes has arrived.

### UDP: messages, but not guaranteed delivery

A UDP sender transmits datagrams; the receiver obtains individual datagrams, if they arrive. Datagram boundaries matter. However, UDP by itself does **not** guarantee delivery, order, or duplicate suppression.

```text
Sender sends:     DATA#0  DATA#1  DATA#2  DATA#3
Possible arrival: DATA#0  DATA#2  DATA#3
                             ^
                      DATA#1 never arrived
```

UDP's absence of reliability guarantees does *not* mean loss or reordering must occur on every transfer. On local loopback, they often do not occur at all; the assignment therefore requires controlled impairment.

| Question | TCP | UDP |
| --- | --- | --- |
| Does the transport maintain connection state? | Yes | No transport connection handshake |
| What does the application receive? | Byte stream | Datagrams |
| Is transport delivery reliable and ordered? | Yes, barring connection failure | No |
| Are application message boundaries preserved? | No | Yes, per datagram |
| Who handles file-level framing and integrity? | Application | Application |
| Who provides retransmissions? | TCP | Application, if implemented |

- **Analogy:** TCP resembles a correctly ordered collection of pages; UDP resembles separately sent numbered postcards.
- **Assignment connection:** Part A focuses on stream framing; Parts B1–B4 investigate datagram limits and reliability.
- **Common mistake:** Assuming one TCP send equals one receive, or assuming UDP is automatically faster in every situation.

## 6. Headers and payloads

A **header** contains information needed to interpret or manage the data; the **payload** contains the actual content being carried.

The assignment's UDP format has a minimum application header:

| Field | Size | Meaning |
| --- | ---: | --- |
| Type | 1 byte | DATA, ACK, NACK, START, END |
| Sequence number | 4 bytes | Position of file chunk |
| Payload length | 2 bytes | Number of payload bytes |
| CRC32 | 4 bytes | Detect accidental payload corruption |
| Payload | Variable | File chunk bytes |

**Total application header:** 1 + 4 + 2 + 4 = **11 bytes**.

A START message announces filename, file size, chunk size, and total chunks; an END message closes the session (assignment p.3). Additional metadata encoding is a future design decision, not fixed here.

- **Analogy:** Shipping label = header; parcel contents = payload.
- **Assignment connection:** All UDP messages require a defined interpretation, and a fixed byte order prevents conflicting serialization.
- **Common mistake:** Ignoring overhead or assuming the packet type field tells the receiver how many file bytes it has received.

## 7. MTU and IP fragmentation

The **Maximum Transmission Unit (MTU)** is the largest IP packet that fits on a given link without link-specific fragmentation/extra mechanisms. On a common 1500-byte Ethernet IP MTU, the maximum UDP payload under IPv4 without IP options is:

```text
Ethernet link's IP MTU                           1500 bytes
  IPv4 header                                    - 20 bytes
  UDP header                                      - 8 bytes
                                                 ----------
  Maximum UDP payload                             1472 bytes
  Your minimum application header                 - 11 bytes
                                                 ----------
  Maximum file chunk in this simplified example   1461 bytes
```

The assignment says fragmentation becomes important above **about 1472 bytes**. That statement refers to UDP payload capacity under common IPv4 assumptions. Because the application adds 11 bytes, the *file data* capacity can be smaller.

If a datagram exceeds a path MTU and fragmentation is permitted, IPv4 may divide it into fragments. If any fragment is lost, reassembly of the original datagram can fail. IPv6 handles fragmentation differently; actual path MTU, IP options, encapsulation, and interfaces can change the threshold.

**What this means for B1:** The mandatory chunk sizes include both tiny chunks (64 bytes) and very large chunks (up to 65,000 bytes). Small chunks cause high relative overhead and many operations; large datagrams can be fragile under loss and fragmentation.

- **Analogy:** A parcel too large for one crate must be split into several; losing one crate disrupts delivery.
- **Assignment connection:** B1 requires throughput, packet-count, and loss comparisons across all nine prescribed sizes.
- **Common mistake:** Treating 1472 as a universal guaranteed safe file chunk size or ignoring the additional 11-byte transfer header.

---

## Worked examples

### Example A — TCP partial reads

A client sends a file header saying **1,000 bytes of file data** will follow. The server's first read returns **300 bytes**.

**What should be concluded?** Not that 700 bytes were lost. The application must continue collecting data until it receives the expected 1,000 bytes or the connection fails.

### Example B — Reordering

File chunks are [A, B, C, D]; received datagrams arrive [A, C, B, D]. Simply appending produces the wrong file. If the receiver knows sequence numbers and the chunk size, it can reconstruct chunks in the correct positions.

### Example C — Packet-size overhead

With a fixed 11-byte custom application header, a 64-byte file chunk has a 11/75 ≈ **14.7%** application-header share *before* UDP/IP/link overhead. For a 1400-byte chunk, the same share is 11/1411 ≈ **0.78%**.

These are protocol-overhead calculations, not measured throughput results.

---

## Self-check questions

1. What is the difference between a process's port and a host's IP address?
2. Why must a TCP file receiver read in a loop rather than assume one read returns the entire file?
3. If UDP delivers all four chunks but the order is wrong, can the received file still fail SHA-256 verification?
4. What is the difference between a UDP header and your custom 11-byte application header?
5. Why might a 65,000-byte UDP *file chunk* be harder to deliver than a 1400-byte chunk?
6. Can one laptop run both the file-transfer client and server for early investigation?

<details>
<summary>Answers</summary>

1. The IP address identifies the endpoint; the transport port identifies the application/service.
2. TCP is a byte stream and does not preserve write boundaries.
3. Yes. Equal size does not imply equal bytes.
4. The UDP header belongs to the transport protocol; the custom header belongs to your application.
5. Large datagrams may need IP fragmentation or exceed path limits; losing one fragment can invalidate the datagram.
6. Yes, using the loopback interface, though controlled impairments are still needed for realistic loss/reordering experiments.

</details>

## Reading references

- Assignment: *TCP vs UDP File Transfer*, pp. 1–4 and 8–10.
- Kurose & Ross, *Computer Networking: A Top-Down Approach*: ninth-edition ``1.5, 2.1, 2.6, 3.3, 4.3 and 6.4; check section titles in other editions.
- [RFC 9293 — Transmission Control Protocol](https://www.rfc-editor.org/rfc/rfc9293).
- [RFC 768 — User Datagram Protocol](https://www.rfc-editor.org/rfc/rfc768).

[Course index](README.md) · [Next lesson: reliability](02-reliability.md)
