# PROGRESS — Networks Final Project

**Updated:** 16 November 2026 (Europe/Luxembourg).  
**Phase:** Research and Stage 4 design review. No implementation authorized.

## Done — confirmed documentation work

- Reviewed the nine files on main at commit ad50d6dfec257007efa75b2c5091686ce8580c77.
- Read the supplied page-referenced assignment inventory, including its AI-use policy; imported a repository snapshot with provenance.
- Added a README assignment summary, prescribed experiment settings, deliverables and tool choices.
- Drafted the Stage 4 architecture comparison and packet lifecycle: START/DATA/ACK/END loss, duplicate handling, bounded retries, cached final verdict and an unconfirmed sender outcome.
- Added proposed session isolation, counter definitions and paper desk-check scenarios.

The original assignment PDF was not re-read during this update. The snapshot records the supplied requirements; no claim of fresh PDF verification is made.

## In progress

- Student review of the README and Stage 4 proposal.
- Learning fundamentals and reliability; actual reading/practice completed outside GitHub is unknown.

## Blocked / unresolved before implementation

- Available hours, team arrangement and exact experiment/file-size matrix.
- Final language, ARQ, architecture, session-isolation method and impairment choices.
- Exact packet encoding, timer/retry values and environment validation.
- Professor questions recorded in the roadmap and architecture proposal.

## Concepts mastered

Not assessed in this documentation session. Document creation does not demonstrate mastery.

## Concepts to revisit

- Why an ACK being lost differs from DATA being lost.
- Why END alone does not prove completeness.
- Receiver hash MATCH versus sender confirmation.
- UDP read truncation versus socket queue overflow.
- Reordering metrics and honest summaries of failed runs.

## Time remaining

As of 16 November 2026:
- 14 calendar days to the internal target, 30 November.
- 16 calendar days to official submission, 2 December at 23:59 Luxembourg time.
- 17 calendar days to the presentation, 3 December.

Official dates: R-DEL-01 in [the assignment inventory](references/assignment-requirements.md). Internal target: planning choice.

## Next session's goal

Review [Stage 4](docs/ARCHITECTURE.md) and answer one scenario: the receiver has a matching file, but its final verdict is lost. Explain the sender's retry and eventual outcome.

No application code, tests, containers, experiments, measurements, report PDF or slides have been produced.
