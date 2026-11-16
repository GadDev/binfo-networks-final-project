# Roadmap — Learn, Research, Build Later, and Write Your Own Report

**Date prepared:** 16 November 2026 · **Current phase:** Research & Discovery  
**This document is a plan, not permission to implement.** Nothing below marked as future work has been started by this document.

**Student profile:** Experienced programmer, beginner in networking; macOS; strongest language Python; Docker/Linux VM accessible; team unknown.  
**Target:** Internal finish by **30 November 2026** · Official code/report/slides submission **2 December 2026 at 23:59 Luxembourg time** · Presentation **3 December 2026**.

> **Schedule realism:** This is an aggressive plan. Daily available hours are not yet known. Technical experimentation plus the **student-authored 8–12-page report** can require substantial work. Before implementation, establish a real time budget and confirm the professor's expected experiment matrix. If the plan cannot fit, raise constraints rather than silently removing mandatory requirements.

## Detailed work checklist

See **[TASKS.md](TASKS.md)** for the complete assignment task inventory (stable IDs, mandatory/recommended/bonus labels, dependencies and concrete completion criteria). **[PROGRESS.md](../PROGRESS.md)** is the active record of what the student has actually completed and mastered.

Keep both synchronized after tutoring/review sessions. Planning is not implementation authorization.

## 1. The two tracks

**Track L — Learn deeply:** Read the textbook in a purposeful order, explain concepts, sketch diagrams, solve reasoning problems, and pass comprehension checks. Learning does not require writing application code.

**Track D — Deliver the assignment (future, gated):** Design, implement, run controlled experiments, collect data, write the report **yourself**, and present. This track begins **only after explicit authorization** to leave the research phase.

A useful habit: write study notes in your own words now. Later, those notes help you produce report arguments, but they are not substitutes for measured evidence.

## 2. Which chapter first?

**FIRST: Kurose & Ross, Chapter 1 — Computer Networks and the Internet**, especially **§§1.1–1.5**. Chapter 1 explains why networks have delays/loss and how layers work; without those foundations, reliability mechanisms in Chapter 3 are just names.

### Recommended assignment-focused reading order

| Sequence | Textbook chapter / section | Essential knowledge | Active exercise (no code) | Suggested effort |
| --- | --- | --- | --- | --- |
| 1 | **Ch. 1 §§1.1–1.3** | Networks, endpoints, clients/servers, packets, routers, packet switching | Draw a request/response traveling through two endpoints and a router; label responsibilities | 1–1.5 h |
| 2 | **Ch. 1 §§1.4–1.5** | Latency, loss, throughput; layered architecture, encapsulation | Explain queue loss vs corruption; draw application → transport → IP → link | 1–1.5 h |
| 3 | **Ch. 2 `2.1** | Application architectures, processes, addressing, transport service requirements | Explain IP vs port vs socket and why your file protocol needs metadata | 45–60 min |
| 4 | **Ch. 2: "Socket Programming: Creating Network Applications"** | TCP streams, UDP datagrams, socket semantics, partial TCP reads | Trace how 1000 bytes sent once can be received in three reads | 1–1.5 h |
| 5 | **Ch. 3 §§3.1–3.3** | Transport services, multiplexing, UDP, checksum | Explain datagram boundaries, header/payload and loss assumptions | 1–1.5 h |
| 6 | **Ch. 3 `3.4** | Reliable delivery, ACKs, timers, retransmissions, Stop-and-Wait, Go-Back-N, Selective Repeat | Draw a lost DATA packet, lost ACK, duplicate DATA, and reordered arrival | 2–3 h |
| 7 | **Ch. 3 §§3.5–3.6** | TCP, RTT, timeout, flow control vs congestion control | Explain why an ACK is not congestion control and why windowing increases utilization | 1.5–2 h |
| 8 | **Ch. 4 (IPv4/datagrams/fragmentation) + Ch. 6 (error detection/CRC)** | MTU, IPv4 fragmentation, checksums, packet corruption | Calculate 1500 − 20 − 8 − 11 = 1461 bytes of simplified file payload | 1–1.5 h |
| 9 | **Assignment pp.5–7 + statistics refresher** | Hashing, goodput, repetitions, standard deviation and error bars | Work a five-run example and explain why one fast run proves little | 1–1.5 h |

**Estimated focused reading + exercises:** ~11–15 hours, possibly more depending on starting knowledge. These are learning estimates, **not assignment requirements**.

