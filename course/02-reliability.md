# Lesson 02 — Reliability: Why UDP Needs Extra Mechanisms

> **Phase: Research & Discovery.** This is conceptual material, not an implementation plan.  
> **Goal:** Understand packet loss, acknowledgements, retransmission, ordering, and three common Automatic Repeat reQuest (ARQ) designs.

[Previous: fundamentals](01-fundamentals.md) · [Course index](README.md) · [Next: measurement](03-measurement.md)

## The central idea

TCP provides a reliable ordered byte stream. UDP sends individual datagrams without a guarantee that they arrive, arrive only once, or arrive in order. *Reliable UDP* is not a built-in mode of UDP: it means an **application protocol** adds the mechanisms it needs.

Your assignment (p.4) asks you to choose and justify **one** reliability scheme. The permitted choices are Stop-and-Wait, Go-Back-N, and Selective Repeat; comparing two can earn bonus credit.

```text
Original file     [0] [1] [2] [3]
Sender             \   |   |   /
                    UDP datagrams
Network: loss / corruption / duplication / reordering
Receiver          [0] [2] [3] [2]
                    ?  missing #1

Reliable design may use:
  sequence numbers + checksum + ACKs + timeout + retransmissions
  + duplicate handling + correct chunk placement
```

## 1. Packet loss

A packet is **lost** when it does not arrive at the receiver. Congestion, equipment problems, or deliberately configured emulation can cause drops. UDP offers no built-in retry.

- **Analogy:** A postal letter disappears before reaching its recipient.
- **Assignment connection:** Experiment B3 requires trials with 0%, 1%, 5%, 10%, and 20% simulated loss.
- **Common mistake:** Assuming UDP automatically loses a fixed proportion of traffic; clean loopback networks often lose none.

## 2. Corruption

**Corruption** occurs when some bytes differ from what the sender intended. The assignment's four-byte CRC32 field can detect many accidental errors within a data payload; corrupted packets must be discarded.

- **Analogy:** The letter arrives, but its message is smudged.
- **Assignment connection:** CRC32 corruption detection is mandatory for UDP error management.
- **Common mistake:** Assuming a checksum corrects damaged bytes. It generally **detects** an error; recovery needs retransmission or another mechanism.

**Security note:** CRC32 is not designed to resist a malicious attacker. It detects accidental changes, unlike cryptographic authentication. SHA-256 also does not authenticate a sender by itself.

## 3. Duplicate packets

A **duplicate** is another copy of a packet already delivered. For example, the receiver may receive DATA #4, but the ACK for #4 might be lost. The sender times out and sends DATA #4 again.

- **Analogy:** A customer receives an order twice because the delivery confirmation vanished.
- **Assignment connection:** Duplicates must be detected and discarded, rather than appended a second time.
- **Common mistake:** Assuming the retransmission is unnecessary because the receiver actually got the data. The **sender does not know that** until it receives acknowledgement.

## 4. Out-of-order delivery

Datagrams may arrive in a different order from transmission. If the file is assembled in arrival order, the bytes can be wrong even when the file length is unchanged.

```text
Sent:      DATA #0  DATA #1  DATA #2  DATA #3
Arrived:   DATA #0  DATA #2  DATA #1  DATA #3

Wrong reconstruction: chunk0 + chunk2 + chunk1 + chunk3
Right reconstruction: chunk0 + chunk1 + chunk2 + chunk3
```

- **Analogy:** Pages of a book delivered in shuffled envelopes.
- **Assignment connection:** B4 demands one incorrect reconstruction (hash mismatch) and one corrected version (hash match); count reordered chunks and maximum displacement.
- **Common mistake:** Thinking UDP guarantees disorder. Reordering is *possible*, not inevitable; testing usually needs controlled impairment.

## 5. Sequence numbers

A **sequence number** identifies a chunk's intended position. In this assignment it occupies four bytes in the application header. If chunks have fixed size, sequence number `i` corresponds to a conceptual file offset `i × chunk_size`, except that the final chunk may be shorter.

- **Analogy:** Number each book page before mailing it.
- **Assignment connection:** Sequence numbers help identify duplicates, missing chunks, and correct file positions.
- **Common mistake:** Confusing **sequence number** with **arrival index** (the order in which the receiver first observes a packet).

## 6. ACKs and NACKs

An **ACK** (positive acknowledgement) tells a sender that data has been received according to the chosen protocol. A **NACK** (negative acknowledgement) identifies missing or rejected data when the protocol uses one.

Different reliability designs interpret ACKs differently:

