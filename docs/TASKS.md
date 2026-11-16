# Complete Assignment Task Inventory

**Repository:** [GadDev/binfo-networks-final-project](https://github.com/GadDev/binfo-networks-final-project)  
**Last reviewed:** 16 November 2026  
**Authoritative requirements:** *Assignment: TCP vs UDP File Transfer*, 12 Nov 2026, 10 pages  
**Status:** **Research & Learning** — application development, network setup and experimental measurement are **not authorized or started**.

This file is the **detailed checklist** of everything to plan, learn, build, measure, write, rehearse and submit. [PROGRESS.md](../PROGRESS.md) records current session status, concepts mastered and next lesson. [ROADMAP.md](ROADMAP.md) gives milestones and study methodology. Do not mark something complete unless you can point to **evidence** (a correct student explanation, a reviewed artifact, a test, a recorded run, a PDF, etc.).

### Task legend and scope

- **[M] Mandatory** — supported by the assignment; its cited page is listed.
- **[R] Recommended** — engineering, learning or workflow advice. Not a new professor requirement.
- **[B] Bonus** — expressly optional; work on these only if mandatory scope is complete.
- **[ ] Planned**, **[x] Documented completed**; researching a feature does **not** complete its eventual implementation task.
- **Dependencies** use stable task IDs. Dates are **proposed scheduling windows, not commitments or assignments from the professor**.
- **Total tasks:** 136 (4 research/documentation tasks marked complete; all implementation and experiment items unchecked). Task counts are tracking convenience, **not grading percentages**.

### Delivery gates

| Gate | Target | Exit criterion |
| --- | --- | --- |
| Research / learning | ~16–20 Nov | Explain core concepts; resolve open issues; student explicitly approves implementation plan |
| Implementation / method | ~21–28 Nov, **only if approved** | Working correct TCP/UDP components; actual controlled experiment data |
| Student-authored report and presentation | Written incrementally; finish by 30 Nov | PDF report and slides match real evidence, rehearsed and independently checked |
| Contingency / submission | 1–2 Dec | Verify packaging and submit before **2 Dec 2026 23:59 Luxembourg time** |
| In-class presentation | 3 Dec | 12-min talk + 3-min Q&A |

**Reality check:** The schedule is tight; the available hours/day and required experiment matrix are unknown. **Do not silently reduce mandatory requirements.** Ask the professor and reconsider architecture/time budget if they cannot fit.

### What is currently true

- **Done:** Stage 1 assignment audit, Stage 2 learning-content preparation, Stage 3 technical comparisons, research governance/roadmap documents (see [PROGRESS.md](../PROGRESS.md)).
- **In progress:** Kurose & Ross Chapter 1 §1.1; the first comprehension question remains unanswered.
- **Not done:** validated concept mastery; Stage 4 architecture; implementation; actual experiments/results; student-written report; presentation.
- **Decisions still conditional:** Python 3, macOS development plus Linux Docker, `tc netem`, Stop-and-Wait vs Go-Back-N.
- **Written report:** Student writes it in their own words. Tutor provides feedback on understanding, evidence, accuracy, sources and rubric — not invented results or ghostwritten findings.

---

## 0. Research, decisions and learning

**Indicative window:** 16–20 Nov (research-only).

- [x] **R-01 [R] — Audit the complete assignment and grading rubric.** Source: PDF pp.1–10. Depends on: —. **Done when:** A requirement inventory distinguishes mandatory work, recommendations and bonus work; every requirement has a page reference and verification criterion.

- [x] **R-02 [R] — Create beginner learning materials.** Source: PDF p.1. Depends on: R-01. **Done when:** Fundamentals, reliability and measurement guides exist with examples and review questions.

- [x] **R-03 [R] — Compare programming languages, reliability schemes and impairment methods.** Source: PDF pp.2–6. Depends on: R-01. **Done when:** Trade-offs are documented, recommendations are marked provisional, and assumptions are recorded.

- [x] **R-04 [R] — Document project constitution, mission, stack and roadmap.** Source: Planning recommendation. Depends on: R-01. **Done when:** Four planning documents exist with assignment boundaries, deadlines and report-authorship rules.

- [ ] **R-05 [R] — Finish Chapter 1 §§1.1–1.5 (hosts, network edge/core, delay/loss, layers).** Source: Learning recommendation; Kurose & Ross Ch.1. Depends on: —. **Done when:** Explain a local LAN versus the Internet; diagram a packet-switched path; calculate and explain sources of delay; answer tutor checks without notes.

- [ ] **R-06 [R] — Study Chapter 2 application architectures and socket programming.** Source: Learning recommendation; Kurose & Ross Ch.2. Depends on: R-05. **Done when:** Explain client/server, IP and port, TCP byte-stream framing, UDP datagram boundaries; draw a socket exchange.

- [ ] **R-07 [R] — Study Chapter 3 transport and reliable data transfer.** Source: Learning recommendation; Kurose & Ross Ch.3. Depends on: R-06. **Done when:** Correctly trace lost DATA, lost ACK, duplicates, reordering, timeout, ARQ windows and why flow control differs from congestion control.

- [ ] **R-08 [R] — Study targeted IPv4 fragmentation, CRC and experimental statistics.** Source: Learning recommendation; Kurose & Ross Chs.4,6; PDF pp.5–6. Depends on: R-07. **Done when:** Explain MTU and custom header overhead, compare CRC32/SHA-256, and compute mean and sample SD on five toy measurements.

- [ ] **R-09 [R] — Compare minimal and modular architectures (Stage 4).** Source: Planning recommendation. Depends on: R-03, R-07. **Done when:** Two architecture diagrams and directory trees are compared; prefer smallest design satisfying every PDF requirement.

- [ ] **R-10 [R] — Confirm team size and realistic hours/day.** Source: PDF pp.2,10 (team); schedule recommendation. Depends on: —. **Done when:** Solo/two-person status and daily available hours are recorded; if three, professor's permission and extra reliability scope are obtained.

- [ ] **R-11 [R] — Clarify ambiguous experiment and plot expectations with professor.** Source: PDF pp.3–7. Depends on: R-01. **Done when:** Record professor replies on file-size × configuration coverage, TCP vs UDP size axes, overflow evidence, B4 error bars and NACK handling; unresolved items stay explicit.

- [ ] **R-12 [R] — Approve implementation transition explicitly.** Source: Project phase gate; not PDF mandate. Depends on: R-05–R-11. **Done when:** Student approves stack, ARQ, impairment method, architecture, experiment matrix and time budget before any application code or infrastructure changes.

---

## 1. Architecture and project foundation (future — requires phase approval)

**Indicative window:** Target 20–21 Nov, conditional.

- [ ] **D-01 [R] — Define component boundaries and one-page data-flow diagram.** Source: PDF pp.2–4,6. Depends on: R-12. **Done when:** Client/server responsibilities, protocol state and common helpers are explained and diagrammed.

- [ ] **D-02 [R] — Specify TCP header encoding and framing.** Source: PDF p.2. Depends on: D-01. **Done when:** Header carries filename, file length and SHA-256; byte order, lengths and read-until-size logic are unambiguous.

- [ ] **D-03 [R] — Specify UDP packet header and START/END metadata encoding.** Source: PDF p.3. Depends on: D-01. **Done when:** 1+4+2+4 byte fields, byte order, legal packet types, payload lengths, sequence numbering and total-chunk meaning are documented.

- [ ] **D-04 [R] — Specify lifecycle and recovery for lost START, DATA, ACK and END.** Source: Reliability design recommendation; PDF pp.3–4. Depends on: D-03. **Done when:** Sender/receiver state diagrams cover normal transfer, loss, duplicate control packets, timeout and clean termination; identify which features are strictly mandated.

- [ ] **D-05 [R] — Define CLI contract and defaults.** Source: PDF p.8. Depends on: D-01. **Done when:** Host, port, file, chunk size, delay, window size, timeout, error-management and reordering controls are enumerated.

- [ ] **D-06 [R] — Define metric semantics and CSV schema before experiments.** Source: PDF p.6. Depends on: D-01. **Done when:** Consistent units, time boundaries, per-run hash verdict, original sends versus retries, duplicates, reorder distance and failure treatment are specified.

- [ ] **D-07 [R] — Set up minimal repository layout and dependencies.** Source: PDF p.8 (suggested layout). Depends on: R-12,D-01. **Done when:** Only needed modules exist and are documented; no prohibited transport abstraction or hard-coded absolute paths.

- [ ] **D-08 [R] — Plan correctness, impairment and reproducibility tests.** Source: PDF pp.4–6,8. Depends on: D-02–D-06. **Done when:** A test matrix covers partial reads, empty/last chunk, loss, duplicates, corruption, reordering, missing file, dead peer and hash failures.

---

## 2. TCP baseline — Part A

**Indicative window:** Target 21–22 Nov, conditional.

- [ ] **T-01 [M] — TCP server listens on configurable port and handles one client at a time.** Source: PDF p.2. Depends on: D-02,D-05. **Done when:** A client can connect and complete a single transfer; concurrency is not required.

- [ ] **T-02 [M] — TCP client establishes connection and announces file metadata.** Source: PDF p.2. Depends on: T-01,D-02. **Done when:** Server decodes filename, byte count and source SHA-256 from header.

- [ ] **T-03 [M] — Stream file content without loading entire file into memory.** Source: PDF pp.2,10. Depends on: T-02. **Done when:** Receiver obtains expected bytes for 100 MB file with bounded buffering.

- [ ] **T-04 [M] — Handle partial TCP writes/reads and end-of-stream framing.** Source: PDF p.2. Depends on: T-03. **Done when:** Short reads and malformed/incomplete transfers are correctly handled; no one-send/one-recv assumption.

- [ ] **T-05 [M] — Save under a new destination name and preserve original.** Source: PDF p.2. Depends on: T-03. **Done when:** Same-host testing never overwrites the source file.

- [ ] **T-06 [M] — Compute destination SHA-256 and reply OK or MISMATCH.** Source: PDF p.2. Depends on: T-03–T-05. **Done when:** Client receives the exact verdict from receiver and bad data produces MISMATCH.

- [ ] **T-07 [M] — Log elapsed time, transferred byte counts and MB/s at both endpoints.** Source: PDF p.2. Depends on: T-03,D-06. **Done when:** Sender/receiver output documents definitions and reports required metrics.

- [ ] **T-08 [M] — Configure send/receive buffer size from CLI.** Source: PDF p.2. Depends on: T-01–T-04,D-05. **Done when:** At least two buffer settings can be selected without code changes.

- [ ] **T-09 [R] — Review negative cases and all required file classes.** Source: PDF pp.5,8,10. Depends on: T-01–T-08. **Done when:** Missing source, unreachable service, interrupted transfer, hash mismatch and large file fail safely or pass correctly.

- [ ] **T-10 [R] — Explain TCP stream framing and record design rationale in personal notes.** Source: PDF pp.6–7. Depends on: T-01–T-09. **Done when:** Student can explain how partial reads and length framing work, for report and Q&A.

---

## 3. UDP packet format and naive baseline

**Indicative window:** Target 23–24 Nov, conditional.

- [ ] **U-01 [M] — Implement UDP client/server using plain datagram sockets.** Source: PDF pp.3–4,10. Depends on: D-03,D-05. **Done when:** Endpoints send and receive application datagrams without external transport abstractions.

- [ ] **U-02 [M] — Encode/decode packet type, sequence number, length, CRC32 and payload.** Source: PDF p.3. Depends on: U-01. **Done when:** Header fields have specified 1/4/2/4-byte widths; fixed byte order; malformed packets detected.

- [ ] **U-03 [M] — Send/parse START metadata (filename, size, chunk size, total chunks).** Source: PDF p.3. Depends on: U-02. **Done when:** Receiver obtains and validates all mandatory transfer metadata.

- [ ] **U-04 [M] — Send/parse END session marker.** Source: PDF p.3. Depends on: U-02–U-03. **Done when:** Session completion can be detected; invalid/incomplete sessions are not silently marked successful.

- [ ] **U-05 [M] — Split file into chunks and assign sequence numbers including final short chunk.** Source: PDF p.3. Depends on: U-02–U-04. **Done when:** No dropped/duplicated last-byte range; expected chunk count matches file size.

- [ ] **U-06 [M] — Provide a naive UDP mode with ACK/reordering handling off.** Source: PDF pp.3–4. Depends on: U-01–U-05. **Done when:** File can be sent and reconstructed naively; SHA-256 match/mismatch observed and recorded.

- [ ] **U-07 [M] — Provide CLI toggles for mechanisms and packet pacing.** Source: PDF pp.3,8. Depends on: U-01,D-05. **Done when:** Delay, error mode, reorder mode and window/timeout parameters are exposed and recorded.

- [ ] **U-08 [M] — Measure naive baseline hash-match frequency across repeated runs.** Source: PDF p.4; p.6 repeats. Depends on: U-06,D-06. **Done when:** Hash-match counts/rate are reported with run count and test configuration.

- [ ] **U-09 [R] — Exercise malformed headers, too-large chunks, duplicate START/END and incomplete file.** Source: Robustness recommendation; PDF pp.3–4. Depends on: U-02–U-08. **Done when:** Invalid datagrams do not crash receiver or produce false successful transfers.

- [ ] **U-10 [R] — Explain custom application header vs UDP/IP headers.** Source: PDF pp.3,6. Depends on: U-02. **Done when:** Diagram/notes distinguish 11-byte custom header from 8-byte UDP header and IP MTU.

---

## 4. B1 — UDP chunk-size experiments

**Indicative window:** Target 24–27 Nov, conditional.

- [ ] **B1-01 [M] — Support every required size: 64,256,512,1024,1400,4096,8192,32768,65000 bytes.** Source: PDF p.3. Depends on: U-05,U-07. **Done when:** Nine configurations accepted, attempted and documented without manual code edits.

- [ ] **B1-02 [M] — Record throughput, datagrams sent, and loss rate for each size.** Source: PDF p.3. Depends on: B1-01,D-06. **Done when:** Raw run records include all three values and hash verdict.

- [ ] **B1-03 [M] — Study fragmentation near/above ~1472-byte IPv4 UDP payload.** Source: PDF p.3. Depends on: B1-01. **Done when:** Report explains MTU 1500, IPv4+UDP headers, custom 11-byte header, and fragmentation fragility.

- [ ] **B1-04 [M] — Study overhead with very small datagrams.** Source: PDF p.3. Depends on: B1-02. **Done when:** Report explains header overhead and system-call/packet-processing cost without unsupported performance claims.

- [ ] **B1-05 [R] — Repeat each configuration at least five times and preserve failures.** Source: PDF p.6. Depends on: B1-02. **Done when:** Five+ raw observations per configuration and sample summary; no discarded failed runs.

- [ ] **B1-06 [M] — Produce required TCP/UDP throughput-vs-size comparison.** Source: PDF p.6. Depends on: B1-02,T-07,B1-05. **Done when:** Common-axis chart shows defined TCP buffer vs UDP chunk-size comparison, units, caption and error bars.

---

## 5. B2 — Inter-packet delay experiments

**Indicative window:** Target 24–27 Nov, conditional.

- [ ] **B2-01 [M] — Support all delays: 0,10µs,100µs,1ms,5ms.** Source: PDF p.3. Depends on: U-07. **Done when:** Every exact setting can be selected and logged.

- [ ] **B2-02 [M] — Run delay sweep with error management off.** Source: PDF p.3. Depends on: B2-01,U-06. **Done when:** Configuration makes ACK/retransmission behavior inactive and is recorded.

- [ ] **B2-03 [M] — Measure loss and throughput for each delay.** Source: PDF p.3. Depends on: B2-02,D-06. **Done when:** Results include matching/mismatching file verdicts and timing/byte definitions.

- [ ] **B2-04 [M] — Investigate and demonstrate receiver-buffer overflow at zero delay.** Source: PDF p.3. Depends on: B2-02. **Done when:** Observed overflows/loss have evidence; if not reproducible, limitation is documented and professor consulted.

- [ ] **B2-05 [M] — Report receiver SO_RCVBUF setting/value.** Source: PDF p.3. Depends on: B2-02. **Done when:** Experimental method records the buffer actually reported and any OS interpretation caveats.

- [ ] **B2-06 [M] — Create loss-rate-versus-delay plot with error bars.** Source: PDF p.6. Depends on: B2-03. **Done when:** Plot includes all five delays, units, mean/variability, caption and reference in report.

- [ ] **B2-07 [R] — Assess accuracy of microsecond pacing on selected OS/runtime.** Source: Scientific-validity recommendation; PDF p.3. Depends on: B2-01. **Done when:** Notes distinguish requested delays from actual scheduling and disclose timing uncertainty.

---

## 6. B3 — Reliable UDP under packet loss

**Indicative window:** Target 25–28 Nov, conditional.

- [ ] **B3-01 [M] — Choose exactly one supported ARQ scheme and justify it.** Source: PDF p.4. Depends on: R-11,R-12,D-04. **Done when:** Design specifies Stop-and-Wait, Go-Back-N, or Selective Repeat, and rationale covers experiment feasibility.

- [ ] **B3-02 [M] — Validate CRC32 and discard corrupted packets.** Source: PDF pp.3–4. Depends on: U-02,B3-01. **Done when:** Corrupted payload is rejected and not written as valid file data.

- [ ] **B3-03 [M] — Define and process acknowledgements for selected ARQ.** Source: PDF p.4. Depends on: B3-01. **Done when:** ACK behavior is unambiguous for sender and receiver; ACK-loss scenario handled.

- [ ] **B3-04 [M] — Track sender sequence and outstanding/in-flight state.** Source: PDF p.4. Depends on: B3-03. **Done when:** State identifies which chunks need ACK/retransmission, consistent with selected scheme.

- [ ] **B3-05 [M] — Implement timeout and retransmission on missing acknowledgement.** Source: PDF p.4. Depends on: B3-03–B3-04. **Done when:** A deliberately lost DATA or ACK can be recovered where retry budget permits.

- [ ] **B3-06 [M] — Define maximum retry count and clean peer-disappearance failure.** Source: PDF p.4. Depends on: B3-05. **Done when:** Absent peer eventually produces a bounded failure, not an endless hang.

- [ ] **B3-07 [M] — Detect/drop duplicates, preserve integrity.** Source: PDF p.4. Depends on: B3-03–B3-06. **Done when:** Duplicate DATA and retransmissions do not append repeated file bytes.

- [ ] **B3-08 [M] — Explain timeout value and its trade-offs.** Source: PDF p.4. Depends on: B3-05. **Done when:** Student can justify a fixed timeout in terms of RTT, false retransmissions and loss.

- [ ] **B3-09 [M] — Test configured loss rates 0%,1%,5%,10%,20%.** Source: PDF p.4. Depends on: B3-02–B3-08,E-02. **Done when:** Every loss condition has documented impairment settings and repeated raw runs.

- [ ] **B3-10 [M] — Count retransmissions and calculate verified effective throughput.** Source: PDF p.4. Depends on: B3-09,D-06. **Done when:** Counts, verified completion, MB/s, failures and integrity verdict are recorded for each run.

- [ ] **B3-11 [M] — Produce effective-throughput and retransmissions versus loss plots.** Source: PDF p.6. Depends on: B3-09–B3-10. **Done when:** Both measures plotted with correctly defined axes/units, error bars, captions and report references.

- [ ] **B3-12 [R] — Analyze packet lifecycle including lost START, ACK and END.** Source: Reliability completeness recommendation; PDF pp.3–4. Depends on: D-04,B3-05. **Done when:** Edge-case tests or documented protocol limits cover lost control messages and session completion.

- [ ] **B3-13 [R] — Review window and pacing interaction against scheme selection.** Source: Quality recommendation; PDF pp.4,8. Depends on: B3-01,D-05. **Done when:** Command-line options have sensible meaning even when using Stop-and-Wait; unsupported modes are not misrepresented.

---

## 7. B4 — Reordering and correct file reconstruction

**Indicative window:** Target 26–28 Nov, conditional.

- [ ] **B4-01 [M] — Enable reproducible packet reordering in the selected emulator.** Source: PDF pp.4,6. Depends on: E-02,U-06. **Done when:** Valid datagrams actually arrive out of order, with settings recorded.

- [ ] **B4-02 [M] — Log arrival index and observed sequence number for each DATA chunk.** Source: PDF p.4. Depends on: B4-01. **Done when:** Raw arrival trace can reconstruct the observed order unambiguously.

- [ ] **B4-03 [M] — Demonstrate incorrect arrival-order concatenation.** Source: PDF p.4. Depends on: B4-02. **Done when:** Deliberately naive receiver produces a file with a SHA-256 MISMATCH under observed reordering.

- [ ] **B4-04 [M] — Implement sequence-aware placement or buffering.** Source: PDF p.4. Depends on: B4-02,D-03. **Done when:** Data is reassembled by sequence rather than reception order, including last short chunk.

- [ ] **B4-05 [M] — Verify corrected file matches original SHA-256.** Source: PDF p.4. Depends on: B4-04,H-03. **Done when:** Same reordering setup results in MATCH after correct reconstruction.

- [ ] **B4-06 [M] — Measure number of reordered chunks and largest reordering distance.** Source: PDF p.4. Depends on: B4-02,D-06. **Done when:** Metric definitions and observed values are present in raw run records and report.

- [ ] **B4-07 [M] — Create arrival-index-versus-sequence-number plot.** Source: PDF p.6. Depends on: B4-02. **Done when:** Figure shows actual disorder, labeled axes and report discussion; handle PDF's general error-bar requirement explicitly.

- [ ] **B4-08 [R] — Test duplicates/loss combined with reordering.** Source: Robustness recommendation; PDF pp.4–6. Depends on: B3-07,B4-04. **Done when:** Receiver never silently reports success on incorrect output; actual behavior documented.

---

## 8. Hashing, fixtures, test environment and automation

**Indicative window:** Plan 20 Nov; execute 21–29 Nov only after approval.

- [ ] **H-01 [M] — Create incremental file hashing for SHA-256, MD5 and SHA-1.** Source: PDF p.5. Depends on: R-12. **Done when:** All three hash algorithms work on streams without loading whole file into RAM.

- [ ] **H-02 [M] — Compute and compare original/received SHA-256 after every run.** Source: PDF p.5. Depends on: H-01. **Done when:** Original digest, received digest and MATCH/MISMATCH are printed and recorded for TCP and UDP.

- [ ] **H-03 [M] — Cross-check SHA-256 against an OS utility.** Source: PDF p.5. Depends on: H-01. **Done when:** Python digests agree with a documented utility (e.g. shasum on macOS or sha256sum on Linux).

- [ ] **H-04 [M] — Explain MD5/SHA-1 limitations and CRC32 vs SHA-256.** Source: PDF p.5. Depends on: H-01,U-02. **Done when:** Student's report accurately distinguishes accidental error detection, collisions and file-level verification.

- [ ] **E-01 [M] — Prepare three file classes: ~100 KB text, ~10 MB image/PDF, 100 MB random.** Source: PDF p.5. Depends on: R-12. **Done when:** Fixtures exist with recorded sizes and original hashes; large fixtures are not committed.

- [ ] **E-02 [M] — Set up documented controlled impairment using tc netem or software emulator.** Source: PDF pp.5–6. Depends on: R-12,D-01. **Done when:** Loss, delay and reordering can be configured and reproduced; setup and reset procedures recorded.

- [ ] **E-03 [M] — Record OS, language version, CPU, network topology and impairment commands/settings.** Source: PDF p.6. Depends on: E-02. **Done when:** Environment table is complete enough for independent repetition.

- [ ] **E-04 [M] — Change one independent parameter at a time.** Source: PDF p.5. Depends on: D-06,E-02. **Done when:** Per-experiment control variables are recorded and held constant.

- [ ] **E-05 [M] — Run each required configuration at least five times.** Source: PDF p.6. Depends on: E-01–E-04,B1-01,B2-01,B3-09,B4-01. **Done when:** Raw data includes ≥5 runs for each actually required setting; professor-confirmed matrix reflected.

- [ ] **E-06 [M] — Record all per-run metrics and hash verdict (including failed transfers).** Source: PDF pp.5–6. Depends on: D-06,E-05. **Done when:** Every row includes elapsed seconds, MB/s, packet/retry/duplicate/reorder counters and SHA-256 verdict.

- [ ] **E-07 [M] — Compute mean and sample standard deviation correctly.** Source: PDF p.6. Depends on: E-05–E-06. **Done when:** Statistics are derived only from raw runs; treatment of failures is described.

- [ ] **E-08 [M] — Add clearly defined error bars to required plots.** Source: PDF pp.6–7. Depends on: E-07. **Done when:** Plot variability measure and n are documented; axes have labels and units.

- [ ] **E-09 [M] — Create full experiment-sweep script writing raw CSV.** Source: PDF p.8. Depends on: E-01–E-08. **Done when:** One documented command reproduces all required configurations and outputs inspectable CSV.

- [ ] **E-10 [M] — Create plotting script that regenerates report figures from CSV.** Source: PDF p.8. Depends on: E-08–E-09. **Done when:** Running plotting tool against raw CSV produces every required graph without manual editing.

- [ ] **E-11 [M] — Handle negative cases: missing file, server unreachable, peer timeout.** Source: PDF p.8. Depends on: T-09,B3-06. **Done when:** Fail clearly with exit status and message; no file mislabeled as successfully transferred.

- [ ] **E-12 [M] — Ensure no hard-coded absolute paths or large committed test fixtures.** Source: PDF p.8. Depends on: D-07,E-01. **Done when:** Fresh checkout works with documented paths and generated fixtures; repo doesn't include huge inputs.

- [ ] **E-13 [M] — Maintain incremental Git history per participating student.** Source: PDF p.8. Depends on: R-10. **Done when:** Repository history shows meaningful work over time; teammate contributions visible if applicable.

- [ ] **E-14 [R] — Validate clean-room reproduction against README.** Source: PDF p.8. Depends on: E-09–E-13. **Done when:** Fresh checkout/environment can run transfers and regenerate plots exactly as documented.

---

## 9. Student-authored written report (40% of grade)

**Indicative window:** Research notes now; drafting throughout project; finalize 29–30 Nov.

- [ ] **W-01 [M] — Choose report tool; verify 8–12-page PDF and 11-point font.** Source: PDF p.6. Depends on: R-12. **Done when:** Tool produces compliant PDF; LaTeX preferred, Word accepted.

- [ ] **W-02 [R] — Maintain personal reading/evidence notebook and citation log.** Source: Academic quality recommendation; PDF p.10. Depends on: R-05. **Done when:** Notes distinguish facts, theory, assumptions, decisions and later actual measurements.

- [ ] **W-03 [M] — Write introduction and experiment research question yourself.** Source: PDF p.6. Depends on: R-07. **Done when:** Student-authored text explains TCP/UDP differences and the question tested, with citations.

- [ ] **W-04 [M] — Describe architecture, UDP packet layout and ARQ rationale.** Source: PDF p.6. Depends on: D-01–D-04,B3-01. **Done when:** Student's explanation matches actual final design and packet fields.

- [ ] **W-05 [M] — Draw and explain a sequence diagram featuring a lost packet.** Source: PDF p.6. Depends on: W-04. **Done when:** Diagram shows sender/receiver flow, loss, timeout/ACK/recovery as implemented.

- [ ] **W-06 [M] — Explain implementation language, modules, parameters and problems/fixes.** Source: PDF p.6. Depends on: T-01–T-10,U-01–U-10. **Done when:** Student's explanation matches code and actual issues encountered.

- [ ] **W-07 [M] — Describe test files, impairment, hardware and repetitions.** Source: PDF pp.6–7. Depends on: E-01–E-06. **Done when:** Independent reader can reproduce environment and protocol conditions.

- [ ] **W-08 [M] — Write Results subsection for TCP and each B1–B4 experiment.** Source: PDF p.7. Depends on: B1-06,B2-06,B3-11,B4-07,E-07. **Done when:** Student-authored observations reference real plots/tables; each subsection says what the results show.

- [ ] **W-09 [M] — Include four mandatory figures with axes, units, captions and in-text references.** Source: PDF pp.6–7. Depends on: E-08–E-10,W-08. **Done when:** All figures match CSV and explain SD/error bars and comparison controls.

- [ ] **W-10 [M] — Write Discussion interpreting TCP versus UDP and measured gap.** Source: PDF p.7. Depends on: W-08–W-09. **Done when:** Student explains observed results, limitations, when UDP is preferable and what could be improved.

- [ ] **W-11 [M] — Write half-page conclusion grounded in findings.** Source: PDF p.7. Depends on: W-10. **Done when:** Student summarizes verified results and limitations without invented claims.

- [ ] **W-12 [M] — Write ~150-word numerical abstract last.** Source: PDF p.6. Depends on: W-08–W-11. **Done when:** Abstract states actual methods and specific observed quantitative findings.

- [ ] **W-13 [M] — Cite RFC 768, RFC 9293, textbook, external help and AI use correctly.** Source: PDF pp.7,10. Depends on: W-03–W-12. **Done when:** All borrowed material and substantive assistance acknowledged consistently.

- [ ] **W-14 [M] — Append commands, raw results tables and contributions statement.** Source: PDF p.7. Depends on: E-09,W-13,R-10. **Done when:** Appendix supports reproducibility and states who did what (including solo work if relevant).

- [ ] **W-15 [R] — Submit draft sections for feedback then revise in own words.** Source: Student learning workflow recommendation. Depends on: W-03–W-14. **Done when:** Tutor checks accuracy, evidence, clarity, figure interpretation, citations; student remains author.

- [ ] **W-16 [M] — Audit report against rubric: design 8, method 8, results 14, discussion 7, writing 3.** Source: PDF p.9. Depends on: W-01–W-14. **Done when:** Each rubric criterion has explicit supporting report pages/figures.

- [ ] **W-17 [M] — Final PDF verification: layout, page limit, font and readability.** Source: PDF pp.6–7. Depends on: W-16. **Done when:** PDF opens correctly, has 11-pt text, 8–12 pages excluding appendices, legible labels and complete references.

---

## 10. Presentation (25% of grade)

**Indicative window:** Prepare alongside findings; rehearse by 30 Nov.

- [ ] **S-01 [M] — Plan 12-minute talk and 3-minute Q&A.** Source: PDF pp.2,7. Depends on: R-10. **Done when:** Storyboard covers the required results within the exact talk timing.

- [ ] **S-02 [R] — Target ~10–12 slides, using professor's suggested arc.** Source: PDF p.7 (suggested). Depends on: S-01. **Done when:** Slides cover problem/design (~2m), TCP (~1m), B1–B4 (~6m), demo (~2m), takeaways (~1m).

- [ ] **S-03 [R] — Explain architecture, 11-byte packet header and chosen ARQ visually.** Source: PDF p.7 (suggested). Depends on: W-04–W-05. **Done when:** Audience sees clear diagrams with accurate explanations.

- [ ] **S-04 [M] — Present accurate TCP, B1, B2, B3 and B4 experimental evidence.** Source: PDF pp.7,9 (results rubric). Depends on: W-08–W-09. **Done when:** Plots are sourced from own runs with legible units; no invented results.

- [ ] **S-05 [R] — Prepare a reliable live demo of UDP under loss/reordering.** Source: PDF p.7 (encouraged). Depends on: B3-09,B4-05. **Done when:** Transfer displays final matching SHA-256; conditions visible.

- [ ] **S-06 [R] — Record backup demonstration.** Source: PDF p.7 (recommendation). Depends on: S-05. **Done when:** Recording is playable independently of network conditions.

- [ ] **S-07 [M] — Assign speaking parts if team; every member must speak.** Source: PDF pp.2,7. Depends on: R-10,S-01. **Done when:** All team members have rehearsed speaking roles; solo case documented.

- [ ] **S-08 [M] — Prepare for examiner questions about all code/design/measurement choices.** Source: PDF pp.7,9–10. Depends on: T-10,W-16. **Done when:** Student can explain packet format, timeout, failures, reordering, plots and limitations without reading code blindly.

- [ ] **S-09 [M] — Rehearse and time a complete 12-minute talk.** Source: PDF p.7. Depends on: S-02–S-08. **Done when:** Timed rehearsal finishes at or under 12 min; Q&A rehearsal covers common hard questions.

- [ ] **S-10 [M] — Export final PDF slides and validate display/legibility.** Source: PDF pp.1–2,7. Depends on: S-09. **Done when:** Submitted PDF is readable on a classroom display before 2 Dec deadline.

---

## 11. Final delivery and validation

**Indicative window:** 30 Nov internal complete; 1–2 Dec buffer.

- [ ] **F-01 [M] — Update root README with exact TCP/UDP commands and plot reproduction steps.** Source: PDF p.8. Depends on: E-09–E-10. **Done when:** New reader can run the applications, all sweeps and all figure generation.

- [ ] **F-02 [M] — Run all TCP integrity checks across required file types.** Source: PDF pp.2,5,9. Depends on: T-09,E-01,H-02. **Done when:** Every required file class shows verified TCP success; failures explained.

- [ ] **F-03 [M] — Confirm UDP B1–B4 requirement traceability.** Source: PDF pp.3–6,9. Depends on: B1-06,B2-06,B3-11,B4-07. **Done when:** All B1–B4 configurations, plots, error management and reordering evidence linked to raw runs.

- [ ] **F-04 [M] — Verify every reported number against raw CSV and hash verdict.** Source: PDF pp.5–7,10. Depends on: W-16,E-09. **Done when:** No fictitious values; missing/failed runs disclosed; tables and charts derived from recorded measurements.

- [ ] **F-05 [M] — Validate repository and instructor access / ZIP with .git history.** Source: PDF p.8. Depends on: E-13. **Done when:** Instructor can read Git history and required project files; any ZIP includes .git.

- [ ] **F-06 [M] — Check commits from each participating student, if applicable.** Source: PDF p.8. Depends on: R-10,F-05. **Done when:** Meaningful incremental history exists for each participant.

- [ ] **F-07 [M] — Check academic citations and report authorship.** Source: PDF p.10. Depends on: W-13,W-17. **Done when:** AI/external references cited, student can defend every line and finding.

- [ ] **F-08 [M] — Check final report PDF, presentation PDF and code package.** Source: PDF pp.1–2,6–10. Depends on: F-01–F-07,S-10. **Done when:** All three deliverables have required formats and no prohibited dependencies.

- [ ] **F-09 [R] — Use 1–2 Dec as buffer for genuine failures and submission verification.** Source: Scheduling recommendation. Depends on: F-08. **Done when:** Only real issues repaired; no fabricated results or incomplete work misrepresented.

- [ ] **F-10 [M] — Submit code + written report PDF + slides PDF by 2 Dec 23:59 Luxembourg.** Source: PDF pp.1–2,8–10. Depends on: F-08. **Done when:** All deliverables accepted before cutoff; access/link verified.

- [ ] **F-11 [M] — Present in class on 3 Dec (12 minutes + 3-minute Q&A).** Source: PDF pp.1–2,7. Depends on: F-10,S-09. **Done when:** Presentation delivered, questions answered accurately, participation rules satisfied.

---

## 12. Bonus work — NOT on the critical path

**Indicative window:** Only after all mandatory criteria are secure.

- [ ] **X-01 [B] — Compare two UDP reliability schemes.** Source: PDF pp.4,9. Depends on: B3-01–B3-11. **Done when:** Two correctly implemented schemes compared using controlled identical experiments.

- [ ] **X-02 [B] — Adaptive retransmission timeout based on measured RTT.** Source: PDF pp.4,9. Depends on: B3-05. **Done when:** Timeout adapts from observed RTT and its effects are measured.

- [ ] **X-03 [B] — Fixed delay versus sliding-window pacing comparison.** Source: PDF pp.3–4,9. Depends on: B2-06. **Done when:** Reproducible results compare N in-flight versus fixed delay.

- [ ] **X-04 [B] — Multi-client TCP server.** Source: PDF pp.2,9. Depends on: T-01–T-09. **Done when:** Concurrent transfers complete correctly without corrupting files.

- [ ] **X-05 [B] — Extra physical two-machine experiments.** Source: PDF pp.6,9. Depends on: E-02. **Done when:** Results supplement controlled emulation and describe additional hardware/path variability.

- [ ] **X-06 [B] — Docker Compose pre-configured impairment environment.** Source: PDF p.8 (optional). Depends on: E-02. **Done when:** A documented container topology simplifies independent reproduction and demo.

---

## Rubric coverage and cross-check

| Rubric component | Points | Task ranges |
| --- | ---: | --- |
| TCP functionality | 8 | T-01–T-10, H-01–H-03, F-02 |
| UDP chunking and delay | 7 | U-01–U-10, B1-01–B1-06, B2-01–B2-07 |
| UDP reliability | 10 | B3-01–B3-13, H-01–H-04 |
| Out-of-order correctness | 5 | B4-01–B4-08 |
| Code quality and reproducibility | 5 | D-01–D-08, E-01–E-14, F-01–F-06 |
| Report design | 8 | W-03–W-06 |
| Report experimental method | 8 | E-01–E-08, W-07 |
| Report results | 14 | B1-06, B2-06, B3-11, B4-07, W-08–W-09 |
| Report discussion | 7 | W-10–W-11 |
| Report writing / references | 3 | W-01, W-12–W-17 |
| Presentation structure | 8 | S-01–S-03, S-09–S-10 |
| Presentation results/demo | 9 | S-04–S-06 |
| Presentation Q&A | 8 | S-07–S-09 |

**Points are from [PDF pp.8–9]**. Bonus opportunities up to 10 points are tracked independently; total grade is capped at 100.

## When to update this inventory

At the end of each interactive tutor/review session, update [PROGRESS.md](../PROGRESS.md) first with what the student demonstrably knows and current blockers. Mark tasks here completed **only when the stated acceptance criterion is satisfied**, and cite the supporting commit, actual test run, graph, reviewed draft or submitted file.

Never equate reading a chapter, receiving an explanation, or designing a plan with mastering the concept or implementing a graded component.

## Key references

- Course PDF: *Assignment: TCP vs UDP File Transfer*, 12 Nov 2026, pp.1–10.
- [Kurose & Ross teaching resources](https://gaia.cs.umass.edu/kurose_ross/ppt.php).
- [RFC 768 — UDP](https://www.rfc-editor.org/rfc/rfc768).
- [RFC 9293 — TCP](https://www.rfc-editor.org/rfc/rfc9293).