**Edition note:** The publisher's ninth-edition *textbook* table of contents puts socket programming at **`2.6**, while the authors' online ninth-edition **knowledge checks** put it at **`2.7**. Follow the **section title**, not just its number, when reading slides versus book. Different editions may have different section numbers.

References:
- [Official author slides (9th-edition June 2025)](https://gaia.cs.umass.edu/kurose_ross/ppt.php)
- [Official author knowledge checks](https://gaia.cs.umass.edu/kurose_ross/knowledgechecks/)
- [9th-edition book table of contents (publisher)](https://www.informit.com/store/computer-networking-a-top-down-approach-book-9780135429334)

### Reading depth: now versus later

- **For this assignment, read deeply:** Chapter 1 §§1.4–1.5, Chapter 2 socket programming, Chapter 3 §§3.3–3.5, relevant parts of Chapters 4/6 and statistical basics.
- **For broader course knowledge after the assignment:** complete application-layer protocols, network routing/control plane, link layer, wireless/mobile, multimedia networking, and security. The course syllabus is broader than this specific assignment. Do not try to read the entire book before the near-term deadline.

## 3. Study methodology — learn, explain, apply, verify

Use a **45–60 minute learning cycle** per concept:

1. **Preview (5 min):** Read the assignment's related requirement and ask "what must this concept explain?"
2. **Read (15–20 min):** Study the textbook or official slides slowly, marking unfamiliar terms.
3. **Explain (5–10 min):** Close the material and explain in your own words, with one analogy. Avoid repeating slide wording.
4. **Draw or calculate (10–15 min):** Create one packet timeline, client/server diagram, or relevant equation manually.
5. **Verify (5–10 min):** Answer a few knowledge checks without looking, review errors, and record what remains uncertain.

### Mastery gates (our recommended learning method, not a professor requirement)

Before transitioning to protocol design, independently:
- Explain TCP stream framing and why one `recv` may not yield a full message.
- Draw loss, duplicate, corruption and reordering scenarios with correct ACK/timeout behavior.
- Compare the three ARQ approaches and justify their limits.
- Calculate chunk count and illustrate MTU fragmentation overhead.
- Define throughput vs verified goodput and explain why failed UDP transfers cannot count as successes.
- Compute sample mean and sample standard deviation from five toy values; interpret error bars.
- Explain CRC32 versus SHA-256 and how full-file hashes detect wrong reassembly.

**Check:** Be able to teach back all seven items without notes, and solve at least 4 of 5 short scenario questions. If not, revisit the relevant section — not as a failure, but to avoid memorizing without understanding.

### Companion course materials already in the repository

- [Networking mini-course index](../course/README.md)
- [Lesson 01 — Fundamentals](../course/01-fundamentals.md)
- [Lesson 02 — Reliability](../course/02-reliability.md)
- [Lesson 03 — Measurement](../course/03-measurement.md)

These are **supporting explanations**, not a substitute for the textbook or assignment PDF.

## 4. Milestones and acceptance criteria (future work)

| ID | Milestone | Dependencies | Expected evidence / acceptance criteria | Priority | Status |
| --- | --- | --- | --- | --- | --- |
| L1 | Study fundamentals | Assignment + Ch.1/2 | Explain layers, addressing, sockets, TCP/UDP boundaries | Critical | Next learning task |
| L2 | Study reliability and experiments | L1 | Draw ARQ/loss/reorder timelines; explain goodput/SD/CRC/SHA | Critical | Planned |
| R1 | Resolve architectural and experiment questions | L1–L2; professor if needed | Explicit approved decisions and realistic timing budget | Critical | Pending approval |
| A1 | Compare minimum vs modular architecture | R1 | Conceptual diagrams, directory trees, trade-off comparison (Stage 4) | Critical | Planned research |
| I1 | TCP baseline | Explicit implementation gate; A1 | All required TCP behaviors, hash MATCH and negative tests | Critical | Not authorized |
| I2 | Naive UDP and protocol | I1 design context, A1 | Packet format/START/END, baseline hash verdict and CLI | Critical | Not authorized |
| I3 | B1 and B2 | I2 | All required chunk/delay configurations, loss/throughput and buffer observations | Critical | Not authorized |
| I4 | B3 reliability | I2; ARQ choice | CRC32, ACK, timeout, retransmission, duplicate/retry behavior; five loss levels | Critical | Not authorized |
| I5 | B4 reordering | I2; impairment | Wrong arrival-order hash vs right sequence-aware hash, count and displacement | Critical | Not authorized |
| E1 | Experiment automation and plots | I1–I5 | Three test file classes, 5+ repetitions/configuration, CSVs, four plot categories and error bars | Critical | Not authorized |
| W1 | Student-authored report | Study notes, architecture, actual E1 data | All required sections, source citations, 8–12 pages and 11 pt | Critical | Notes only |
| S1 | Presentation | Design + E1 results + W1 findings | PDF slides, rehearsed 12-minute talk + 3-minute Q&A | Critical | Not started |
| F1 | Final reproducibility and submission | I1–S1 | README with exact commands, instructor access/history, PDFs verified | Critical | Not started |

**No item above is a commitment to implementation before an explicit go-ahead.**

## 5. Suggested calendar (conditional, high workload)

These dates are **planning windows**, not promises. Current available study/development hours are unknown, and this may need revision. Days involving code/experiments happen only **if** the research gate is approved.

| Date | Primary goal | Evidence and report-authorship activity |
| --- | --- | --- |
| **16 Nov** | Planning documents + start Ch.1 | Write personal glossary and 5 unanswered questions |
| **17 Nov** | Ch.1 §§1.1–1.5 | Draw packet switching and layers; explain delay/loss |
| **18 Nov** | Ch.2 `2.1 and socket programming | Draw TCP/UDP client-server timelines; begin your own report research-question notes |
| **19 Nov** | Ch.3 §§3.1–3.4 | Work ACK/timeout/reorder diagrams; discuss candidate ARQ |
| **20 Nov** | Ch.3 §§3.5–3.6, targeted Ch.4/6; Stage 4 architecture | Check understanding, verify work hours and experiment matrix; make decisions or revise schedule |
| **21 Nov** | **Conditional:** TCP architecture and baseline | Start your own design/implementation notes; cite references |
| **22 Nov** | **Conditional:** TCP correctness and integrity | Student drafts introduction/design outline in their own words |
| **23 Nov** | **Conditional:** UDP packet format and naive baseline | Write packet-format explanation and diagrams in notes |
| **24 Nov** | **Conditional:** B1/B2 and measurement instrumentation | Draft experimental-methodology section based on actual setup |
| **25 Nov** | **Conditional:** B3 reliability | Explain lost DATA vs lost ACK; refine report design section |
| **26 Nov** | **Conditional:** Finish reliability and B4 reordering | Document failure and correct reassembly; capture diagrams |
| **27 Nov** | **Conditional:** Impairment, test fixtures, automated sweeps | Gather *actual* raw trials; audit metric definitions |
| **28 Nov** | **Conditional:** Finish repeated experiments and plots | Student writes results text for TCP and B1–B4 from actual data |
| **29 Nov** | Report-first day | Student writes discussion/conclusion, citations, abstract **after** actual findings; prepare slides |
| **30 Nov** | Internal completion, independent review and rehearsal | Validate report 8–12 pages/11 pt, figures, code reproduction and timed talk |
| **1 Dec** | Contingency and reconciliation | Fix genuine evidence/formatting gaps, rerun documented failures if needed |
| **2 Dec** | Final submission by 23:59 Luxembourg time | Submit code link/ZIP, report PDF and slide PDF; verify receipt/access |
| **3 Dec** | Class presentation | Deliver 12-min talk + 3-min Q&A, both members speak if applicable |