- **Stop-and-Wait:** usually acknowledge the current packet.
- **Go-Back-N:** usually cumulative ACKs indicate how far consecutive data has been received.
- **Selective Repeat:** typically ACK individual chunks.

The assignment's packet-type enumeration includes DATA, ACK, NACK, START and END, but not every ARQ design requires active use of NACK messages. Whether the professor expects NACK processing in an ACK-only scheme is a valid open question.

- **Analogy:** "Delivery received" vs "Parcel 3 missing."
- **Assignment connection:** Acknowledgements are necessary for the selected reliability mechanism.
- **Common mistake:** Assuming an ACK always acknowledges exactly one chunk or that no ACK proves the data was lost.

## 7. Timeouts and retransmissions

A **timeout** is the maximum period the sender will wait for an acknowledgement before attempting recovery. A **retransmission** sends data again. The sender must also limit retries so an unreachable peer does not cause an endless loop.

```text
Time moves DOWN

CLIENT                            SERVER
  | ---- DATA #0 ----------------> |
  | <-------------------- ACK #0 --|
  | ---- DATA #1 ----X             |   [DATA is lost]
  |                                 |
  | ... timeout expires ...         |
  | ---- DATA #1 ----------------> |
  | <-------------------- ACK #1 --|
```

What if the original DATA #1 arrived but its **ACK** was lost? The sender might retransmit DATA #1 anyway. The receiver needs duplicate detection and should acknowledge according to the chosen scheme.

- **Analogy:** Sending a parcel again when the receipt never arrived.
- **Assignment connection:** B3 requires timeout selection, retransmission, retry limit, and a clean error if the peer disappears.
- **Common mistake:** Selecting a timeout shorter than ordinary network RTT and then concluding all resulting retransmissions indicate network loss.

## 8. Stop-and-Wait ARQ

**Automatic Repeat reQuest (ARQ)** combines acknowledgements and retransmissions to recover from loss.

Stop-and-Wait permits **one outstanding data packet**. The sender does not start the next packet until the current one is acknowledged or recovery fails.

```text
Sender                             Receiver
DATA #0 ------------------------->
       <------------------------- ACK #0
DATA #1 ---------------X
                (timeout)
DATA #1 ------------------------->
       <------------------------- ACK #1
DATA #2 ------------------------->
```

**Advantages:** simplest reasoning, small state space, easier to debug and explain.

**Limitations:** if the network has appreciable RTT, waiting after every chunk wastes capacity. The maximum useful rate is constrained by roughly one chunk per acknowledgement cycle (plus sending time).

- **Analogy:** The courier waits for a receipt before taking the next parcel.
- **B3 fit:** provides acknowledgements, timeout, retries, duplicate handling.
- **B4 nuance:** with just one original DATA packet in flight, normal out-of-order delivery between distinct DATA chunks is uncommon or impossible under a strictly sequenced transfer. B4's disorder demonstration would need a separate naive/burst transfer mode or an appropriate controlled experiment; the project must still explain and handle reordering.

**Common mistake:** Assuming correctness implies good throughput.

## 9. Go-Back-N ARQ

Go-Back-N permits up to **N outstanding packets**. The receiver generally accepts the next in-order packet and uses **cumulative ACKs**. If an earlier packet is lost, subsequent packets may be wasted and the sender may need to retransmit multiple outstanding chunks.

```text
Window size = 4

Sender                         Receiver
DATA #0 ----------------------> accept #0
DATA #1 ----------X             missing #1
DATA #2 ----------------------> later chunk; normally discard
DATA #3 ----------------------> later chunk; normally discard
      (timeout for #1)
DATA #1 ----------------------> accept #1
DATA #2 ----------------------> accept #2
DATA #3 ----------------------> accept #3
```

**Advantages:** better network utilization than Stop-and-Wait in many conditions; conceptually simpler receiver than Selective Repeat.

**Limitations:** unnecessary retransmission when one lost packet forces re-sending multiple packets.

- **Analogy:** Multiple parcels are in transit, but a missing confirmation can trigger resending the group.
- **B3 fit:** window, cumulative ACK, timeout, retries, duplicates.
- **B4 fit:** can receive out-of-order packets but classic Go-Back-N often discards them; the assignment's correct reassembly demonstration still needs careful design and isolation from ARQ behavior.
- **Common mistake:** Assuming Go-Back-N retransmits only packets actually lost.

## 10. Selective Repeat ARQ

Selective Repeat also permits multiple outstanding data packets, but the sender retransmits **only the chunks known or suspected to be missing**. The receiver can buffer valid out-of-order chunks until earlier gaps have been filled.

