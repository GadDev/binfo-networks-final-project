# Networks Final Project — TCP vs UDP File Transfer

> **Current phase: Stage 4 — architecture and protocol design, for review.**  
> University of Luxembourg · BINFO · Networks (4 ECTS)  
> This repository contains research and learning documents. Application code and experiments have not started.

## In a nutshell: what do I actually need to do?

**Build a file delivery service, deliberately give the network problems, and explain what happens.** Think of TCP as a courier that manages delivery and ordering; with UDP, you investigate postcards and then add your own delivery receipts.

| Step | Your task | Evidence you will eventually produce | Assignment reference |
| --- | --- | --- | --- |
| 1. Establish the TCP baseline | Write a plain-socket client/server; send file metadata, stream bytes, handle partial reads, save under a new name, return OK/MISMATCH | Matching SHA-256; elapsed time, byte counts and throughput at both endpoints | R-TCP-01–07 [PDF p.2] |
| 2. Build naive UDP | Define the packet format, START/DATA/END messages, and configurable chunk size and delay; measure with recovery disabled | Baseline transfer logs and frequency of matching file hashes | R-UDP-01–04; R-BASE-01 [PDF pp.3–4] |
| 3. Explore size and pacing | Run the prescribed B1 chunk-size and B2 inter-packet-delay settings; investigate receive-buffer overflow | Throughput, packet counts, loss and the SO_RCVBUF setting | R-B1-01–03; R-B2-01–03 [PDF p.3] |
| 4. Add reliable UDP | Choose and justify one ARQ scheme; detect corruption/duplicates, acknowledge data, retry on timeout and stop cleanly after bounded retries | B3 measurements at the five specified loss levels | R-B3-01–06 [PDF p.4] |
| 5. Demonstrate reordering | Show an incorrect file assembled in arrival order, then correct sequence-aware reassembly | Both hash verdicts, arrival trace, reordered count and maximum distance | R-B4-01–04 [PDF p.4] |
| 6. Run repeatable experiments | Use the three file classes, controlled impairments and at least five runs per configuration; preserve raw CSV and regenerate plots | Mean, standard deviation, error bars, hashes and documented environment | R-EXP-01–07; R-PLOT-01–04; R-CODE-04–05 [PDF pp.5–6, 8] |
| 7. Explain your own work | Write the report yourself, prepare slides and be able to explain every line of code | Code repository/history, report PDF and presentation PDF | R-REP-01–11; R-PRES-01–02; R-CODE-01 [PDF pp.6–8, 10] |

All requirement IDs resolve to [the assignment inventory](references/assignment-requirements.md). Recommendations below are engineering choices, not additional professor requirements.

### The experiment settings to keep in view

| Experiment | Specified values / outcome | Source |
| --- | --- | --- |
| B1: UDP file-chunk size | 64, 256, 512, 1024, 1400, 4096, 8192, 32768, 65000 bytes | R-B1-01 |
| B2: inter-packet delay | 0, 10 µs, 100 µs, 1 ms, 5 ms; error management off | R-B2-01–02 |
| B3: configured loss | 0%, 1%, 5%, 10%, 20%; error management on | R-B3-05 |
| B4: reordering | Arrival-order reconstruction fails its hash; sequence-aware reconstruction matches | R-B4-01–04 |
| Test files | About 100 KB text, about 10 MB binary image/PDF, 100 MB random data | R-EXP-02 |
| Repetitions | At least five per configuration, with mean, SD and error bars | R-EXP-06 |

The exact crossing of file classes with experiment settings still needs clarification. Do not silently assume an enormous full Cartesian product or omit required evidence. We propose **sample** SD; the PDF itself does not specify the SD variant.

## What tools can I use?

**Proposed route for your background:** develop in Python on macOS, run controlled networking experiments in Linux containers or a Linux VM, and use Python for CSV analysis and plots. These choices remain proposals; no packages, containers or network settings have been installed or changed.

