# Lesson 03 — Measurement: How Do We Know Which Design Performs Better?

> **Phase: Research & Discovery.** All numerical examples below are fictional teaching examples, **not experimental results**.  
> **Goal:** Learn to measure file-transfer performance, characterize packet loss and reordering, and interpret results without misleading comparisons.

[Previous: reliability](02-reliability.md) · [Course index](README.md) · [Back to project overview](../README.md)

## Why the measurements matter

Suppose a UDP program transfers a file successfully in 2 seconds and a TCP program does so in 3 seconds. Can we already conclude "UDP is faster"? **No.**

We would first ask whether both transferred the same file, used comparable conditions, performed the same integrity checks, and were tested repeatedly. A one-off run says little about variability, reliability, or the cost of retransmissions.

The assignment requires controlled experiments, **at least five repetitions** per configuration, means, standard deviations, **error bars on every plot**, and SHA-256 checks for every result row (pp.5–6).

## 1. Bandwidth

**Bandwidth** is a link's capacity, often expressed in bits per second (e.g. Mbit/s). It represents an upper constraint, not a promise that the application can achieve the same rate.

- **Analogy:** The motorway's theoretical capacity for vehicles per hour.
- **Assignment connection:** Helps explain observed speed under B1 chunk-size and B2 pacing experiments.
- **Common mistake:** Calling the available link bandwidth a measured application throughput.

## 2. Latency

**Latency** is the time it takes for data to travel from one endpoint toward another, potentially including propagation, serialization, processing, and queueing. Different network conditions create different delays.

- **Analogy:** Time for a package to reach its destination.
- **Assignment connection:** Pacing and added network delay influence completion time and the efficiency of waiting for ACKs.
- **Common mistake:** Assuming high bandwidth implies low latency.

## 3. Round-trip time (RTT)

**RTT** is the elapsed time for a message to reach the destination and for a response to return. It is not always exactly twice the one-way delay because the forward and return paths and processing times may differ.

- **Analogy:** Asking a question and measuring how long until you hear the answer.
- **Assignment connection:** B3 requires explaining the chosen retransmission timeout. Adaptive RTT-based timeouts are a bonus.
- **Common mistake:** Assuming RTT is constant across the experiment.

## 4. Throughput versus goodput

**Throughput** is a data transfer rate measured in bits/s or bytes/s. **Goodput** is the rate of successfully delivered, *unique application content*. Definitions must specify exactly which bytes are counted, from which endpoint, and over which time interval.

A useful teaching example:

- Original file size: **10 MB**.
- Time to complete and verify it: **5 seconds**.
- Successful goodput: **10 / 5 = 2 MB/s**.
- Total application payload transmitted, including retransmissions: **12 MB**.
- Sender's application-payload send rate: **12 / 5 = 2.4 MB/s**.

More bytes sent can indicate **more overhead**, not more useful delivery.

> **Convention to decide later:** In standard decimal notation, 1 MB = 1,000,000 bytes. A binary MiB is 1,048,576 bytes. The assignment requests MB/s; do not mix MB/s with MiB/s without labeling them. For a failed or incomplete transfer, do not label its raw send rate as successful goodput.

- **Analogy:** A delivery company ships 100 boxes, but only 80 represent new orders delivered correctly.
- **Assignment connection:** TCP/UDP throughput and B3 effective throughput are central report measures.
- **Common mistake:** Counting every retransmission as newly delivered file data.

## 5. Packet overhead and chunk size

Every packet requires extra protocol information and processing. A small file chunk means many more packets for the same file size and a higher share of bytes spent on headers.

Using only the **11-byte custom application header**, for illustration:

| File chunk | Application header | Header share of application datagram |
| ---: | ---: | ---: |
| 64 bytes | 11 bytes | 11 / 75 = 14.67% |
| 256 bytes | 11 bytes | 11 / 267 = 4.12% |
| 1400 bytes | 11 bytes | 11 / 1411 = 0.78% |

These shares exclude UDP, IP, and link-layer overhead. Larger chunks amortize header cost but can increase fragmentation and loss consequences on typical networks.

