# Technical Research & Proposed Technology Stack

**Phase:** Research & Discovery — nothing implemented or installed  
**Status:** All technology choices are **proposed**, not approved.  
**Environment reported by student:** macOS; strongest relevant language Python; access to Docker or a Linux VM; team arrangement unknown.

This is a decision record for architectural research, not a dependency list for immediate installation. The assignment PDF takes precedence over any recommendation.

## 1. Fixed constraints from the assignment

- **Use plain sockets.** HTTP clients, FTP, QUIC, ZeroMQ, `sendfile`, or other abstractions that hide transport are not permitted. TCP `sendall` is explicitly allowed (PDF pp.2, 10).
- TCP must frame a stream with at least filename, byte size and SHA-256; handle partial reads; calculate both endpoints' statistics (p.2).
- UDP uses a minimum custom 11-byte header: 1-byte packet type, 4-byte sequence number, 2-byte payload length, 4-byte CRC32; START and END control messages and fixed byte order (p.3).
- Support all B1/B2 settings and selected B3 ARQ; implement B4 reordering demonstration (pp.3–4).
- Hash files incrementally; compute SHA-256 plus MD5/SHA-1 and explain why the latter two are insecure for collision resistance (p.5).
- Run controlled network impairments, at least five trials/configuration, statistics, error bars and all four required plots (pp.5–6).
- All parameters (host, port, file, chunk size, delay, window, timeout, error management, reorder handling) must be CLI-configurable (p.8).

## 2. Language decision

**Proposed:** Python 3 using the standard library for transfer logic and measurements; Matplotlib later for plots. Python is directly recommended in the assignment and is the student's strongest relevant language.

| Criterion | Python | Go | Java | Rust |
| --- | --- | --- | --- | --- |
| Plain socket APIs | `socket` | `net` | `Socket` / `DatagramSocket` | `std::net` |
| Binary packet layout | `struct` | `encoding/binary` | `ByteBuffer` | byte slices / explicit parsing |
| Hash / CRC | `hashlib`, `zlib` | `crypto/*`, `hash/crc32` | `MessageDigest`, CRC32 | std or vetted crates |
| Good for experiments/plots | Strong Python ecosystem | External plotting needed | External plotting needed | External plotting needed |
| Learning cost for this student | Lowest | Unconfirmed | Familiarity unconfirmed | Likely higher |
| Interpreter overhead | Relevant for tiny UDP chunks | Compiled | JIT/runtime | Compiled |
| Schedule fit | Strongest provisional | Credible runner-up | Viable | Higher risk if unfamiliar |

**Alternatives:** Go is the leading runner-up, especially if a compiled networking implementation or window-based concurrency becomes important. Java remains fully acceptable; Rust offers control/performance but introduces higher learning risk within this deadline.

**Critical scientific limitation:** Python UDP application-level per-packet overhead and ACK handling may differ from the kernel TCP implementation. A measured result compares **these two applications in the stated environment**, not the theoretical protocol speeds in isolation.

**Confidence:** High for compliance and short-term suitability. No performance claim until actual testing.

### Candidate Python facilities (for future phases only)

| Use | Module | Status |
| --- | --- | --- |
| TCP and UDP | `socket` | Standard library, proposed |
| Binary packet format | `struct` | Standard library, proposed |
| File hashes | `hashlib` | Standard library, proposed |
| CRC32 | `zlib.crc32` | Standard library, proposed |
| CLI configuration | `argparse` | Standard library, proposed |
| High-resolution elapsed clock | `time.perf_counter_ns` | Standard library, proposed |
| Raw CSV | `csv` | Standard library, proposed |
| Mean and sample SD | `statistics` | Standard library, proposed |
| Unit tests | `unittest` | Standard library, proposed |
| Plots / error bars | Matplotlib | External library, proposed; do not install yet |

No language version has been pinned and no requirements file created.

## 3. UDP reliability decision

**Conditional proposal:** Stop-and-Wait, with Go-Back-N as the preferred alternative if experiment run-time budgets make Stop-and-Wait impractical.

| Attribute | Stop-and-Wait | Go-Back-N | Selective Repeat |
| --- | --- | --- | --- |
| Outstanding DATA | One | Window N | Window N |
| ACK scheme | Individual | Usually cumulative | Individual |
| Retransmission | Current chunk | Outstanding range | Missing chunk(s) |
| Receiver complexity | Low | Low–medium | Higher |
| Reordering handling | Separate B4 demo likely | Separate B4 demo likely | Natural buffering support |
| Loss/RTT efficiency | Weak on long RTT | Better potential | Better potential |
| Schedule risk | Lowest | Medium | Highest |

**Reason for conditional status:** A 100 MB file in 64-byte chunks has 1,562,500 chunks (decimal MB), and even a hypothetical 20 ms RTT would make a one-at-a-time scheme take many hours. The assignment does not explicitly require every file size crossed with every chunk/delay/loss setting. Confirm the experiment matrix before fixing an ARQ choice.

One ARQ scheme is mandatory; a second scheme is bonus only (PDF p.4). The separate B4 demonstration must include an intentionally incorrect arrival-order reconstruction, then correct placement.

**Open decision:** Stop-and-Wait or Go-Back-N once the experiment workload has been clarified.

## 4. Development and impairment environment

**Proposed topology:** macOS development host → Docker Desktop Linux VM → isolated client/server Linux containers on a controlled network, using Linux `tc netem` for impairments.

**Rationale:** macOS stays the editor/Git environment while both endpoints and their measurements run within comparable Linux conditions. The PDF recommends `tc netem`, especially between containers or VMs.