### Honest feasibility gate

**Check on 20 November:** If the confirmed schedule leaves too few hours for reliable UDP, repeated experiments, a self-written report and rehearsal, revise effort assumptions immediately. Mandatory experiment/rubric items cannot be silently omitted. Keep bonus work out of the critical path.

**Check on 27 November:** If experiments are still incomplete, prioritize correctness and obtaining trustworthy mandatory evidence. Do not fabricate missing graphs or assertions; document actual limitations and discuss blockers with the instructor.

## 6. Student-authored report writing process — embedded throughout

The report is **40% of the final grade**, so don't postpone thinking about it until the code is finished.

### Your personal research journal (start now)

For each session, record:
- Date and what you read.
- The idea in your own words.
- One simple diagram or analogy.
- One question you cannot yet answer.
- Source and section/page reference.
- Later, what experimental evidence would confirm or challenge the claim.

### Writing milestones

| When | You write | Assistant may help by |
| --- | --- | --- |
| During Chapters 1–3 | Notes, glossary, research questions, report outline | Quizzing, source navigation, checking your conceptual statements |
| After architecture approval | Architecture and packet diagrams, chosen ARQ rationale | Challenging design logic and requesting citations |
| During actual experiments | Environment record and factual observations | Checking units, definitions, repeatability and graph interpretation |
| After results | Results, discussion, limitations and conclusion | Identifying unsupported claims and missing rubric criteria |
| Last | ~150-word numeric abstract, appendix, references | Checking structure, wording, citations and formatting |