- **Analogy:** Shipping 1,000 tiny envelopes versus ten larger packages.
- **Assignment connection:** B1 requires an explanation of both small-chunk overhead and large-datagram fragmentation.
- **Common mistake:** Assuming the largest supported UDP datagram will always achieve the highest effective throughput.

### A warning about large UDP datagrams

Under a 1500-byte IPv4 MTU with no IP options, a UDP payload of about 1472 bytes fits without fragmentation. A custom 11-byte transfer header reduces the simplified **file-data** threshold to about 1461 bytes.

The assignment's largest required chunk is **65,000 file bytes**, plus the application header, which may be sent by UDP within its maximum payload limit but greatly exceeds a common Ethernet MTU. It therefore faces fragmentation or delivery limitations depending on actual network conditions.

This is a theoretical sizing observation, not a measured result.

## 6. Socket buffers

A socket's receive buffer holds incoming data waiting to be processed. If the receiver cannot keep up, **UDP datagrams may be dropped** when the queue fills.

```text
UDP sender:   packet packet packet packet packet packet ...
                           |
                           v
Receiver:        +---------------------+
                 | socket receive queue|
                 | packet | packet     | <- finite capacity
                 +---------------------+
                        |
                   application
                   reads packets
```

- **Analogy:** A reception desk with limited space for incoming parcels.
- **Assignment connection:** B2 investigates packet loss when inter-packet delay is zero and requires reporting the `SO_RCVBUF` size.
- **Common mistake:** Assuming a larger receive buffer completely eliminates overload; it may only delay it, and operating systems can report buffer values differently than requested settings.

## 7. Controlled network impairment

On loopback, packet loss or reordering may be effectively absent. For scientifically useful experiments, the assignment permits intentionally introducing impairments.

### Option A — Linux `tc netem`

Linux network emulation can introduce controlled loss, delay/jitter, duplication, reordering, and corruption. This is the assignment's recommended option when used on an interface between containers or VMs.

### Option B — In-application emulator

A software impairment layer can deliberately discard, delay, swap, duplicate, or corrupt datagrams. The assignment explicitly permits this alternative and recommends a fixed random seed for reproducibility.

### Option C — Physical machines on Wi-Fi

Physical machines can expose real network behavior, but Wi-Fi alone does not guarantee repeatable or sufficient loss. The PDF describes physical-machine tests **in addition to** one of the above controlled approaches.

**No impairment approach has been selected yet.** Stage 3 will compare them based on the actual development OS, privileges, reliability, reproducibility, and learning cost.

- **Analogy:** A flight simulator recreates adverse weather so experiments are comparable.
- **Assignment connection:** B3/B4 require deliberate network loss and reordering.
- **Common mistake:** Reporting configured impairment percentages as if they were the exact packet loss measured by the receiver. They are related but not identical.

