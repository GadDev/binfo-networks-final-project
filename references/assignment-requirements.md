# Assignment requirements — TCP vs UDP File Transfer

> **Source of truth:** `Assignment TCP vs UDP File Transfer.pdf` (10 pages; dated 12 November 2026). Page numbers in this file refer to the printed page numbers in that PDF. Requirements below are a page-referenced inventory of the professor's instructions, not new instructions. IDs (`R-...`) are local lookup labels added for the tutor; they are **not** IDs assigned by the professor.
>
> **Interpretation:** **Mandatory** = wording such as *must*, *required*, *use*, *test*, *report*, or a specified deliverable; **Bonus** = expressly described as bonus/extra credit; **Recommended/optional** = expressly recommended, preferred, suggested, optional, or encouraged. Decisions not made in the provided sources remain unfilled.

> **Repository snapshot:** Imported on 16 November 2026 from the supplied `assignment-requirements-populated.md` record. The original PDF supersedes this inventory; its bytes were not re-read during this documentation update. The "Decisions already made" section describes what was known when that record was prepared. Current proposals are in [TECHSTACK.md](../docs/TECHSTACK.md) and [ARCHITECTURE.md](../docs/ARCHITECTURE.md).

## AI-use and academic integrity policy (exact text from PDF p.10)

> **Academic integrity.** You may discuss ideas with other teams but not share code. You may use online references and AI assistants. Cite them in the report and be ready to explain every line in the Q&A. Code you cannot explain will not be credited. All measurements must come from your own runs.

This is the professor's **complete academic-integrity paragraph** [PDF p.10], transcribed verbatim. Do not replace it with an inferred AI prohibition or an invented permission. For other rules on permitted tools, see [PDF pp.2, 10].

## Constraints fixed by the assignment