### Required report outline (PDF pp.6–7)

1. Abstract (~150 words, write last).
2. Introduction.
3. Design (include lost-packet sequence diagram).
4. Implementation.
5. Experimental setup.
6. Results (TCP and B1–B4).
7. Discussion.
8. Conclusion (~half-page).
9. References (including RFC 768, RFC 9293 and appropriately cited AI assistance).
10. Appendix (commands, raw results, contributions).

**Review rule:** The student submits the first draft of each section; the assistant returns margin-note-style feedback and questions instead of quietly replacing the analysis.

## 7. Risks and fallbacks

| Risk | Likelihood | Impact | Mitigation / fallback |
| --- | --- | --- | --- |
| Limited remaining hours | High if fewer than ~3–4 productive hours/day | High | Confirm real time budget; use staged scope; raise professor ambiguities early |
| Stop-and-Wait excessively slow for small chunks with RTT | High in those configurations | High | Resolve required matrix; consider Go-Back-N before implementation |
| Linux/Docker `netem` privileges or direction incorrect | Medium | High | Research topology; use PDF-permitted fixed-seed emulator fallback |
| Microsecond pacing inaccurate | High with ordinary user-space sleeps | Medium | Document requested vs observed pacing and limitations |
| Receiver-buffer overflow not observable | Medium | Medium | Explain environment limits; ask professor if deliberate tuning is acceptable |
| Incomplete stats and results | Medium | High | Plan 5+ runs and CSV/plot workflow before coding; preserve failures |
| Report written too late | High without incremental writing | High | Write personal notes early; reserve 29–30 Nov for student-authored report and presentation |
| Team arrangement unknown | Medium | Medium | Plan conservatively as solo until clarified |
| Docker virtualization biases timings | Medium | Medium | State environment, control conditions, avoid universal claims |

Risks are judgments, not empirical probabilities.

## 8. Open research and professor questions

1. Must every file class be used in **every** B1/B2/B3 parameter setting?
2. For TCP vs UDP size plot, should TCP buffer size match each UDP chunk size, or act as a fixed baseline?
3. If zero-delay buffer overflow isn't observed under measured conditions, is describing that result sufficient?
4. For a packet-arrival-order trace, is an illustrative single trial acceptable if separate error-bar statistics accompany it?
5. Is using an ACK-only scheme without functional NACK handling acceptable given the packet-type list?

These should be resolved with the professor where necessary. Do not assume the answers.

## 9. Stage checkpoints

- **Current:** Study Chapter 1; review these research documents; continue Stage 4 architecture research only when authorized.
- **Before any coding:** Explicitly approve the architecture, Python choice, impairment setup, ARQ, experiment matrix and available hours.
- **Before report finalization:** Verify every empirical claim against actual run records.
- **Before submission:** Independent rubric/format/access check and presentation rehearsal.

## References

- Assignment: *TCP vs UDP File Transfer*, pp.1–10.
- [Kurose & Ross course slides](https://gaia.cs.umass.edu/kurose_ross/ppt.php) and [knowledge checks](https://gaia.cs.umass.edu/kurose_ross/knowledgechecks/).
- [9th edition contents (publisher)](https://www.informit.com/store/computer-networking-a-top-down-approach-book-9780135429334).
- [RFC 768 — UDP](https://www.rfc-editor.org/rfc/rfc768), [RFC 9293 — TCP](https://www.rfc-editor.org/rfc/rfc9293).

Related: [CONSTITUTION.md](CONSTITUTION.md) · [MISSION.md](MISSION.md) · [TECHSTACK.md](TECHSTACK.md) · [TASKS.md](TASKS.md) · [PROGRESS.md](../PROGRESS.md).
