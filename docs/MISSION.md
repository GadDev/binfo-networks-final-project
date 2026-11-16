# Mission — TCP vs UDP File Transfer

**Status:** Research & Discovery · **Course:** Networks (4 ECTS), University of Luxembourg  
**Source of truth:** *Assignment: TCP vs UDP File Transfer* (12 November 2026, 10 pages).

## 1. What are we trying to learn?

In plain English:

> We will eventually make a client send a file to a server, first using TCP (which gives ordered, reliable byte delivery) and then using UDP (which does not). We will investigate how UDP can lose, duplicate, corrupt, or reorder datagrams; add an application-level reliability scheme; and measure what each mechanism costs. We will verify each transfer with file hashes and explain the evidence in a report we write ourselves.

This is an **engineering and scientific investigation**, not merely "build a file-copy utility."

## 2. Research questions

1. How do TCP and UDP differ in the guarantees they provide to a file-transfer application?
2. How do UDP chunk size and inter-packet delay affect throughput, packet counts, and loss?
3. How much does a chosen UDP reliability scheme cost in retransmissions and effective throughput as loss increases?
4. What does out-of-order delivery do to naive file reconstruction, and how does sequence-aware reconstruction fix it?
5. Under what measured conditions would UDP be preferable to TCP for this experiment, and what qualifications are needed?

We must not write answers to the empirical questions before performing our own measurements.

## 3. What the professor requires

### Part A — TCP baseline (PDF p.2)
- Configurable server, at least one client at a time.
- Client header: filename, byte size and SHA-256.
- Stream the file and handle partial reads.
- Receive under a different filename; compare SHA-256; return OK/MISMATCH.
- Both sides record elapsed time, bytes and MB/s throughput.
- Configurable send/receive buffer size.

### Part B — UDP (PDF pp.3–4)
- Defined datagram format: type (1 B), sequence (4 B), payload length (2 B), CRC32 (4 B), variable payload.
- START metadata, END session marker; fixed byte order.
- **B1:** all nine chunk sizes: 64, 256, 512, 1024, 1400, 4096, 8192, 32768, 65000 B; measure throughput, datagrams and loss.
- **B2:** all five pauses: 0, 10 µs, 100 µs, 1 ms, 5 ms; measure loss/throughput without error management; investigate zero-delay receive-buffer overflow and report `SO_RCVBUF`.
- **B3:** one justified ARQ scheme (Stop-and-Wait, Go-Back-N or Selective Repeat), including checksum handling, ACKs, timeouts, retransmissions, duplicate suppression, max retries and graceful failure. Study 0%, 1%, 5%, 10%, 20% network loss.
- **B4:** log packet arrival order; demonstrate both incorrect arrival-order reconstruction (hash mismatch) and correct sequence-based reconstruction (hash match); report reordered count and maximum reordering distance.
- Naive UDP baseline: show how frequently its output matches when ACKs/reorder handling are off.

### Integrity (PDF p.5)
- SHA-256 is the main file integrity verdict; also compute MD5 and SHA-1 and explain why they are no longer cryptographically collision-resistant.
- Stream hashing in blocks and cross-check SHA-256 with a standard OS tool.
- Distinguish per-packet CRC32 from whole-file SHA-256.

### Experimental conditions (PDF pp.5–6)
- At least three test files: ~100 KB text, ~10 MB binary image/PDF, 100 MB random.
- Controlled, documented impairment using Linux `tc netem` or an in-application emulator with seeded choices; optional additional physical machine tests.
- At least **five repetitions per configuration**.
- Record transfer time, MB/s, datagrams, retransmissions, duplicates, out-of-order arrivals and hash verdict.
- Report mean, standard deviation and error bars; record OS, CPU, runtime and network settings.
- Required visualizations: (1) TCP & UDP throughput versus size, (2) loss versus inter-packet delay, (3) goodput & retransmissions versus loss rate, (4) arrival sequence versus arrival index.

## 4. Three deliverables

| Deliverable | Weight | Official format | What success looks like |
| --- | ---: | --- | --- |
| Source code | 35% | Accessible Git repository link or ZIP including `.git` history | Correct TCP/UDP behavior, CLI controls, full experiment-sweep script, CSVs and plot regeneration, README with exact commands |
| Written report | 40% | PDF, **8–12 pages excluding appendices**, **11 pt** | Student-authored scientific argument using genuine measurements, diagrams, analysis and citations |
| Presentation | 25% | PDF slides, 12-min talk + 3-min Q&A | Explain problem/design/results and demonstrate a correct transfer under impairment; answer questions |

**Internal target:** 30 November 2026.  
**Submission:** 2 December 2026, 23:59 Luxembourg time.  
**Presentation:** 3 December 2026.