- **Deadline:** Submit report, slides and source code by **2 December 2026, 23:59 Luxembourg time**; class presentation **3 December 2026** [PDF pp.1–2]. The skill's **30 November 2026 internal target** is a planning choice, **not** a professor deadline.
- **Weights:** Code **35 points**; report **40 points**; presentation **25 points**. Up to **10 bonus points**; total capped at **100** [PDF pp.1–2, 8–9].
- **Teams:** Work in teams of two unless told otherwise; every team member speaks. Working alone is permitted with the same requirements. Teams of three need prior approval **and must implement two error-management schemes** [PDF pp.2, 10].
- **Language / transport API:** Python 3 with standard `socket` and `hashlib` is **recommended**. C, Java, Go, and Rust are also accepted with **plain sockets**. Libraries that hide the transport (HTTP clients, FTP, QUIC, ZeroMQ) are not allowed; `sendall` is fine for TCP, but `sendfile`, HTTP, FTP, QUIC and messaging libraries are not allowed [PDF pp.2, 10]. The student has **not yet recorded a chosen language**.
- **UDP datagram packet fields (minimum):** Type **1 byte** (DATA, ACK, NACK, START, END); sequence number **4 bytes**; payload length **2 bytes**; checksum **4 bytes** (CRC32 of payload); payload **variable length** (file chunk). Use `struct.pack` or equivalent with **fixed byte order**. `START` announces file name, size, chunk size, total chunks; `END` closes session [PDF p.3].
- **B1 chunk sizes (at least):** **64, 256, 512, 1024, 1400, 4096, 8192, 32768, 65000 bytes** [PDF p.3].
- **B2 inter-packet delays (at least):** **0, 10 µs, 100 µs, 1 ms, 5 ms** [PDF p.3].
- **B3 network loss rates:** **0%, 1%, 5%, 10%, 20%** with retransmission counts and effective throughput [PDF p.4]. Pick **one** reliability scheme (Stop-and-Wait, Go-Back-N or Selective Repeat) and justify it; comparing two is **bonus**, except teams of three must implement two [PDF pp.4, 10].
- **Integrity:** SHA-256 is the main file hash; also compute MD5 and SHA-1 and explain their security weakness in the report. Hash in blocks; show original/received hashes and verdict for every run; verify with a standard tool; SHA-256 comparison is each experiment row's pass/fail criterion [PDF p.5]. CRC32 is per packet [PDF pp.3, 5].
- **Test files (at least three):** text **about 100 KB**, binary image/PDF **about 10 MB**, random data **100 MB** [PDF p.5]. Files larger than RAM must be handled through chunked reading and hashing [PDF p.10].
- **Network impairment:** Controlled impairment is required. Pick and document an approach: **(1)** Linux `tc netem` on the interface between two Docker containers or VMs (**recommended**); **(2)** receiver-side software emulator that drops/delays/swaps packets probabilistically with **fixed random seed**; **(3)** two physical machines on Wi-Fi **in addition to one of the preceding approaches** [PDF pp.5–6]. No method is recorded as chosen yet.
- **Repetitions and statistics:** Each configuration **at least 5 runs**; report **mean and standard deviation**, and **error bars on every plot** [PDF p.6]. The PDF does **not** specify sample versus population standard deviation; the unchanged tutor `SKILL.md` separately directs use of **sample** standard deviation.
- **Metrics for every run:** transfer time **(s)**, throughput **(MB/s)**, datagrams sent, retransmissions, duplicates received, out-of-order arrivals, and hash verdict [PDF p.6]. B1 and B2 have additional metrics in the inventory.
- **Required plots (minimum):** **(1)** throughput vs chunk size, TCP and UDP on same axes; **(2)** loss rate vs inter-packet delay with error management off; **(3)** effective throughput and retransmissions vs network loss rate with error management on; **(4)** arrival sequence number vs arrival index with reordering on [PDF p.6].
- **Environment:** Document OS, language version, CPU, endpoint connectivity, exact `netem` commands or software emulator settings [PDF p.6].
- **Report:** **8–12 PDF pages**, excluding appendices, **11-point font**, with prescribed sections listed below; **LaTeX** (IEEE/ACM template) preferred, **Word accepted**. Figures need axes with units, captions and textual references [PDF pp.6–7].
- **Presentation:** PDF slides due **2 December 2026**; **12-minute talk + 3-minute Q&A** on **3 December**. Both team members must speak (where applicable). The **10–12 slide outline** is *suggested*; a live demo is *encouraged* and screen-recording backup recommended, not explicitly mandatory [PDF pp.2, 7].
- **Code submission:** Git repository link with instructor read access **or** repository ZIP **including `.git` history**. README with exact run/reproduction commands; command-line parameters, experiment CSV sweep and plotting script, clean error exits, no hard-coded absolute paths, no large test files committed, contributions from every team member [PDF p.8]. Repository layout is only *suggested*; `docker-compose.yml` is optional.
- **Submission location:** The PDF describes accepted formats but **does not name a submission portal** [PDF pp.1, 8].
- **Late policy:** Each day late costs **10% of report and code marks**. Slides not received by the deadline mean presenting without slides or losing the presentation slot [PDF p.9].

## Requirement inventory

