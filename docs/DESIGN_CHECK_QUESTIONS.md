# Design-Check Questions — TCP vs UDP File Transfer

**Status:** Study and design-review bank; no implementation or measurements.  
**Scope:** 136 questions across 16 topics, covering the assignment and the current [Stage 4 proposal](ARCHITECTURE.md).  
**Date:** 16 November 2026.

These are tutoring questions, not 136 new professor requirements, a report to submit, or a claim that you must answer everything before coding. Use relevant groups while learning, designing, debugging and rehearsing. Requirements come from [the supplied inventory](../references/assignment-requirements.md); session tags, terminal states, counter conventions and tool choices are marked as proposals in the architecture.

Question IDs are stable lookup labels. Preserve them when adding questions. No answers or mastery scores are prefilled.

## How to use the bank

1. Choose one question and answer in your own words.
2. For a failure scenario, name the sender and receiver states, the missing/corrupted message, timer/retry behavior, file effects, and evidence recorded.
3. Draw a timeline or work a tiny example where useful.
4. Ask for feedback, correct your answer and revisit the point later.

**Review scale, for tutoring only:**
- **Strong:** explains why the behavior follows and handles the relevant failure case.
- **Partial:** the main idea is right but an important state, boundary or evidence is missing.
- **Weak:** relies on an unsupported guarantee or cannot explain what happens.

Unanswered means unassessed, not weak. Reading an answer is not the same as independently explaining it.

## Start with these twelve

This is a suggested first selection, not an additional submission gate. Answer one at a time. Return to the remaining groups as their design/experiment topic becomes relevant.

| Question | Focus |
| --- | --- |
| Q-FND-02 | What TCP supplies and what the application still does |
| Q-TCP-01 | A TCP receive returns fewer bytes than one send |
| Q-FMT-02 | Shared byte order and field sizes |
| Q-SES-01 | Distinguishing the current transfer from an old one |
| Q-START-02 | START accepted but its acknowledgement lost |
| Q-DATA-04 | A timeout has several possible causes |
| Q-ACK-01 | DATA accepted but its ACK lost |
| Q-ORDER-01 | Arrival-order versus sequence-aware reconstruction |
| Q-END-03 | The receiver's final verdict is lost |
| Q-END-04 | Every final verdict is lost |
| Q-TIME-01 | Justifying a DATA timeout |
| Q-MET-01 | Comparable timing boundaries |

The current worked example below explains Q-END-03–04; revisit them later without looking. For the next live exchange, start with **Q-DATA-04**.

## Worked example: the final confirmation is lost

**Scenario:** The receiver has all file chunks and verifies a matching SHA-256. Its final confirmation does not reach the sender.

**Plain-English analogy:** Your parcel arrived, but the delivery receipt disappeared. The delivery and your knowledge of the delivery are two different things.

Under the **proposed** reliable lifecycle:

1. The sender waits until the final-verdict timeout.
2. It sends the same END message again, within the retry budget.
3. The receiver briefly retains its finished-session record and resends the same actual verdict.
4. It does not restart the transfer, truncate the file or append chunks again.
5. If every final reply is lost, the sender stops with **UNCONFIRMED** and an unsuccessful exit. The receiver may still record **MATCH**. Both records are kept and joined by the run/session identifier.

This is a design explanation supporting R-B3-04 and R-HASH-01–04; the PDF does not prescribe these exact ACK meanings, terminal names or Grace implementation. Finite retries cannot guarantee that both parties always learn success under arbitrary loss.

## Topic index