**Needs verification before implementation:**
- Docker engine and Linux VM version.
- Container network interface names / direction of egress impairment.
- Availability of Linux `tc`/`netem` and privileges/capabilities such as `NET_ADMIN`.
- Network conditions and actual packet ordering, not merely configured loss percentages.
- Effects of virtualization and timer/scheduler behavior on measurements.
- Reversibility and isolation of network impairment settings.

**Fallback:** PDF-permitted in-application impairment emulator with fixed seeds. This can model receiver-level drops, delays, duplicates, corruption and swaps, but **is not the same as a real kernel socket-buffer overflow or IP-fragmentation behavior**.

**Physical Wi-Fi:** Supplemental only (PDF p.6); not a substitute for controlled impairments.

## 5. Measurement approach (proposed definitions)

| Metric | Definition to test and approve | Caveat |
| --- | --- | --- |
| Elapsed time | Monotonic time from session start to receipt of final integrity verdict | Document boundaries at both ends |
| Verified goodput | Original file bytes / elapsed seconds / 1,000,000, only for successful completion | Do not treat failed attempts as successful |
| Transmitted DATA packets | Original DATA transmissions | Count retransmitted DATA separately |
| Retransmissions | Extra DATA send attempts after original | ACK loss may also trigger resend |
| Duplicates | Valid repeated chunk sequence IDs | Define per-run counting clearly |
| Out-of-order | Valid chunk seen before a lower expected sequence | Define displacement measure |
| Loss | Expected unique DATA chunks missing at receiver in naive mode | Not the same as emulator loss probability |
| File verdict | SHA-256 original versus SHA-256 received | Every experiment row has a verdict |

- Store raw, per-run results (future CSV), including errors and non-matching hashes.
- Use decimal MB/s unless the report explicitly labels MiB/s.
- Use at least five repeats/configuration; report mean, **sample standard deviation**, and labeled error bars.
- Document emulator seeds; controlled reproducibility is not the same as repeating identical random events.
- Keep file, topology, impairment, reliability mode and buffer settings stable when varying a single parameter.
- Do not assume 10 µs / 100 µs requested delays are accurately scheduled. Document requested versus observed pacing limitations.
- Compare TCP and UDP on a common, clearly explained timing and integrity basis; naive UDP success rate must be visible.

## 6. Report and presentation tooling: *options*, not decisions

The assignment **prefers LaTeX with an IEEE or ACM template**, but accepts Word (p.6). Options to research further:

| Option | Advantages | Costs / risks |
| --- | --- | --- |
| LaTeX / Overleaf | Academic citations, controlled formatting and equations | Learning curve, template/page-limit checking |
| Markdown → Pandoc → PDF | Reusable text and Git-friendly diffs | PDF formatting/citations need verification |
| Typst | Modern approachable typesetting | External tooling unfamiliarity; verify 11-pt and export |
| Word | Explicitly accepted and accessible for hands-on authoring | Manual figure/reference consistency needs care |

**Provisional recommendation:** Let the student choose the tool most comfortable for **writing the report themselves**. Favor LaTeX if already familiar and meeting the deadline; otherwise Word is compliant. The only final requirement is a properly formatted PDF.

Slides must be PDF. Choice of slide-authoring tool is open; every student who is assessed must understand the material and rehearse the 12-minute talk.

## 7. Provisional Architecture Decision Record (ADR)

| ID | Decision | Status | Rationale / next evidence |
| --- | --- | --- | --- |
| ADR-001 | Python 3 | **Proposed** | Assignment recommendation + student skill; pin only after approval |
| ADR-002 | macOS development, Linux VM/containers for experiments | **Proposed** | Consistent environment; verify capabilities |
| ADR-003 | Linux `tc netem`, application emulator fallback | **Proposed** | Network-level impairments preferred |
| ADR-004 | Stop-and-Wait ARQ | **Conditional** | Simplicity vs feasibility on small chunks/large files |
| ADR-005 | Naive UDP separate from reliable-UDP/reordering demos | **Proposed** | Cleaner B1/B2/B4 evidence |
| ADR-006 | Raw CSV + Python statistics + Matplotlib | **Proposed** | Reproducible analysis and error bars |
| ADR-007 | Minimal modular architecture | **Open** | Compare variants during Stage 4 |
| ADR-008 | Report authoring workflow | **Open** | Student's personal comfort; professor permits Word |

**Decision gate:** No implementation until these decisions are reviewed, relevant ambiguities clarified, and the student explicitly authorizes the next phase.

## 8. Authoritative references

- Assignment: *TCP vs UDP File Transfer*, 12 Nov 2026, pp.2–10.
- [Python `socket`](https://docs.python.org/3/library/socket.html), [`struct`](https://docs.python.org/3/library/struct.html), [`hashlib`](https://docs.python.org/3/library/hashlib.html), [`time`](https://docs.python.org/3/library/time.html).
- [Go `net`](https://pkg.go.dev/net), [Java networking](https://docs.oracle.com/en/java/), [Rust `std::net`](https://doc.rust-lang.org/std/net/).
- [Linux `tc-netem(8)`](https://man7.org/linux/man-pages/man8/tc-netem.8.html).
- [RFC 768 — UDP](https://www.rfc-editor.org/rfc/rfc768), [RFC 9293 — TCP](https://www.rfc-editor.org/rfc/rfc9293).
- [Kurose & Ross lecture slides](https://gaia.cs.umass.edu/kurose_ross/ppt.php).

Related documents: [MISSION.md](MISSION.md), [CONSTITUTION.md](CONSTITUTION.md), [ROADMAP.md](ROADMAP.md).