| ID | Requirement (faithful summary of PDF) | PDF page | Status | Verification / evidence | Rubric |
|---|---|---:|---|---|---|
| R-DEL-01 | Submit written report, presentation PDF, and code by 2 Dec 2026, 23:59 Luxembourg time; present on 3 Dec. | 1–2 | Mandatory | Three deliverables with deadline; class presentation | Report 40; code 35; presentation 25 |
| R-DEL-02 | Work in a team of two unless told otherwise; both speak; solo allowed with same work; team of three requires approval and two schemes. | 2, 10 | Mandatory/conditional | Team composition, participation, approvals if needed | Presentation Q&A 8; code B3 10 |
| R-TCP-01 | TCP server listens on a configurable port and handles one client at a time. | 2 | Mandatory | Configurable port; single-client transfer | Code TCP 8 |
| R-TCP-02 | TCP client connects, sends header with at least file name, byte size, SHA-256, then streams file. | 2 | Mandatory | Header contains all three; received file complete | Code TCP 8 |
| R-TCP-03 | TCP server saves under a **new** name so it does not overwrite original. | 2 | Mandatory | Original untouched; separate received file | Code TCP 8 |
| R-TCP-04 | TCP server hashes received file, compares against header and returns `OK` or `MISMATCH`. | 2 | Mandatory | Tested matching and mismatching verdicts | Code TCP 8 |
| R-TCP-05 | Both TCP sides log transfer time, bytes sent/received and throughput in MB/s. | 2 | Mandatory | Client and server logs | Code TCP 8 |
| R-TCP-06 | TCP `send` / `recv` buffer size is set by a command-line argument. | 2 | Mandatory | Run with different buffer settings | Code TCP 8; code quality 5 |
| R-TCP-07 | Handle partial TCP `recv`: loop until expected byte count; do not assume one `send` equals one `recv`. | 2 | Mandatory | Correct on fragmented/partial reads | Code TCP 8 |
| R-TCP-08 | Multiple simultaneous TCP clients (threads or `select`). | 2 | Bonus | Optional concurrency demonstration | Bonus up to 10 |
| R-LANG-01 | Use plain sockets; no libraries hiding transport (HTTP clients, FTP, QUIC, ZeroMQ); `sendall` allowed but `sendfile` not allowed. | 2, 10 | Mandatory restriction | Source/dependency review | Code quality 5 |
| R-UDP-01 | Build the naive UDP transfer first, then add mechanisms; make **each** mechanism command-line switchable for on/off measurements. | 3 | Mandatory | CLI flags and baseline vs enabled runs | Code UDP 7; code B3 10; B4 5 |
| R-UDP-02 | Datagram header includes packet type (1 byte), sequence number (4), payload length (2), payload CRC32 (4), and variable payload. | 3 | Mandatory minimum | Serialized header fields and sizes | Code UDP 7; B3 10; report design 8 |
| R-UDP-03 | Pack UDP header using `struct.pack` or equivalent and a fixed byte order. | 3 | Mandatory | Encode/decode test | Code UDP 7; report design 8 |
| R-UDP-04 | `START` announces file name, size, chunk size and total chunks; `END` closes session. | 3 | Mandatory | START/END transfers recorded | Code UDP 7 |
| R-B1-01 | Test at least **64, 256, 512, 1024, 1400, 4096, 8192, 32768, 65000** byte chunks. | 3 | Mandatory | Sweep data includes all nine values | Code UDP 7; report results 14 |
| R-B1-02 | Measure **throughput**, **packets sent**, **loss rate** for every chunk size. | 3 | Mandatory | Table/CSV and results plot | Report results 14 |
| R-B1-03 | Explain Ethernet MTU (~1500), IP/UDP header overhead (~1472 payload), fragmentation fragility, and why tiny chunks hurt throughput. | 3 | Mandatory | Written explanation tied to observations | Report discussion 7 |
| R-B2-01 | Add configurable inter-datagram delay; test at least **0, 10 µs, 100 µs, 1 ms, 5 ms**. | 3 | Mandatory | CLI setting, data for all five delays | Code UDP 7; report results 14 |
| R-B2-02 | Measure loss and throughput with error management **off** for each delay. | 3 | Mandatory | Runs/CSV/plot tagged error mode off | Report results 14 |
| R-B2-03 | Show receiver socket buffer overflow at zero delay; report `SO_RCVBUF` used. | 3 | Mandatory | Observed effect and buffer size in results/setup | Report methodology 8; results 14 |
| R-B2-04 | Replace fixed delay with sliding window of N packets in flight and compare. | 4 | Bonus | Optional comparative measurements | Bonus up to 10 |
| R-B3-01 | Implement and justify **one** reliable UDP scheme: Stop-and-Wait, Go-Back-N or Selective Repeat. | 4 | Mandatory | Design rationale and reliable-transfer demo | Code B3 10; report design 8 |
| R-B3-02 | Detect corrupted packets via checksum and discard them. | 4 | Mandatory | Corruption test; discards logged | Code B3 10 |
| R-B3-03 | Detect and drop duplicate packets. | 4 | Mandatory | Duplicate test; no duplicate file content | Code B3 10 |
| R-B3-04 | Use timeout, maximum retry count and clean failure if peer disappears. | 4 | Mandatory | Loss/peer-timeout test | Code B3 10 |
| R-B3-05 | Report retransmissions and effective throughput at **0%, 1%, 5%, 10%, 20%** loss. | 4 | Mandatory | Five network-loss settings with metrics | Report results 14 |
| R-B3-06 | Explain the choice of timeout value. | 4 | Mandatory | Report design/implementation rationale | Report design 8; discussion 7 |
| R-B3-07 | Compare two reliable-UDP schemes; adaptive RTT-based timeout. | 4 | Bonus (two schemes mandatory for team of three [p.10]) | Optional comparison / adaptive-timeout demo | Bonus up to 10 |
| R-B4-01 | Enable network reordering and log arrival order of sequence numbers. | 4 | Mandatory | Ordered arrival trace | Code B4 5; report results 14 |
| R-B4-02 | Append in arrival order and demonstrate a nonmatching file hash. | 4 | Mandatory | Naive reassembly hash verdict | Code B4 5; report results 14 |
| R-B4-03 | Reassemble using `seq × chunk_size` offsets **or** buffer until gaps filled; demonstrate matching hash. | 4 | Mandatory | Correct reassembly hash verdict | Code B4 5 |
| R-B4-04 | Report number of out-of-order chunks and maximum reordering distance observed. | 4 | Mandatory | Metrics and plotted arrival order | Report results 14 |
| R-BASE-01 | Before B3/B4, run plain UDP without ACKs/reordering and report how often output hash matches. | 4 | Mandatory | Baseline results with hash match frequency | Report results 14 |
| R-HASH-01 | Compare hashes of original and received files; hash equality determines success. | 5 | Mandatory | Pass/fail per run | Code TCP 8; report results 14 |
| R-HASH-02 | Use SHA-256 main hash; also compute MD5 and SHA-1 and explain why they are insecure despite accidental error detection. | 5 | Mandatory | Hash outputs and report discussion | Report discussion 7; writing 3 |
| R-HASH-03 | Read/hash in blocks so files larger than memory work. | 5, 10 | Mandatory | Chunked processing of large file | Code TCP 8; code quality 5 |
| R-HASH-04 | Print original/received hashes and verdict at end of **every run**. | 5 | Mandatory | Run logs | Report results 14 |
| R-HASH-05 | Cross-check against a standard OS hashing tool (`sha256sum`, `shasum -a 256`, or `certutil -hashfile`). | 5 | Mandatory | Cross-tool hash verification | Code TCP 8; report methodology 8 |
| R-HASH-06 | Explain CRC32 per datagram vs SHA-256 across the entire file. | 5 | Mandatory | Report explanation | Report design 8; discussion 7 |
| R-EXP-01 | Change one experimental variable at a time and describe conditions for reproducibility. | 5 | Mandatory | Documented controlled runs | Report methodology 8 |
| R-EXP-02 | Use at least three files: ~100 KB text, ~10 MB binary, 100 MB random. | 5 | Mandatory | Generated file inventory | Report methodology 8 |
| R-EXP-03 | Impose controlled loss/delay/reordering; choose and document an approved impairment approach. | 5–6 | Mandatory | Exact emulator/netem setup and logs | Report methodology 8 |
| R-EXP-04 | Software emulator, **if chosen**, uses a fixed random seed. Physical two-machine Wi-Fi tests are **in addition to** an impairment method. | 6 | Conditional | Emulator config / optional Wi-Fi setup | Report methodology 8 |
| R-EXP-05 | Record time, throughput, datagrams sent, retransmissions, duplicates, out-of-order arrivals and hash verdict **for every run**. | 6 | Mandatory | Raw CSV has all metrics | Report results 14 |
| R-EXP-06 | Run every configuration **at least five times**; report mean, standard deviation and error bars on every plot. | 6 | Mandatory | Five or more rows/configuration and statistical plots | Report methodology 8; results 14 |
| R-EXP-07 | Report OS, language version, CPU, endpoint connectivity, and exact netem commands/emulator settings. | 6 | Mandatory | Reproducible setup section | Report methodology 8 |
| R-PLOT-01 | Plot throughput vs chunk size for TCP and UDP on **same axes**. | 6 | Mandatory | Figure with both series | Report results 14 |
| R-PLOT-02 | Plot loss rate vs inter-packet delay, error management **off**. | 6 | Mandatory | Figure and raw data | Report results 14 |
| R-PLOT-03 | Plot effective throughput **and retransmissions** vs network loss rate, error management **on**. | 6 | Mandatory | Figure(s) representing both metrics | Report results 14 |
| R-PLOT-04 | Plot sequence number vs arrival index with reordering **on**. | 6 | Mandatory | Arrival-order figure | Report results 14 |
| R-REP-01 | Report PDF must be 8–12 pages excluding appendices, 11-point font. | 6 | Mandatory | PDF page count and font inspection | Report 40 |
| R-REP-02 | Abstract (150 words) states what was built and main findings **with numbers**; Introduction explains TCP vs UDP and experimental question. | 6 | Mandatory | Abstract and introduction reviewed | Report writing 3; results 14 |
| R-REP-03 | Design covers architecture, UDP header, selected reliability algorithm/rationale and a sequence diagram including a lost packet. | 6 | Mandatory | Design section and sequence diagram | Report design 8 |
| R-REP-04 | Implementation covers language, key modules, CLI parameters, problems encountered and solutions. | 6 | Mandatory | Implementation section | Report design 8; writing 3 |
| R-REP-05 | Experimental setup includes test files, impairment/emulation, hardware and repetitions. | 6 | Mandatory | Methods section | Report methodology 8 |
| R-REP-06 | Results subsections cover TCP baseline and B1–B4, each with plot/table and 2–3 sentences interpreting it. | 7 | Mandatory | Five results subsections | Report results 14 |
| R-REP-07 | Discussion addresses choosing UDP vs TCP, reliable-UDP/TCP performance gap and what would be changed. | 7 | Mandatory | Discussion section | Report discussion 7 |
| R-REP-08 | Conclusion is half a page; References include RFC 768, RFC 9293 and other properly cited sources. | 7 | Mandatory | Conclusion and bibliography | Report writing 3 |
| R-REP-09 | Appendix includes how to run code, raw results tables and short contribution statement. | 7 | Mandatory | Appendix sections | Report writing 3 |
| R-REP-10 | Every figure has labelled axes with units, a caption and reference in report text. | 7 | Mandatory | Figure audit | Report results 14; writing 3 |
| R-REP-11 | LaTeX IEEE/ACM template preferred; Word accepted. | 6 | Recommended/preferred | Selected report tool | Report writing 3 |
| R-PRES-01 | Slides in PDF due 2 Dec 2026, 23:59; 12-minute presentation plus 3-minute Q&A on 3 Dec. | 1–2, 7 | Mandatory | Slide PDF and rehearsal duration | Presentation 25 |
| R-PRES-02 | Every team member must speak and understand code; answer Q&A. | 2, 7 | Mandatory for teams | Rehearsal and role plan | Presentation Q&A 8 |
| R-PRES-03 | Suggested 10–12-slide outline: design 2 min; TCP 1 min; B1–B4 6 min; demo/recording 2 min; takeaways 1 min; Q&A 3 min. | 7 | Suggested | Optional alignment of slide plan | Presentation clarity 8; results/demo 9 |
| R-PRES-04 | Live demo encouraged; bring screen recording backup. | 7 | Recommended | Demo or recording, if prepared | Presentation results/demo 9 |
| R-CODE-01 | Submit Git repository link granting instructor read access or ZIP including `.git` directory/history. | 8 | Mandatory | Link permissions or ZIP listing | Code quality 5 |
| R-CODE-02 | README gives exact TCP/UDP run commands and commands to reproduce **every** report plot. | 8 | Mandatory | README reproduction test | Code quality 5 |
| R-CODE-03 | All host, port, file, chunk size, delay, window size, timeout, error mode and reorder on/off parameters are CLI-configurable. | 8 | Mandatory | CLI help and tests | Code quality 5 |
| R-CODE-04 | Script runs full experiment sweep and writes **raw CSV** results. | 8 | Mandatory | Reproducible CSV generation | Code quality 5 |
| R-CODE-05 | Script converts CSV data into all report plots. | 8 | Mandatory | Reproduced plots | Code quality 5 |
| R-CODE-06 | Exit cleanly on unreachable server, missing file and peer timeout. | 8 | Mandatory | Negative-path tests | Code quality 5 |
| R-CODE-07 | No hard-coded absolute paths and no large test files in repo; generate large test files with script. | 8 | Mandatory | Repo review; generator test | Code quality 5 |
| R-CODE-08 | Commits from every team member throughout the project, not one final commit. | 8 | Mandatory for teams | Git log | Code quality 5 |
| R-CODE-09 | Suggested directory layout (`tcp/`, `udp/`, `common/`, `experiments/`, `plots/`), and optional `docker-compose.yml`. | 8 | Suggested/optional | Optional layout/compose file | Code quality 5 |
| R-LATE-01 | Each late day costs 10% of report and code marks; late slides may mean presenting without slides or losing slot. | 9 | Mandatory consequence | Delivery timestamps | Deadline policy |

