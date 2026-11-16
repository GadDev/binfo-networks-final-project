# PROGRESS.md

_Last updated: 2026-11-16 · Days to internal target (30 Nov): **14 calendar days** · Official submission: **2 Dec 2026, 23:59 Luxembourg time** · Presentation: **3 Dec 2026**_

> **Project phase: Research & guided learning.** No implementation has been authorized, and no experiments have been run. This file records demonstrated learning separately from research/documentation completed.

## Done

- [x] Stage 1 — Assignment audit: analyzed TCP and UDP requirements, experiment sets B1–B4, deliverables, marking rubric, and open professor questions.
- [x] Stage 2 — Created beginner-friendly networking reference lessons on fundamentals, UDP reliability, and measurement (documentation PR [#1](https://github.com/GadDev/binfo-networks-final-project/pull/1)).
- [x] Stage 3 — Compared Python, Go, Java and Rust; Stop-and-Wait, Go-Back-N and Selective Repeat; and impairment tools. Refined recommendations for **macOS + Python experience + available Docker/Linux VM**.
- [x] Drafted research governance and planning documents: constitution, mission, technical alternatives and learning roadmap (documentation PR [#2](https://github.com/GadDev/binfo-networks-final-project/pull/2)). PR publication/merge status should be checked independently.
- [x] Started guided Kurose & Ross Chapter 1 §1.1 lesson: “The Internet is a network of networks.”

## Complete task checklist

The full actionable, requirement-traceable plan is in **[docs/TASKS.md](docs/TASKS.md)**. It contains **136 tasks with stable IDs, priority labels, dependencies, PDF references and objective “done when” criteria**.

| Workstream | Tasks done / total | Current status |
| --- | ---: | --- |
| Research, decisions and learning (R-01–R-12) | 4 / 12 | Chapter 1 reading and comprehension in progress |
| Architecture and project foundation (D-01–D-08) | 0 / 8 | Research/design only; not authorized to implement |
| TCP baseline (T-01–T-10) | 0 / 10 | Not started |
| UDP protocol and naive baseline (U-01–U-10) | 0 / 10 | Not started |
| B1 chunk-size experiments | 0 / 6 | Not started |
| B2 delay experiments | 0 / 7 | Not started |
| B3 UDP reliability | 0 / 13 | Not started |
| B4 packet reordering | 0 / 8 | Not started |
| Integrity, environment and reproducibility | 0 / 18 | Not started |
| **Student-authored report** | 0 / 17 | Notes and structure planned; no report sections completed |
| Presentation | 0 / 10 | Not started |
| Final verification and submission | 0 / 11 | Not started |
| Bonus/optional work | 0 / 6 | Excluded from critical path |
| **Total** | **4 / 136** | **Research and guided learning only** |

**Next actionable tasks:** **R-05** read and demonstrate Chapter 1 §§1.1–1.5; **R-10** confirm team and available hours; **R-11** send unresolved experiment questions to professor. **R-09** Stage 4 architecture research follows learning progress. No coding until **R-12** explicit implementation approval.

**Important:** The 4 completed tasks represent research/documentation. They are **not four completed graded implementation tasks**, and no concept has yet been demonstrated as mastered.

## In progress

- [ ] **Chapter 1 §1.1:** Explain the difference between a local network and the public Internet. The first check question was asked; **student answer is pending**.
- [ ] Read the textbook: **Chapter 1 §§1.1–1.3 first**, then §§1.4–1.5; Chapter 2 on application architectures and sockets; Chapter 3 on transport/reliability.
- [ ] Stage 4 — Research-only architecture planning: compare minimal and modular approaches; no implementation.
- [ ] Define a **student-authored** written-report workflow: the student writes draft sections; the tutor provides accuracy, evidence, citation, and rubric feedback. No report text or results are yet completed.

## Blocked / questions

- **Team:** working alone or with teammate(s) remains unconfirmed.
- **Experiment matrix:** ask whether all three file sizes must be crossed with every chunk/delay/loss configuration.
- **ARQ choice:** Stop-and-Wait is *conditional* because of runtime limits on very large files and tiny chunks; Go-Back-N is a fallback. No selection approved.
- **Environment/stack decisions:** Python 3 and macOS + Docker/Linux + `tc netem` are recommendations, not implemented or verified.
- **Report/plots ambiguities:** TCP buffer-size versus UDP chunk-size comparison, B2 zero-delay buffer overflow if not observed, and error bars on B4 arrival-order traces.
- **Time budget:** available hours per day not yet established.

## Concepts mastered

- **None formally verified yet.** Research coverage and listening to an explanation do not count as demonstrated mastery.
- A concept is promoted here only after the student explains it correctly or solves an appropriate check question without relying on the answer key.

## Concepts to revisit

- **Local area network vs public Internet:** awaiting the first answer.
- Next: hosts/end systems, client/server, packet switching, protocols, TCP byte streams, UDP datagrams, ACK/retransmission, throughput and error bars.
- This list is a **study queue**, not a list of demonstrated weaknesses.

## Next session goal

1. **Answer the pending question:** Two laptops share the same home Wi-Fi but their ISP is down. Can they still transfer a file to each other, and why?
2. **Done when:** the student correctly distinguishes local-network communication from Internet access, in their own words.
3. **Only after that:** continue Chapter 1 §1.1 with hosts/end systems and one new comprehension question. No code.

## Session log

| Date | Session | Evidence | Next action |
| --- | --- | --- | --- |
| 2026-11-16 | Stages 1–3 research, planning documentation, guided Chapter 1 §1.1 started | Research and reference materials exist; first student comprehension response not yet received | Answer local Wi-Fi / ISP-outage question |

## End-of-session update protocol

At the end of **each interactive tutoring session**, review the student's actual answers and work, then update this file with:

- **Done / In progress / Blocked:** only completed actions and current dependencies.
- **Concepts mastered:** concepts supported by a demonstrated correct explanation or solved problem.
- **Concepts to revisit:** misunderstandings with a brief reason.
- **Days left / next session goal:** use the current Luxembourg date and one concrete mastery check.
- **Session log:** concise date, covered material, evidence, next step.
- **Task inventory:** update [docs/TASKS.md](docs/TASKS.md) checkboxes only when the task's own acceptance criterion is met; reference task IDs and evidence in this tracker.

**Do not automatically mark a concept complete just because it was explained or read. Do not fabricate quiz results or experiment outputs.** GitHub updates can be made **during our active sessions** when repository access is available; this tracker is not an unattended background automation.

## Sources and navigation

- [Project README](README.md)
- [Course index](course/README.md)
- [Learning roadmap](docs/ROADMAP.md)
- [Complete task inventory](docs/TASKS.md)
- Assignment: *TCP vs UDP File Transfer*, 12 November 2026, especially pp. 2–10.
- Kurose & Ross, *Computer Networking: A Top-Down Approach*, ninth edition, §§1.1–1.5, 2.1, 2.6, 3.3–3.5.
