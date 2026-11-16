# PROGRESS — Networks Final Project

**Updated:** 16 November 2026 (Europe/Luxembourg).  
**Phase:** Research and Stage 4 design review. No implementation authorized.

> **Project phase: Research & guided learning.** No implementation has been authorized, and no experiments have been run. This file records demonstrated learning separately from research and documentation.

## Done — confirmed research and documentation

- Stage 1 — Audited the assignment and grading rubric, including requirements, experiment sets, deliverables and open questions.
- Stage 2 — Created beginner-friendly networking references on fundamentals, UDP reliability and measurement (documentation PR [#1](https://github.com/GadDev/binfo-networks-final-project/pull/1)).
- Stage 3 — Compared programming languages, reliability schemes and impairment methods; documented project governance and planning (documentation PR [#2](https://github.com/GadDev/binfo-networks-final-project/pull/2)).
- Created the 136-task, requirement-traceable checklist in [docs/TASKS.md](docs/TASKS.md). Four research/documentation tasks are marked complete; implementation and experiment tasks remain unchecked.
- Reviewed the nine files on `main` at commit `ad50d6dfec257007efa75b2c5091686ce8580c77`.
- Added the page-referenced assignment inventory and a README assignment overview, experiment settings, deliverables and tool choices. The original assignment PDF was not reread during this update; the supplied snapshot was used, and no fresh PDF verification is claimed.
- Drafted the Stage 4 architecture comparison and packet lifecycle, covering START/DATA/ACK/END loss, duplicate handling, bounded retries, cached final verdicts and an unconfirmed sender outcome.
- Added proposed session isolation, counter definitions and packet lifecycle desk-check scenarios.
- Reworked the README into six plain-English parts with assignment references and added [136 design-check questions](docs/DESIGN_CHECK_QUESTIONS.md) across 16 topics, a twelve-question starting set and an explanation of lost final confirmation.

## In progress

- Student review of the README and Stage 4 proposal.
- Chapter 1 §1.1: the first local-network comprehension question has been asked; the student's answer is pending.
- Read the textbook: Chapter 1 §§1.1–1.5, then Chapter 2 on application architectures and sockets, then Chapter 3 on transport and reliability.
- Define a student-authored report workflow; no report text or results are complete.

## Blocked / unresolved before implementation

- Team arrangement and available hours.
- Exact experiment and file-size matrix; professor questions recorded in the roadmap and architecture proposal.
- Final language, ARQ scheme, architecture, session-isolation method and impairment choices.
- Exact packet encoding, timer/retry values and environment validation.

## Task checklist status

The checklist's 136 tasks track requirements and evidence; counts are not grading percentages. Research and documentation are distinguished from demonstrated mastery and from future implementation.

| Workstream | Done / total | Current status |
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
| Student-authored report | 0 / 17 | Notes and structure planned; no report sections completed |
| Presentation | 0 / 10 | Not started |
| Final verification and submission | 0 / 11 | Not started |
| Bonus/optional work | 0 / 6 | Excluded from critical path |
| **Total** | **4 / 136** | **Research and guided learning only** |

No concept is counted as mastered merely because it was explained or documented.

## Concepts mastered

None formally verified yet. A concept is promoted only after the student explains it correctly or solves an appropriate check question without relying on the answer key.

## Concepts to revisit

- Local area network versus public Internet; hosts/end systems and client/server.
- TCP byte streams, UDP datagrams, ACKs, retransmission, throughput and error bars.
- Why a lost ACK differs from lost DATA; why END alone does not prove completeness.
- Receiver hash MATCH versus sender confirmation.
- UDP read truncation versus socket queue overflow.
- Reordering metrics and honest summaries of failed runs.

## Time remaining

As of 16 November 2026:
- 14 calendar days to the internal target, 30 November.
- 16 calendar days to official submission, 2 December at 23:59 Luxembourg time.
- 17 calendar days to the presentation, 3 December.

Official dates: R-DEL-01 in [the assignment inventory](references/assignment-requirements.md). Internal target: planning choice.

## Next session goal

1. Answer the pending question: two laptops share home Wi-Fi but their ISP is down. Can they still transfer a file to each other, and why?
2. Done when the student distinguishes local-network communication from Internet access in their own words.
3. Then use [the design-check bank](docs/DESIGN_CHECK_QUESTIONS.md), starting with Q-DATA-04: does a sender timeout prove DATA was lost? Explain possible alternatives.
4. Continue Chapter 1 §1.1 with hosts/end systems and one new comprehension question. The final-confirmation scenario has been explained as a worked example; independent understanding has not yet been assessed.

## Session log

| Date | Session | Evidence | Next action |
| --- | --- | --- | --- |
| 2026-11-16 | Stages 1–3 research, planning documentation, guided Chapter 1 §1.1 started | Research and reference materials exist; first student comprehension response not yet received | Answer local Wi-Fi / ISP-outage question |

## End-of-session update protocol

At the end of each interactive tutoring session, review the student's actual answers and work, then update this file with:

- **Done / In progress / Blocked:** only completed actions and current dependencies.
- **Concepts mastered:** concepts supported by a demonstrated correct explanation or solved problem.
- **Concepts to revisit:** misunderstandings with a brief reason.
- **Days left / next session goal:** use the current Luxembourg date and one concrete mastery check.
- **Session log:** concise date, covered material, evidence and next step.
- **Task inventory:** update [docs/TASKS.md](docs/TASKS.md) checkboxes only when the task's own acceptance criterion is met; reference task IDs and evidence in this tracker.

Do not automatically mark a concept complete just because it was explained or read. Do not fabricate quiz results or experiment outputs. GitHub updates can be made during active sessions when repository access is available; this tracker is not an unattended background automation.

## Sources and navigation

- [Project README](README.md)
- [Course index](course/README.md)
- [Learning roadmap](docs/ROADMAP.md)
- [Complete task inventory](docs/TASKS.md)
- Assignment: *TCP vs UDP File Transfer*, 12 November 2026, especially pp. 2–10.
- Kurose & Ross, *Computer Networking: A Top-Down Approach*, ninth edition, §§1.1–1.5, 2.1, 2.6, 3.3–3.5.