## Rubric (transcribed from PDF pp.8–9)

The assignment is **100 points**, with **up to 10 bonus points** available and a maximum final score of **100** [PDF p.8]. The following values are the actual rubric point allocations (not predictions of a grade).

| Component | Criterion (PDF wording) | Points | PDF page |
|---|---|---:|---:|
| Code (35) | TCP client/server works; hashes match on all test files | 8 | 8 |
| Code (35) | UDP transfer with chunk size and delay configurable (B1, B2) | 7 | 9 |
| Code (35) | Error management: checksum, ACK/timeout/retransmit, duplicates (B3) | 10 | 9 |
| Code (35) | Out-of-order handling with correct reassembly (B4) | 5 | 9 |
| Code (35) | Code quality, README, reproducible experiment and plot scripts | 5 | 9 |
| Report (40) | Design explained clearly, including packet format and sequence diagram | 8 | 9 |
| Report (40) | Methodology: controlled impairments, repetitions, environment stated | 8 | 9 |
| Report (40) | Results: correct plots with error bars for TCP and B1–B4 | 14 | 9 |
| Report (40) | Discussion: sound interpretation, TCP vs UDP trade-offs | 7 | 9 |
| Report (40) | Writing, structure, references | 3 | 9 |
| Presentation (25) | Clarity and structure, within 12 minutes | 8 | 9 |
| Presentation (25) | Quality of results shown and of the demo | 9 | 9 |
| Presentation (25) | Answers to questions; both members show understanding | 8 | 9 |
| Bonus (up to 10) | Two error-management schemes compared, adaptive timeout, sliding window, multi-client server, or two-machine tests | +2 to +5 each | 9 |

