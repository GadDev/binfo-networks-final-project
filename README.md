# Networks Final Project — TCP vs UDP File Transfer

> **Phase: Research & Discovery — no implementation yet.**  
> University of Luxembourg · BINFO · Networks (4 ECTS)

## In a nutshell

The assignment asks us to transfer a file from a **client** to a **server** in two ways:

1. **TCP:** use a reliable, ordered byte stream as the correctness and performance baseline.
2. **UDP:** first observe what can go wrong when sending datagrams, then investigate application-level reliability mechanisms (sequence numbers, acknowledgements, retransmissions, and reordering).

A transfer counts as correct only when the original and received files have matching **SHA-256** hashes. Later, controlled experiments will compare throughput, packet loss, delays, retransmissions, and arrival order. This is an **experimental networking assignment**, not just a file-copying exercise.

## Required submissions

| Deliverable | Assignment requirement | Weight |
| --- | --- | ---: |
| Source code | TCP/UDP client-server applications, experiment reproduction scripts, raw results and plotting | 35% |
| Written report | PDF, 8–12 pages excluding appendices, 11 pt; experiments and analysis | 40% |
| Presentation | PDF slides for a 12-minute talk + 3-minute questions | 25% |

**Our internal target:** 30 November 2026.  
**Official submission:** **2 December 2026, 23:59 Luxembourg time**.  
**Class presentation:** **3 December 2026**.

The assignment is the source of truth: *Assignment: TCP vs UDP File Transfer* (12 November 2026, 10 pages). The PDF is supplied separately and is not yet committed to this repository.

## What will eventually be studied and built?

- **Part A, TCP:** a configurable server and client, file metadata header, streaming reads/writes, SHA-256 integrity check, and transfer statistics.
- **Part B1, UDP chunk size:** test 64, 256, 512, 1024, 1400, 4096, 8192, 32768, and 65000 bytes.
- **Part B2, UDP packet delay:** test 0, 10 µs, 100 µs, 1 ms, and 5 ms, without error management.
- **Part B3, reliability:** compare possible ARQ schemes, later choose one, and measure recovery at loss levels of 0%, 1%, 5%, 10%, and 20%.
- **Part B4, reordering:** demonstrate incorrect arrival-order reconstruction and correct sequence-aware reassembly.
- **Scientific methodology:** three test-file types (~100 KB text, ~10 MB binary, 100 MB random), at least five runs per configuration, mean, standard deviation, error bars, and four required plot categories.

These are **future assignment requirements**, not completed features. No experimental results are presented as fact.

## Start learning here

The `course/` directory is a beginner-friendly mini-course designed for a software engineer new to networking:

| Lesson | You'll learn |
| --- | --- |
| [Course index](course/README.md) | Reading path, prerequisites, and glossary |
| [01 — Networking fundamentals](course/01-fundamentals.md) | Client/server, ports, sockets, TCP vs UDP, protocol layers, headers, MTU |
| [02 — UDP reliability](course/02-reliability.md) | Loss, duplicates, corruption, ACKs, ARQ schemes, windows, ordering, flow control |
| [03 — Measurement and integrity](course/03-measurement.md) | Throughput, goodput, RTT, buffers, emulation, statistics, hashes and plots |

**Suggested reading order:** fundamentals → reliability → measurement. Each lesson includes explanations, examples, common pitfalls, and self-check questions. There is no executable code.

## Project boundaries today

This repository currently contains **learning and planning documentation only**. The programming language, UDP reliability scheme, network impairment approach, and implementation architecture are **not approved**. Python is being considered, not selected.

**Not included yet:** source code, dependency manifests, setup scripts, Docker configuration, experiments, measurements, report PDF, or slide deck.

## References

- Assignment PDF: *Assignment: TCP vs UDP File Transfer*, especially pp. 1–10 (provided with the course, not stored here).
- Kurose & Ross, *Computer Networking: A Top-Down Approach* — [authors' lecture-slide resources](https://gaia.cs.umass.edu/kurose_ross/ppt.php).
- [RFC 9293: Transmission Control Protocol (TCP)](https://www.rfc-editor.org/rfc/rfc9293).
- [RFC 768: User Datagram Protocol (UDP)](https://www.rfc-editor.org/rfc/rfc768).

> **Academic integrity:** The assignment permits AI assistance and references provided they are cited. Every future implementation must be understood and defended by the student(s), and experimental measurements must come from their own runs.