| Topic | Question IDs | Count |
| --- | --- | ---: |
| [Foundations](#foundations) | Q-FND-01–08 | 8 |
| [TCP framing and stream correctness](#tcp-framing-and-stream-correctness) | Q-TCP-01–10 | 10 |
| [UDP packet format and validation](#udp-packet-format-and-validation) | Q-FMT-01–10 | 10 |
| [Session identity and mode boundaries](#session-identity-and-mode-boundaries) | Q-SES-01–08 | 8 |
| [Startup and duplicate START](#startup-and-duplicate-start) | Q-START-01–08 | 8 |
| [DATA recovery and ARQ choice](#data-recovery-and-arq-choice) | Q-DATA-01–10 | 10 |
| [ACKs duplicates and corruption](#acks-duplicates-and-corruption) | Q-ACK-01–08 | 8 |
| [Reordering and the B4 demonstration](#reordering-and-the-b4-demonstration) | Q-ORDER-01–08 | 8 |
| [Finalization and lost confirmation](#finalization-and-lost-confirmation) | Q-END-01–10 | 10 |
| [Timeouts retries and clean failure](#timeouts-retries-and-clean-failure) | Q-TIME-01–08 | 8 |
| [File boundaries and integrity](#file-boundaries-and-integrity) | Q-FILE-01–08 | 8 |
| [Measurements and counters](#measurements-and-counters) | Q-MET-01–10 | 10 |
| [Repetitions plots and honest interpretation](#repetitions-plots-and-honest-interpretation) | Q-STAT-01–08 | 8 |
| [Environment impairment and pacing](#environment-impairment-and-pacing) | Q-ENV-01–08 | 8 |
| [Architecture tools and implementation boundaries](#architecture-tools-and-implementation-boundaries) | Q-ARCH-01–06 | 6 |
| [Report presentation and reproducibility](#report-presentation-and-reproducibility) | Q-DEL-01–08 | 8 |
| **Total** | | **136** |

## Foundations

**Related sources:** R-LANG-01; R-TCP-01–07; R-UDP-01–04. Kurose & Ross §§2.1, 2.6, 3.1–3.3. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-FND-01.** In this project, which program sends the file and which receives it? Can both run on one laptop?
- **Q-FND-02.** What does TCP promise the receiving application, and which file-level responsibilities still belong to your program?
- **Q-FND-03.** What does UDP preserve that TCP does not, and which delivery guarantees does UDP lack?
- **Q-FND-04.** How do an IP address, a transport protocol and a port number identify where data should go?
- **Q-FND-05.** What is a socket, and why does this assignment use plain sockets?
- **Q-FND-06.** What is the difference between file bytes, an application chunk, a UDP datagram and an IP packet?
- **Q-FND-07.** Why is “UDP is always faster than TCP” a claim that your experiments cannot assume in advance?
- **Q-FND-08.** Describe the whole project using the photograph/postcard analogy, including one way that analogy is imperfect.

## TCP framing and stream correctness

**Related sources:** R-TCP-01–07; R-HASH-03; R-CODE-06. RFC 9293 and Python socket documentation. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-TCP-01.** The sender writes 1000 file bytes; the receiver gets 300. What should happen next, and why is that not proof of loss?
- **Q-TCP-02.** What happens if the file metadata header itself arrives across several reads?
- **Q-TCP-03.** What happens if a read contains both the end of the metadata and the beginning of the file?
- **Q-TCP-04.** How will your chosen framing distinguish metadata length, metadata contents and file bytes?
- **Q-TCP-05.** If the sender uses send rather than sendall, what must it check before assuming all bytes were submitted?
- **Q-TCP-06.** What does an empty TCP receive mean, and what if it occurs before the announced file size has arrived?
- **Q-TCP-07.** How do you prevent waiting forever for a file whose sender disappeared halfway through?
- **Q-TCP-08.** What does the buffer-size CLI parameter control: application read/write size, kernel socket queues, or both? How will you document or clarify that?
- **Q-TCP-09.** Why should the receiver return OK only after checking the received file, rather than immediately after receiving its last bytes?
- **Q-TCP-10.** The TCP connection breaks while the client awaits OK. What can the server know, what can the client know, and how should their records differ?

## UDP packet format and validation

**Related sources:** R-UDP-02–04; R-B3-02; R-HASH-06. The session extension is a proposal in ARCHITECTURE.md, not an assignment requirement. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-FMT-01.** List the required minimum UDP application-header fields, their sizes and their jobs.
- **Q-FMT-02.** Why must sender and receiver agree on byte order and exact field sizes?
- **Q-FMT-03.** A datagram is shorter than the minimum header. What should happen before any unpacked field is trusted?
- **Q-FMT-04.** The payload-length field says 1400, but only 900 payload bytes arrived. What should the receiver do and record?
- **Q-FMT-05.** What should happen to an unknown packet type, an out-of-range sequence or an invalid chunk size?
- **Q-FMT-06.** Which bytes does the required CRC32 cover? If a sequence-number bit changes while payload bytes stay unchanged, can payload CRC alone detect that?
- **Q-FMT-07.** How will you distinguish an ACK for START, an ACK for DATA #0 and a final ACK for END?
- **Q-FMT-08.** If you accept the proposed 16-byte session extension, what changes in total application-header size and the simplified MTU calculation?
- **Q-FMT-09.** What is the difference between the receiver's kernel queue size and the byte capacity passed to recvfrom or recvmsg?
- **Q-FMT-10.** If a UDP datagram is truncated by an undersized application read buffer, can a second read recover its missing tail? How will you diagnose that case?

## Session identity and mode boundaries

**Related sources:** R-UDP-01; R-BASE-01; R-B3-04; R-B4-01–03. Session tags, Grace and UNCONFIRMED are design proposals. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-SES-01.** What identifies one transfer so a delayed packet from a previous run cannot advance the current run?
- **Q-SES-02.** If you decline the proposed session extension, what explicit alternative isolates transfers and what limitations does it have?
- **Q-SES-03.** Two transfers use the same filename and size. Why is that not enough to identify the same session?
- **Q-SES-04.** A START for another session arrives while the receiver is busy. What happens to the active output?
- **Q-SES-05.** A packet uses the right session identifier but a different peer address. What does your proposal do?
- **Q-SES-06.** In naive mode, are ACKs or hidden START/END retries still enabled? Would that contradict the run's label?
- **Q-SES-07.** If naive START is lost but DATA/END arrive, how do both endpoint records show the failed/incomplete run?
- **Q-SES-08.** Which independent mechanism switches are exposed, and how does every CSV row record their actual values?

## Startup and duplicate START

**Related sources:** R-UDP-04; R-B3-04; R-HASH-01. Exact negotiation/retry behavior follows the Stage 4 proposal. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-START-01.** START is lost. Which sender timer expires, what is retried, and what state is the receiver in?
- **Q-START-02.** The receiver accepts START, but its START ACK is lost. Why must the repeated START not truncate the partial file?
- **Q-START-03.** What state or record lets the receiver recognize a repeated START and resend the same acknowledgement?
- **Q-START-04.** The same session identifier arrives with conflicting filename, size or chunk metadata. What should happen?
- **Q-START-05.** How will you check that announced file size, chunk size and total chunk count agree?
- **Q-START-06.** What extra metadata, if any, supplies the original SHA-256 for UDP verification, and is that an assignment field or your design choice?
- **Q-START-07.** A matching START ACK arrives after the sender has already advanced to DATA. Can it change the DATA state or timer?
- **Q-START-08.** What bounded outcomes occur if the receiver is unreachable or every START acknowledgement is lost?

## DATA recovery and ARQ choice

**Related sources:** R-B3-01–06; R-B3-07; R-UDP-01. Kurose & Ross §3.4. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-DATA-01.** In Stop-and-Wait, which exact DATA packet may remain outstanding, and when may the sender advance?
- **Q-DATA-02.** DATA #5 is lost. Trace sender state, timeout, retransmission and receiver state.
- **Q-DATA-03.** DATA #5 arrives corrupted. At what point is it rejected, what is recorded, and how is recovery triggered?
- **Q-DATA-04.** Does a sender timeout prove DATA was lost? Name other explanations.
- **Q-DATA-05.** Why does a receiver still need duplicate suppression when the network itself never duplicates packets?
- **Q-DATA-06.** What is gained by keeping several DATA packets in flight, and what extra state does that introduce?
- **Q-DATA-07.** In Go-Back-N, define exactly what cumulative ACK value k means in your chosen convention.
- **Q-DATA-08.** In Go-Back-N, what happens to later chunks after an earlier chunk is missing? Contrast that with Selective Repeat.
- **Q-DATA-09.** How will required files, chunk sizes, assumed RTT and loss settings influence whether Stop-and-Wait fits the runtime budget? Label any estimate theoretical.
- **Q-DATA-10.** Which single scheme will you justify, what alternatives will you discuss, and when does team size make a second scheme mandatory rather than bonus?

## ACKs duplicates and corruption

**Related sources:** R-B3-02–04; R-UDP-02; R-HASH-06. Kurose & Ross §3.4. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-ACK-01.** The receiver writes DATA #5 but its ACK is lost. What must happen when DATA #5 is retransmitted?
- **Q-ACK-02.** If the sender times out, how could a delayed or lost ACK explain the event even though DATA arrived?
- **Q-ACK-03.** ACK #4 arrives while the sender waits for ACK #5. What is ignored, and which timer remains active?
- **Q-ACK-04.** The same ACK is received twice. Can that skip a chunk or expand a window incorrectly?
- **Q-ACK-05.** A corrupted ACK has a valid-looking sequence field. What validation is performed before it is allowed to advance progress?
- **Q-ACK-06.** A duplicate DATA packet contains different bytes from the already accepted chunk with the same sequence. What should the receiver record or reject?
- **Q-ACK-07.** Does your ACK promise only packet acceptance, file storage, or whole-file verification? Why should DATA and END acknowledgements have different meanings?
- **Q-ACK-08.** The packet-type list contains NACK. Does your chosen ARQ use it, and which expectation should be clarified with the professor rather than assumed?

## Reordering and the B4 demonstration

**Related sources:** R-B4-01–04; R-BASE-01; R-EXP-05. Operational counter definitions are proposals in ARCHITECTURE.md. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-ORDER-01.** Given distinct chunks arriving as 0, 2, 1, 3, what does arrival-order appending produce, and how does sequence-aware placement differ?
- **Q-ORDER-02.** Why is a sequence number different from an arrival index?
- **Q-ORDER-03.** Can classic Stop-and-Wait generate a normal burst of several distinct DATA chunks in flight? How will you conduct B4 separately?
- **Q-ORDER-04.** How will both B4 reconstructions use exactly the same captured DATA arrivals?
- **Q-ORDER-05.** Under the proposed late-arrival definition, what are the count and maximum backward gap for 0, 3, 1, 2? Show how the high-water mark changes.
- **Q-ORDER-06.** Do repeated copies of one sequence increase the duplicate count, the unique-chunk count, the reordering count, or some combination? State your definitions.
- **Q-ORDER-07.** If a chunk is missing as well as reordered, can correct placement alone restore a matching file?
- **Q-ORDER-08.** What do you report if configured reordering produces no observed reordering, or shuffled chunks happen to contain identical bytes?

## Finalization and lost confirmation

**Related sources:** R-UDP-04; R-B3-04; R-HASH-01–04; R-CODE-06. These detailed terminal states are proposed design behavior. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-END-01.** What must the receiver check before a session can be considered a complete matching file?
- **Q-END-02.** END is lost after all DATA acknowledgements. What does each endpoint wait for, and what is retried?
- **Q-END-03.** The receiver verifies a matching file, but its final verdict is lost. What should the sender do first?
- **Q-END-04.** Every repeated final verdict is also lost. What should the sender report, and what may the receiver still know?
- **Q-END-05.** Why should a repeated END receive the cached terminal result rather than reopen or rewrite the completed output?
- **Q-END-06.** What happens if END arrives before the final DATA chunk or while coverage still has a gap?
- **Q-END-07.** The receiver is still hashing when another END arrives. Why must it not send OK before verification finishes?
- **Q-END-08.** All expected chunks exist but SHA-256 differs. What terminal status, hashes and output file are preserved?
- **Q-END-09.** After the receiver's Grace period expires, a very late END arrives. What bounded assumption or policy applies?
- **Q-END-10.** Why does adding a receipt for the final receipt not create an absolute guarantee that both sides know success under arbitrary packet loss?

## Timeouts retries and clean failure

**Related sources:** R-B3-04–06; R-CODE-06. Timer separation and Grace durations are design choices. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-TIME-01.** How will you choose T_data relative to RTT and processing delay, and explain that choice with evidence?
- **Q-TIME-02.** What happens if the DATA timeout is too short? What if it is excessively long?
- **Q-TIME-03.** Why may final-verdict waiting need a different timeout from waiting for a small DATA acknowledgement?
- **Q-TIME-04.** Does R mean total attempts or retransmissions after the initial attempt? Write the maximum-attempt count in your convention.
- **Q-TIME-05.** With fixed timeout T and R retransmissions, what approximate wait budget follows, and what work is excluded from that estimate?
- **Q-TIME-06.** How must receiver idle/session limits and the final-result Grace period relate to the sender's retry horizon?
- **Q-TIME-07.** Which packets are allowed to reset a timeout or retry budget, and how do stale duplicates fail to keep a session alive forever?
- **Q-TIME-08.** What error/result is recorded when a peer disappears, a session budget expires or the client is interrupted?

## File boundaries and integrity

**Related sources:** R-TCP-03; R-HASH-01–06; R-CODE-06–07. Input/output validation choices supplement those requirements. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-FILE-01.** How many DATA chunks exist for an empty file, and what does its transfer lifecycle look like?
- **Q-FILE-02.** How do you calculate the expected length of the final chunk when file size is not a multiple of chunk size?
- **Q-FILE-03.** How will you detect a missing chunk if the output file has already been preallocated to its final length?
- **Q-FILE-04.** Why can matching file sizes coexist with different SHA-256 hashes, and what does a matching hash provide in practice?
- **Q-FILE-05.** How will file reads, hashing and reassembly stay bounded in memory when the file is larger than RAM?
- **Q-FILE-06.** What happens if the source file changes after its original digest/size was calculated?
- **Q-FILE-07.** How will output names, missing input files, existing outputs and disk-write errors be handled without overwriting the original?
- **Q-FILE-08.** Why compute CRC32, SHA-256, MD5 and SHA-1 for different purposes, and how will an independent OS tool cross-check the main digest?

## Measurements and counters

**Related sources:** R-B1-02; R-B2-02–03; R-B3-05; R-B4-04; R-EXP-01, R-EXP-05–07. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-MET-01.** Which exact event starts and stops each endpoint's timer, and are TCP/UDP compared using the same metric definition?
- **Q-MET-02.** Which hashing, connection/START negotiation, disk work and final-verdict costs are inside or outside each timed interval?
- **Q-MET-03.** What byte count is used for throughput and goodput, and are units decimal MB/s or binary MiB/s?
- **Q-MET-04.** Why must transmitter send rate not be presented as verified successful delivery rate?
- **Q-MET-05.** How do you count original DATA attempts, DATA retransmissions, ACKs and other control messages without mixing them?
- **Q-MET-06.** How does configured loss probability differ from missing unique DATA, corruption discards and observed retransmissions?
- **Q-MET-07.** A client is UNCONFIRMED while its receiver has MATCH. How are those records reconciled without discarding either?
- **Q-MET-08.** Which run/session/configuration identifiers join sender logs, receiver logs, CSV rows and raw arrival traces?
- **Q-MET-09.** How will you report a naive UDP failure that was very fast at sending all its datagrams?
- **Q-MET-10.** Which settings must remain constant when studying chunk size or pacing, and which potential confounders still need to be recorded?

## Repetitions plots and honest interpretation

**Related sources:** R-EXP-02, R-EXP-05–07; R-PLOT-01–04; R-REP-06, R-REP-10. Sample SD is the tutor's proposed convention. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-STAT-01.** What is one configuration, how many repetitions does the assignment require, and what experiment-matrix ambiguity still needs clarification?
- **Q-STAT-02.** What are the arithmetic mean and sample SD measuring, and why are sample SD and standard error different?
- **Q-STAT-03.** Five attempts yield two MATCH results and three failures. How do success rate and any success-conditioned speed summary show the whole picture?
- **Q-STAT-04.** If only one successful observation contributes to a speed summary, why is sample SD undefined rather than zero?
- **Q-STAT-05.** What do your error bars represent, and where do you state their definition and contributing sample count?
- **Q-STAT-06.** What are the four required plot categories, their axes/units and their associated raw data?
- **Q-STAT-07.** Why does the B4 arrival-order trace need clarification about error bars rather than inventing meaningless bars?
- **Q-STAT-08.** If measured results contradict the expected theory, what checks and limitations do you investigate before writing your conclusion?

## Environment impairment and pacing

**Related sources:** R-B1-03; R-B2-01–03; R-EXP-03–04, R-EXP-07. See udp(7), tc-netem(8) and Python time documentation. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-ENV-01.** Where will client and server run, and on which interface/direction will loss, delay or reordering actually apply?
- **Q-ENV-02.** If both DATA and reverse ACK traffic are impaired, how does that change the meaning of a configured loss setting?
- **Q-ENV-03.** What Docker/Linux capabilities, interface names, runtime versions and network settings must be verified and recorded?
- **Q-ENV-04.** If user-space sleep is asked for 10 microseconds, why might actual pacing differ, and how will that limitation be documented?
- **Q-ENV-05.** What is held constant when investigating zero-delay receiver queue overflow, and what if no overflow is observed?
- **Q-ENV-06.** How can a large datagram encounter fragmentation or path-MTU send errors, and why must those outcomes be distinguished from an application's dropped chunks?
- **Q-ENV-07.** If corrupted packets are rejected by the kernel before your program receives them, how can a clearly documented protocol-level fault test exercise the application CRC check?
- **Q-ENV-08.** How will seeds, repeated-trial randomization and impairment settings be recorded, while keeping experiments isolated and returning the environment to its baseline?

## Architecture tools and implementation boundaries

**Related sources:** R-LANG-01; R-UDP-01; R-CODE-02–07. Module layout, session tags and most tool choices are proposals. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-ARCH-01.** Why choose separate standalone programs or a small shared core? Which concrete responsibility belongs in each module?
- **Q-ARCH-02.** Which Python standard-library tools would handle sockets, binary layout, hashing, CRC, CLI, timing, CSV and sample SD?
- **Q-ARCH-03.** Which tools are assignment restrictions, which are recommendations, and which are merely optional conveniences?
- **Q-ARCH-04.** What small correctness checks will expose partial TCP reads, duplicate DATA writes, truncated UDP reads and lost final verdicts?
- **Q-ARCH-05.** Which ARQ, session-isolation, timer and experiment-matrix decisions still need an explicit answer before coding?
- **Q-ARCH-06.** What extra features would you defer until mandatory evidence, your own report and rehearsal fit the remaining time?

## Report presentation and reproducibility

**Related sources:** R-DEL-01–02; R-REP-01–11; R-PRES-01–04; R-CODE-01–08; academic integrity [PDF p.10]. Requirement IDs resolve to [the inventory](../references/assignment-requirements.md).

- **Q-DEL-01.** What will you write in your own report about design, setup, actual results, interpretation and limitations?
- **Q-DEL-02.** Which required report sections and formatting limits will you check before PDF submission?
- **Q-DEL-03.** How will every numeric claim and plotted point trace back to your own raw runs rather than a teaching example or theoretical estimate?
- **Q-DEL-04.** How will you cite technical references and substantive AI assistance, while being able to explain every line of code?
- **Q-DEL-05.** What exact run, experiment-sweep and plot commands will let the instructor reproduce your results from the repository?
- **Q-DEL-06.** Which files must be submitted by the deadline, how will instructor access/history be verified, and how do internal and official dates differ?
- **Q-DEL-07.** How will you fit the presentation into 12 minutes, prepare for 3 minutes of Q&A, and ensure every team member can explain the work?
- **Q-DEL-08.** Which professor questions remain unresolved, which team-size conditions apply, and how will you handle an encouraged demo without claiming it is mandatory?

## Answer record

Record only answers actually given and reviewed. Add rows as needed; do not mark the whole bank complete because the document exists.

| Question | Date attempted | Student explanation / note location | Review | Revisit action |
| --- | --- | --- | --- | --- |
| Q-DATA-04 | Not attempted | — | Unassessed | Explain what a timeout can and cannot prove |

## Sources and boundaries

- [Assignment inventory](../references/assignment-requirements.md): page-referenced requirements and AI-use policy. The original PDF remains authoritative.
- [Stage 4 architecture](ARCHITECTURE.md): current session lifecycle and recovery proposals.
- Kurose & Ross, Computer Networking: A Top-Down Approach: §1.4 (delay/loss/throughput), §1.5 (layers), §2.6 (socket programming in the ninth-edition textbook), §3.4 (reliable transfer/ARQ), §3.5 (TCP), §6.2 (error detection), §8.3 (message integrity).
- [RFC 768 — UDP](https://www.rfc-editor.org/rfc/rfc768); [RFC 9293 — TCP](https://www.rfc-editor.org/rfc/rfc9293).
- [Python socket](https://docs.python.org/3/library/socket.html), [time](https://docs.python.org/3/library/time.html), [statistics](https://docs.python.org/3/library/statistics.html), [hashlib](https://docs.python.org/3/library/hashlib.html).
- [Linux udp(7)](https://man7.org/linux/man-pages/man7/udp.7.html): datagram truncation, checksums and path-MTU behavior.
- [tc-netem(8)](https://man7.org/linux/man-pages/man8/tc-netem.8.html): impairment options, direction and timer/reordering limitations.

Hypothetical packet traces and runtime estimates are teaching examples or theory, never experimental results. Full implementation, experiments, report writing and final technology decisions remain future student work.

[Back to README](../README.md) · [Architecture](ARCHITECTURE.md) · [Progress](../PROGRESS.md)