## Decisions already made (from TECHSTACK.md)

**No `TECHSTACK.md` or confirmed technology decisions were supplied with the assignment references. Do not invent choices.**

- Language: **not yet recorded** (Python 3 is a recommendation, not a requirement) [PDF p.2].
- ARQ scheme: **not yet recorded** (choose one of Stop-and-Wait, Go-Back-N, Selective Repeat) [PDF p.4].
- Impairment: **not yet recorded** (`tc netem` recommended; software emulator accepted) [PDF pp.5–6].
- Report tool: **not yet recorded** (LaTeX preferred; Word accepted) [PDF p.6].
- Team composition: **not yet recorded** (solo and teams allowed under conditions) [PDF pp.2, 10].

## Source-faithfulness notes

- The **academic integrity paragraph is reproduced exactly** from [PDF p.10]. Other entries are structured summaries traceable to the cited page; check the PDF for authoritative wording.
- The PDF does not stipulate an experiment-specific constant chunk size, a particular socket timeout value, a standard deviation *variant*, a submission platform, a mandatory demo, or a mandatory programming language. Do not introduce any as assignment requirements.
- One explicit conditional exception: **two ARQ schemes are mandatory for teams of three** [PDF p.10], despite generally being an extra-credit option [PDF p.4].
- The skill's internal 30 November target and preference for **sample** standard deviation are **tutor rules**, not requirements from the PDF.