```text
Sender                         Receiver
DATA #0 ----------------------> store #0, ACK #0
DATA #1 ----------X             missing #1
DATA #2 ----------------------> store #2, ACK #2
DATA #3 ----------------------> store #3, ACK #3
      (timeout for #1)
DATA #1 ----------------------> store #1, ACK #1

Reconstructed file order: #0, #1, #2, #3
```

**Advantages:** efficient retransmission under loss; natural support for out-of-order buffering.

**Limitations:** more state, more intricate sequence/window logic, timer management, and buffering. Sequence-number wrap-around needs care for long-lived schemes.

- **Analogy:** When parcel #3 is missing, only parcel #3 must be resent.
- **B3 fit:** acknowledgements, retransmission of missing data, duplicate handling.
- **B4 fit:** integrates naturally with out-of-order data handling.
- **Common mistake:** Assuming it is automatically the best choice without accounting for implementation complexity and deadline risk.

## 11. Sliding windows

A **window** is the number (or amount) of packets/bytes allowed to remain unacknowledged. After acknowledgement, the sender's allowed range slides forward.

```text
Stop-and-Wait (window 1):
[ 0 ]  1   2   3   4   5       only #0 is in flight

Sliding window of 4:
[ 0   1   2   3 ]  4   5       up to four in flight

After ACK of #0:
  0  [ 1   2   3   4 ]  5
```

- **Analogy:** A conveyor belt keeps several packages moving simultaneously.
- **Assignment connection:** Required CLI options include window size; a sliding-window comparison is a bonus experiment.
- **Common mistake:** Confusing *window size* (count of outstanding datagrams) with *chunk size* (bytes per datagram).

## 12. Flow control versus congestion control

**Flow control** protects the receiving application and its buffers from being overwhelmed. **Congestion control** reduces pressure on the network and its intermediate devices. They solve different problems, even though both can affect how fast a sender transmits.

- **Analogy:** Warehouse receiving capacity versus road traffic capacity.
- **Assignment connection:** B2 compares explicit delays and loss; interpreting buffer overflow requires understanding receiver limits.
- **Common mistake:** Assuming that adding ACKs alone creates complete flow control or fair network congestion control.

---

## Compare the three schemes — research preview

| Question | Stop-and-Wait | Go-Back-N | Selective Repeat |
| --- | --- | --- | --- |
| Data packets simultaneously in flight | 1 | Up to N | Up to N |
| ACK strategy | Per current chunk | Typically cumulative | Usually per chunk |
| Recovery from one lost chunk | Resend that chunk | May resend whole outstanding range | Usually resend only missing chunk |
| Receiver complexity | Low | Low–moderate | Higher, buffers out of order |
| Potential link utilization | Low when RTT is high | Often better | Often better |
| Implementation risk | Lower | Moderate | Higher |
| Automatically satisfies *every* assignment requirement? | No; B4 still requires an explicit reordering demonstration | No; B4 demonstration needs attention | No; full B1–B4 experiments and hashing still needed |

**No decision has been made** about which reliability scheme to adopt. Stage 3 will compare them against deadline risk, reproducibility, and explainability.

## Knowledge checks

1. If the sender times out, is that proof that the DATA packet was lost?
2. If the receiver gets packets #0, #2, #1, what information enables correct reassembly?
3. What is the difference between cumulative and per-packet acknowledgements?
4. Why can Stop-and-Wait have poor throughput even with zero packet loss?
5. How can a lost ACK lead to a duplicate DATA packet?
6. Why is Selective Repeat harder to implement than Stop-and-Wait?
7. What is the difference between flow control and congestion control?

<details>
<summary>Answers</summary>

1. No. The DATA, ACK, or timing could be responsible.
2. Sequence numbers (and correct chunk placement).
3. A cumulative ACK covers a contiguous acknowledged prefix; per-packet ACKs identify individual received chunks.
4. A single outstanding packet leaves the sender idle while waiting for the round trip.
5. The sender may retransmit data that was successfully received but not acknowledged.
6. It manages many outstanding chunks, possible per-chunk timers, and out-of-order buffering.
7. Flow control protects the receiver; congestion control protects the network.

</details>

## References

- Assignment: *TCP vs UDP File Transfer*, pp. 3–4, 8.
- Kurose & Ross, *Computer Networking: A Top-Down Approach*: `3.4 on reliable data transfer and ``3.5–3.6 on TCP and congestion control (ninth-edition course resources).
- [RFC 768 — UDP](https://www.rfc-editor.org/rfc/rfc768).
- [RFC 9293 — TCP](https://www.rfc-editor.org/rfc/rfc9293).

[Previous: fundamentals](01-fundamentals.md) · [Course index](README.md) · [Next: measurement](03-measurement.md)