| Job | Tool you can use | Why / status |
| --- | --- | --- |
| TCP and UDP applications | Python 3 standard-library [socket](https://docs.python.org/3/library/socket.html) | Python is recommended by the assignment; plain sockets are required (R-LANG-01) |
| Packet encoding | [struct](https://docs.python.org/3/library/struct.html) | Explicit byte order and field sizes; proposed implementation of R-UDP-02–03 |
| File hashes | [hashlib](https://docs.python.org/3/library/hashlib.html) | Incremental SHA-256, plus MD5/SHA-1 for the required comparison (R-HASH-02–03) |
| Packet corruption checks | [zlib.crc32](https://docs.python.org/3/library/zlib.html#zlib.crc32) | Payload CRC32; proposed implementation of R-B3-02 |
| CLI, clocks, CSV and statistics | argparse, time.perf_counter_ns, csv, statistics | Standard-library candidates; CLI and raw results support R-CODE-03–04 |
| Correctness checks | unittest | Proposed testing tool; no test framework is prescribed |
| Plots and error bars | [Matplotlib](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.errorbar.html) | Proposed external plotting dependency; not installed |
| Controlled loss/delay/reordering | Linux [tc netem](https://man7.org/linux/man-pages/man8/tc-netem.8.html) inside Docker/Linux VM | Assignment-recommended impairment route (R-EXP-03); privileges/topology must be checked |
| Impairment fallback | Seeded receiver-side software emulator | Assignment-permitted alternative (R-EXP-04); does not reproduce every kernel/network behavior |
| Independent SHA-256 check | macOS shasum -a 256; Linux sha256sum | Cross-check the application's hashes (R-HASH-05) |
| Source history | Git and GitHub | Preserve changes; give the instructor read access or submit a ZIP including history (R-CODE-01, R-CODE-08) |
| Your written report | LaTeX/Overleaf or Word, exported to PDF | LaTeX is preferred; Word is accepted (R-REP-11) |
| Your slides | A slide editor that exports PDF | PDF output and talk duration are specified; editor choice is open (R-PRES-01) |
| Optional packet inspection | Wireshark or tcpdump | Helpful for diagnosis; an engineering suggestion, not a required deliverable |

C, Java, Go and Rust with plain sockets are also accepted. Libraries hiding the transport, including HTTP clients, FTP, QUIC and ZeroMQ, are disallowed; TCP sendall is allowed, sendfile is disallowed (R-LANG-01 [PDF pp.2, 10]).

## What do I submit, and when?

| Deliverable | Format | Weight |
| --- | --- | ---: |
| Code and reproducibility materials | Accessible Git repository, or ZIP including .git history; exact run/plot commands in README | 35% |
| Student-authored report | PDF, 8–12 pages excluding appendices, 11-point font | 40% |
| Presentation | PDF slides; 12-minute talk and 3-minute Q&A | 25% |

Sources: R-DEL-01, R-CODE-01–02, R-REP-01 and R-PRES-01.

- **Internal planning target:** 30 November 2026.
- **Official submission:** 2 December 2026, 23:59 Luxembourg time.
- **Presentation:** 3 December 2026.

One reliability scheme is the normal minimum. **Teams of three require prior approval and two schemes** (R-DEL-02; R-B3-07). A live demo is encouraged, with a recording backup recommended; it is not explicitly mandatory (R-PRES-04).

## Stage 4: architecture and packet lifecycle

Read [the Stage 4 design proposal](docs/ARCHITECTURE.md) next. It compares a minimal layout with a small modular layout and covers:

- START negotiation and duplicate START without resetting a file.
- Lost DATA, lost ACK, corruption, duplicates and reordering.
- END arriving early, lost END and lost final confirmation.
- Bounded retries, receiver idle deadlines and an explicit unconfirmed sender outcome.
- Separate naive, reliable and B4 demonstration modes.
- A proposed session identifier and ACK meanings, with their extra overhead clearly labeled as design choices.

**Design checkpoint:** lifecycle and recovery cases are documented for review. Architecture, ARQ scheme, experiment matrix and time budget still need a decision before implementation.

## Learning and planning documents

| Document | Purpose |
| --- | --- |
| [Course index](course/README.md) | Beginner learning path and English–French glossary |
| [01 — Fundamentals](course/01-fundamentals.md) | Layers, ports, sockets, TCP streams, UDP datagrams and MTU |
| [02 — Reliability](course/02-reliability.md) | ACKs, timers, ARQ schemes, duplicates and reordering |
| [03 — Measurement](course/03-measurement.md) | Throughput, goodput, buffers, statistics and hashes |
| [Constitution](docs/CONSTITUTION.md) | Academic integrity, phase boundaries and student ownership |
| [Mission](docs/MISSION.md) | Objectives, deliverables and report-writing guidance |
| [Technical research](docs/TECHSTACK.md) | Proposed tools and unresolved choices |
| [Roadmap](docs/ROADMAP.md) | Reading order, milestones and schedule |
| [Progress](PROGRESS.md) | Confirmed repository state and next checkpoint |

Start with Kurose & Ross Chapter 1 §§1.1–1.5, then Chapter 2's socket programming section, then Chapter 3 on transport and reliable data transfer. The accompanying course contains explanations, not executable deliverables.

## Evidence and academic integrity

The [page-referenced requirements inventory](references/assignment-requirements.md) records the supplied assignment and its AI-use policy. The original PDF remains authoritative. AI assistance and online references must be cited, you must be able to explain your code, and all measurements must come from your own runs [PDF p.10].

**Repository status:** documentation only. There are no runnable application commands, experiment results, report PDF or slide deck yet. Exact reproduction commands will be added with the implementation (R-CODE-02).

## Technical references

- Kurose & Ross, Computer Networking: A Top-Down Approach — [authors' resources](https://gaia.cs.umass.edu/kurose_ross/ppt.php), especially Chapter 3 §3.4.
- [RFC 768 — UDP](https://www.rfc-editor.org/rfc/rfc768).
- [RFC 9293 — TCP](https://www.rfc-editor.org/rfc/rfc9293).