**Official technical reference:** [tc-netem(8)](https://man7.org/linux/man-pages/man8/tc-netem.8.html).

## 8. Repeated trials, mean and sample standard deviation

Networks and operating systems vary across time. Repeating a configuration reduces the temptation to overinterpret one unusually fast or slow run.

Suppose five fictional throughput measurements are:

```text
Run:        1    2    3    4    5
MB/s:       8   10    9   11   12
```

The **mean** is the average:

\[
\bar{x} = \frac{\sum_{i=1}^{n}x_i}{n}
        = \frac{8+10+9+11+12}{5} = 10\ \mathrm{MB/s}
\]

The **sample standard deviation** measures spread:

\[
s=\sqrt{\frac{\sum_{i=1}^{n}(x_i-\bar{x})^2}{n-1}}
=\sqrt{\frac{10}{4}}\approx 1.58\ \mathrm{MB/s}
\]

The results may be reported as **10 ± 1.58 MB/s (mean ± sample standard deviation)**, provided the ± quantity is clearly labeled. This notation does not create a 95% confidence interval.

- **Analogy:** Two players may average ten goals, but one can be more consistent.
- **Assignment connection:** At least five repeats of each configuration, with mean and standard deviation (p.6).
- **Common mistake:** Using only the best run, or confusing sample SD with the standard error of the mean.

## 9. Error bars

**Error bars** indicate variability or uncertainty around measured quantities. Common choices include standard deviation, standard error, and confidence intervals. They answer different questions, and the graph caption must state which is used.

```text
Throughput (illustrative, MB/s)

12 |                  |
11 |                --o--   mean at 10
10 |                  |
 9 |                  |
 8 |
   +-----------------------------
      one experimental setting

The bar represents dispersion around the mean
(not actual results; not to scale).
```

- **Analogy:** A weather forecast gives an expected temperature plus uncertainty.
- **Assignment connection:** The rubric explicitly rewards correct plots and error bars; all plots must label axes and units and have captions and text references.
- **Common mistake:** Including bars without a definition, or claiming the spread proves one protocol is better than another.

**Potential ambiguity:** A single arrival-order trace for B4 is not naturally an aggregate mean ± SD plot; the professor's statement that every plot needs error bars may warrant clarification for this particular diagnostic figure.

## 10. Integrity verification: did the *same bytes* arrive?

**File integrity** is not guaranteed by a matching byte count. One missing chunk replaced by a duplicate can leave the reconstructed file the expected size but alter its contents.

The assignment's required final verdict is based on comparing **SHA-256 hashes** of the original and received files. A matching digest is powerful practical evidence of identical contents; hashes do not provide mathematical proof of equality, nor do they authenticate an untrusted sender without additional protections.

Conceptual workflow:

```text
Original file -------> SHA-256 --------> original digest
      |                                  |
      |       transfer over network      | compare
      v                                  |
Received file -------> SHA-256 --------> received digest
                                             |
                                         MATCH / MISMATCH
```

Hashes should be computed **incrementally** in blocks so the full file need not fit into RAM. The assignment specifies, for example, 64 KB at a time.

Independent OS verification methods mentioned by the assignment:

- Linux: `sha256sum`
- macOS: `shasum -a 256`
- Windows: `certutil -hashfile` with the SHA256 algorithm

These are references for future testing, **not commands run during this research phase**.

- **Analogy:** Compare fingerprints of the original and received documents.
- **Assignment connection:** A hash match or mismatch is the pass/fail verdict for every experiment row.
- **Common mistake:** Calling a matching file size "integrity verified" or assuming SHA-256 on its own authenticates data.

## 11. CRC32, MD5, SHA-1, and SHA-256 have different roles

| Mechanism | Size | Role in assignment | Security note |
| --- | ---: | --- | --- |
| CRC32 | 32 bits | Detect accidental corruption of a UDP DATA payload | Not a cryptographic authenticity mechanism |
| MD5 | 128 bits | Also compute file hash for comparison/discussion | Known collision attacks; not appropriate for cryptographic collision resistance |
| SHA-1 | 160 bits | Also compute file hash for comparison/discussion | Known collision attacks; not appropriate for cryptographic collision resistance |
| SHA-256 | 256 bits | Main end-to-end file-integrity check | Strong modern collision resistance; not authentication alone |

**Key distinction:** The **per-packet CRC32** checks one datagram payload. The **end-to-end SHA-256** checks the reconstructed file. These checks complement each other.

- **Analogy:** Inspect each parcel for local damage, then check whether the entire reconstructed shipment matches the manifest.
- **Assignment connection:** CRC32 is mandatory in the UDP header, while SHA-256, MD5 and SHA-1 must be considered at the file level (assignment p.5).
- **Common mistake:** Treating CRC32 as a secure hash or treating MD5 and SHA-1 as suitable modern security guarantees.

## 12. Reproducible experimental science

The assignment asks for changes to **one parameter at a time**. That means a comparison of chunk sizes should keep the file, network condition, reliability mode, machine and other relevant settings fixed.

### The required test files

| Test file | Approximate size | Purpose |
| --- | --- | --- |
| Text | 100 KB | Easy sanity check and human-readable comparison |
| Binary image or PDF | 10 MB | Typical file transfer |
| Random binary | 100 MB | Stress test / buffer and loss effects |

All large files must eventually be generated or supplied without committing the 100 MB fixture to Git.

### Record the experimental environment

The PDF requires: operating system, language version, CPU, how endpoints are connected, and the exact emulator settings or commands.

### What to record for each run

| Metric | What it tells you | Important caveat |
| --- | --- | --- |
| Transfer time (s) | Elapsed time | Use consistent start/stop boundaries |
| Throughput (MB/s) | Transfer rate | Define byte basis and whether failed transfers count |
| Datagrams sent | UDP traffic generated | Distinguish original packets from retransmissions |
| Retransmissions | Recovery effort | A timeout may be due to a missing ACK |
| Duplicates received | Duplicate traffic | Define whether counting copies or repeated sequence IDs |
| Out-of-order arrivals | Ordering disorder | State exact counting definition |
| SHA-256 verdict | File integrity | Record MATCH or MISMATCH, never infer from file size |

For elapsed time, a **monotonic timer** is conceptually preferable to wall-clock time, because it is not subject to clock adjustments. We will research exact tooling only after choosing a language.

### Four mandatory plot categories

1. **TCP and UDP throughput vs chunk/buffer size** on the same axes.
2. **Loss rate vs inter-packet delay**, with UDP error management off.
3. **Effective throughput and retransmissions vs network loss rate**, with error management on (may need separate panels/plots for readable units).
4. **Received sequence number vs arrival index**, with packet reordering enabled.

Every figure needs axes with units, a caption, a reference in the report, and error bars as required by the PDF.

### Experimental pitfalls

- **Configured loss is not the same as observed loss.** Report the actual measurement definition.
- **One fast run is not a performance conclusion.** Use repeated trials.
- **Failed transfers must not masquerade as successful high-goodput runs.** Report hash verdict and failures.
- **Different test conditions invalidate simple comparisons.** Keep relevant controls constant.
- **Microsecond delays are tricky.** Requesting 10 µs or 100 µs between packets does not guarantee exact real-world pacing from an ordinary user-space sleep call; record this as a limitation.
- **Large datagrams can fragment.** This changes their failure probability and may make results look counterintuitive.
- **ACK traffic and retransmissions matter.** Explicitly document which bytes and packets each counter includes.

---

## Worked example: a careful comparison

**Research question:** Does increasing UDP chunk size improve useful delivery rate?

**Fair conceptual plan (not run here):**

1. Hold file contents, receiver, network conditions, error-management mode, and pacing constant.
2. Vary only chunk size through the nine values prescribed in B1.
3. Measure time, packet count, loss and final hash verdict for each run.
4. Repeat each configuration at least five times.
5. Compute mean and sample SD; plot with clear error bars.
6. Interpret both small-chunk overhead and possible large-datagram fragmentation.

This is a *planning example*, not an experiment that has been executed.

## Knowledge checks

1. How can transmitted throughput rise while useful goodput stays the same?
2. Why might a very small UDP chunk hurt performance?
3. If two configurations have the same mean, what can sample standard deviation reveal?
4. What must a graph caption say about error bars?
5. What is the difference between configured emulator loss and measured receiver loss?
6. Can a received file have the correct size and an incorrect SHA-256 hash?
7. What is the difference between CRC32 and SHA-256 in this assignment?
8. Why do repeated trials help us interpret performance differences?

<details>
<summary>Answers</summary>

1. Retransmissions add sent bytes without delivering new unique file data.
2. More packet headers, system calls, and processing operations per file byte.
3. One configuration may be more variable/less stable.
4. Whether bars represent SD, SE, confidence intervals, etc., with the underlying sample size.
5. One is an emulator probability/configuration; the other is observed reception behavior.
6. Yes, wrong content can have the same length.
7. CRC32 checks payloads for accidental corruption; SHA-256 checks the complete reconstructed file.
8. Runs vary; repeats quantify variability instead of relying on one anecdotal result.

</details>

## References

- Assignment: *TCP vs UDP File Transfer*, pp.3–7 and 9–10.
- Kurose & Ross, *Computer Networking: A Top-Down Approach*: ninth-edition ``1.4, 3.4–3.6, 4.3, 6.2 and 8.3, where relevant.
- [tc-netem(8) — Linux manual](https://man7.org/linux/man-pages/man8/tc-netem.8.html).
- [RFC 9293 — TCP](https://www.rfc-editor.org/rfc/rfc9293).
- [RFC 768 — UDP](https://www.rfc-editor.org/rfc/rfc768).

[Previous: reliability](02-reliability.md) · [Course index](README.md) · [Back to project overview](../README.md)