The recommended presentation outline (~10–12 slides) and a live demo with backup video are encouragements in the PDF, not independent additional mandatory technologies.

## 5. Report: what YOU will write

**You are the report author.** The role of the assistant is to coach the research process, check drafts against the rubric, explain networking, and challenge interpretations — **not write the findings for you**.

Your report's required structure (PDF pp.6–7), with questions to answer in your own words:

| Section | Guiding questions and evidence |
| --- | --- |
| Abstract (~150 words) | What did we test, under what conditions, and what were the **actual numeric findings**? Write last. |
| Introduction | Why do TCP and UDP make different trade-offs? What question are our experiments answering? |
| Design | How do client/server and UDP packets work? Why this ARQ scheme? Draw a transfer with a lost packet. |
| Implementation | What language/modules/CLI options were used? What problems occurred and how were they resolved? |
| Experimental setup | Which files, CPU/OS, Python version, Docker/Linux topology, impairment commands/seeds, and repetitions? |
| Results | What did **each** TCP/B1/B2/B3/B4 plot or table show? Describe what you observe first, then interpret. |
| Discussion | Where is reliable UDP close to TCP, where not, and what explains the gap? When is UDP a sensible choice? |
| Conclusion (~half-page) | What did we actually learn, what are the limitations, and what would we change next? |
| References | RFC 768, RFC 9293, Kurose & Ross and any other cited sources, including relevant AI assistance. |
| Appendix | How to run experiments, raw result tables, contributions (solo or team). |

**Writing discipline:** For each factual sentence, ask "what source or run proves this?" For each graph, state units, the error-bar definition, repetitions and limitations. Never write the abstract's findings or the results narrative from hypothetical data.

### Student-authored report workflow

1. **Before programming:** Keep a notebook of questions, definitions, citations and architecture trade-offs. Draft your *own* introduction outline and research questions.
2. **During design:** Sketch network/packet diagrams and explain every design choice in your own words. Ask for review, not a finished answer.
3. **During experiments:** Record settings, raw runs, failures and observations. Write factual descriptions next to each graph.
4. **After experiments:** Write interpretation, discussion, conclusion and the numeric abstract. Ask the assistant to identify errors and unanswered questions.
5. **Before submission:** Apply rubric and formatting checklist; validate every number against CSV results.

### AI collaboration protocol for report reviews

When requesting feedback, provide your draft paragraph or plot and ask: **"Identify inaccurate networking claims, missing evidence, unclear reasoning, citation gaps and rubric omissions; explain how I can improve this myself."**

Do not use AI-generated plausible numbers, pretend that theoretical examples were observed results, or claim unperformed verification.

## 6. Out of scope and bonus work

Not required to satisfy the mandatory minimum: production-grade encrypted file sharing, graphical UI, cloud deployment, implementing every TCP guarantee, more than one ARQ scheme, adaptive RTT timeout, extra multi-client server and additional physical-machine experiments.

These may only be considered after mandatory requirements are secure and the schedule allows them.

## 7. Learning prerequisites

Before implementation, be able to explain:

- IP addresses, ports, protocol layers and sockets.
- TCP byte streams and framing versus UDP datagram boundaries.
- Why packet loss, duplication, corruption and reordering occur.
- ACK/NACK, timeouts, retransmissions, sequence numbers, windows and reassembly.
- Bandwidth/latency/RTT; throughput/goodput; MTU and fragmentation; receive buffers.
- CRC32 versus SHA-256; sample mean/standard deviation; reproducible experiments.

**Start with Kurose & Ross Chapter 1 ``1.1–1.5**, then Chapter 2 application principles and socket programming, then Chapter 3 transport/reliable delivery. Detailed learning sequence is in [ROADMAP.md](ROADMAP.md).

## 8. Open questions

- **[Open]** Team of one or two (three requires prior approval and two ARQ schemes).
- **[Open]** Are all three test-file sizes required at every experimental setting?
- **[Open]** How should the TCP buffer-size / UDP chunk-size graph be matched?
- **[Open]** How should a non-observed zero-delay buffer-overflow outcome be reported?
- **[Open]** How should error bars apply to the B4 arrival-order trace?

## References

- Assignment: *TCP vs UDP File Transfer* (12 Nov 2026), pp.1–10.
- [Kurose & Ross official slides](https://gaia.cs.umass.edu/kurose_ross/ppt.php).
- [Kurose & Ross official knowledge checks](https://gaia.cs.umass.edu/kurose_ross/knowledgechecks/).
- [RFC 768 — UDP](https://www.rfc-editor.org/rfc/rfc768).
- [RFC 9293 — TCP](https://www.rfc-editor.org/rfc/rfc9293).

See also [CONSTITUTION.md](CONSTITUTION.md), [TECHSTACK.md](TECHSTACK.md), and [ROADMAP.md](ROADMAP.md).
