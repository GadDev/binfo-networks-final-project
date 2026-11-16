# Project Constitution — Networks Final Project

**Status:** Proposed for review · **Phase:** Research & Discovery (no implementation authorized)  
**Authority:** The professor's 10-page *Assignment: TCP vs UDP File Transfer* (12 November 2026) supersedes this document.

## 1. Purpose

This constitution sets the non-negotiable principles for a university assignment that includes file-transfer software, controlled networking experiments, a **student-authored written report**, and a class presentation.

Our goal is not to recreate TCP entirely, collect impressive-looking graphs, or maximize lines of code. It is to learn the underlying networking concepts, meet the assignment's specific requirements, measure honestly, and explain every important decision.

## 2. Academic integrity and ownership

1. **The student writes the written report.** AI may act as a tutor, reviewer, technical explainer, outline critic, methodological checker, and language editor. It must not silently substitute invented results or independently authored analysis for the student's own understanding. The student owns the wording, conclusions, and citation decisions.
2. The assignment explicitly permits AI assistants and online references, **provided they are cited in the report**. Maintain an accurate record of substantive assistance and references, following any additional instructor guidance (PDF p.10).
3. **Every measurement must come from our own actual experimental runs.** Never create fictitious tables, timing results, packet counts, error bars, or plots that purport to be results (PDF p.10).
4. Code and protocol logic must be fully explainable by every student who is assessed. Code not understood earns no credit (PDF p.10).
5. Discussing ideas with other teams is permitted; **sharing code with other teams is not** (PDF p.10).
6. If the team is two students, both must participate and understand all of the work. A three-person team requires prior approval and two reliability schemes (PDF pp.2, 10).

## 3. Phase gate: research before implementation

Until the student explicitly authorizes a later implementation phase:

- Do **not** create application source files, tests, experiment runners, Docker configuration or automation scripts.
- Do **not** install dependencies, start containers, modify network settings or execute measurements.
- Documentation-only changes may be proposed and reviewed via pull requests when explicitly requested.
- Architecture, programming language, error-control scheme and experiment environment are **proposed**, not accepted solely because they appear in a document.
- Short diagrams and pedagogical pseudo-code are permitted as explanations, not executable deliverables.

**Gate to implementation:** explicit user approval of scope, language, reliability design, environment, methodology and a realistic time budget.

## 4. Engineering principles for later phases

### 4.1 Correctness before speed
- Validate complete file delivery and a final SHA-256 MATCH verdict; a fast corrupted transfer is not successful.
- TCP is a byte stream: never assume one send equals one receive (PDF p.2).
- UDP is a datagram service: sequence numbers and correct file placement matter.
- Handle missing files, peer timeouts, unreachable servers and incomplete sessions cleanly.

### 4.2 Minimal, explainable architecture
- Use plain sockets. Avoid HTTP/FTP/QUIC/ZeroMQ and similar transport-hiding abstractions; do not use `sendfile` (PDF pp.2, 10).
- Do not introduce UI frameworks, distributed services or advanced concurrency merely for appearance.
- Keep mandatory functionality separate from bonus experiments.
- Prefer simple interfaces, explicit packet layouts, configurable parameters and understandable error messages.

### 4.3 Reproducible measurements
- Change one experimental variable at a time and record all relevant controls.
- Use at least five repetitions per configuration, with a clear definition of mean, sample standard deviation, and error bars (PDF p.6).
- Record OS, CPU, language/runtime versions, network topology, tools, parameters, and impairment seeds/settings.
- Preserve raw results, failures and hash mismatches rather than filtering inconvenient runs.
- Clearly distinguish configured packet-loss probability, actual observed loss, retransmission counts, and verified goodput.
- Interpret TCP/UDP comparisons with their different implementation overhead and guarantees in mind.

### 4.4 Data integrity and security
- Use a packet-level CRC32 to detect accidental corruption, and end-to-end SHA-256 for file comparison.
- Calculate MD5 and SHA-1 as the assignment requires, and explain their cryptographic weaknesses (PDF p.5).
- Hash and process files incrementally rather than loading files larger than memory.
- Distinguish hash-based integrity comparison from authenticated secure communication. The assignment does not ask for an encryption layer.

### 4.5 Evidence and uncertainty
All technical documents and reports distinguish:
- **[PDF fact]** directly stated in the assignment, with page/section;
- **[Source]** supported by cited official technical material;
- **[Recommendation]** engineering judgment and trade-off;
- **[Assumption]** unverified environment or workload input;
- **[Open question]** needs the professor or student to decide;
- **[Measured result]** actual observations, with configuration, repetitions and source CSV.

Never turn an assumption into a verified fact merely by repeating it.

## 5. Report authorship protocol

The student is the **primary and final author** of the 8–12-page, 11-pt PDF report (appendices excluded). The assistant may:

- Help interpret the required section structure.
- Ask guiding questions and request evidence for claims.
- Explain formulas, protocols and statistical reasoning.
- Check the draft for factual errors, weak reasoning, incomplete citations, ambiguous units, and missing figures.
- Suggest edits **with explanations** so the student can accept or reject them.

The assistant must **not** fabricate findings or present hypothetical diagrams/data as observed results. Draft sections may be outlined before results exist, but final numerical claims, conclusions and the abstract's findings are written only after real measurements.

For each report section, use the cycle: **student draft → technical review → student revision → rubric check**.

## 6. Quality gates and later Definition of Done

### Research phase is complete when
- PDF requirements and rubric are understood.
- Technology decisions and their alternatives are recorded.
- Assumptions and professor questions have been resolved or made explicit.
- Reading prerequisites and the plan are reviewed.
- Student explicitly authorizes transition to implementation.

### Implementation phase will be complete only when
- Mandatory TCP and UDP functionality passes correctness and negative tests.
- All B1–B4 experiments and integrity checks have credible evidence.
- Required file classes and at least five repetitions per configuration are documented.
- CSV outputs and four mandatory plot categories are reproducible.
- The student can explain the code and all networking mechanisms.

### Submission is ready only when
- The student-authored report follows the required structure, page count, font size, figures and references.
- Slides are submitted as a PDF, and the talk is rehearsed to 12 minutes plus 3 minutes of Q&A.
- Source repository or ZIP includes required history and access.
- Academic-assistance citations are accurate.
- Submission is completed by **2 December 2026, 23:59 Luxembourg time**; presentation is **3 December 2026**.

## 7. Scope discipline and change control

- Prioritize the mandatory rubric, especially UDP reliability and the scientific results.
- Bonus items (second ARQ algorithm, adaptive timeout, sliding window comparison, multi-client server, extra physical-network tests) require separate justification and approval.
- Do not conceal schedule risk. If workload exceeds available hours, propose a simpler compliant approach and raise real ambiguities with the professor, rather than silently omitting mandatory requirements.
- Significant technology decisions are recorded in [TECHSTACK.md](TECHSTACK.md); the future work schedule is in [ROADMAP.md](ROADMAP.md); project goals are in [MISSION.md](MISSION.md).

## References

- Course assignment: *TCP vs UDP File Transfer* (12 Nov 2026), especially pp.2–10.
- [RFC 768 — User Datagram Protocol](https://www.rfc-editor.org/rfc/rfc768).
- [RFC 9293 — Transmission Control Protocol](https://www.rfc-editor.org/rfc/rfc9293).

**Review status:** Proposed. No application implementation has been authorized.
