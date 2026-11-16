# Networking Mini-Course

**Audience:** Experienced software developer, beginner in computer networking.  
**Status:** Research-only learning materials; no code or experiments.  
**Purpose:** Prepare to understand the TCP vs UDP file-transfer assignment before making engineering decisions.

## Learning path

| Order | Topic | Suggested focus | Lesson |
| --- | --- | --- | --- |
| 1 | How programs communicate | TCP/IP stack, addresses, ports, sockets, TCP byte streams and UDP datagrams, headers and MTU | [01 — Fundamentals](01-fundamentals.md) |
| 2 | How reliability works | Loss, corruption, duplicates, reordering, ACK/NACK, retransmissions, Stop-and-Wait, Go-Back-N, Selective Repeat | [02 — Reliability](02-reliability.md) |
| 3 | How to evaluate the design | Bandwidth, latency, RTT, throughput, goodput, buffers, impairment, statistics, integrity checks | [03 — Measurement](03-measurement.md) |

**Study goal:** Explain each mechanism in your own words and show how its behavior could be measured. The chapters contain no runnable code.

## Concept map

```text
Client file --> application chunks --> TCP stream or UDP datagrams
                                       |
                         Network: delay, loss, reordering
                                       |
Server receives --> reconstruct file --> compare SHA-256 hashes
                                       |
                  Repeated experiments --> plots --> report
```

## How to use the lessons

1. Read one section and explain the concept out loud in plain English.
2. Study the small examples and diagrams; ask what could fail.
3. Answer the self-check questions without looking at the explanation.
4. Trace the relevant requirement back to the assignment PDF.
5. Do not treat any fictional example numbers as project measurements.

## Essential English–French glossary

| English | Français | English | Français |
| --- | --- | --- | --- |
| Network | Réseau | Client | Client |
| Server | Serveur | IP address | Adresse IP |
| Port | Port | Socket | Socket / prise réseau |
| TCP | Protocole de contrôle de transmission | UDP | Protocole de datagramme utilisateur |
| Datagram | Datagramme | Packet | Paquet |
| Header | En-tête | Payload | Charge utile |
| File chunk | Bloc de données | Sequence number | Numéro de séquence |
| ACK | Accusé de réception | NACK | Accusé négatif |
| Packet loss | Perte de paquets | Corruption | Corruption des données |
| Duplicate | Doublon | Reordering | Désordonnancement |
| Timeout | Expiration du délai d'attente | Retransmission | Réémission |
| Sliding window | Fenêtre glissante | MTU | Unité de transmission maximale |
| Fragmentation | Fragmentation | Bandwidth | Bande passante |
| Latency | Latence | Throughput | Débit |
| Goodput | Débit utile | Checksum / hash | Somme de contrôle / empreinte |

## Recommended study sequence (~9 h 15 min, rough estimate)

- Network delay, loss, performance and layers — ~45 min, Kurose & Ross ``1.4–1.5.
- Application architectures and sockets — ~1 h, ``2.1, 2.6.
- UDP and TCP basics — ~1 h 30 min, ``3.3, 3.5.
- Reliable data transfer and ARQ — ~2 h, `3.4.
- Flow and congestion control — ~45 min, ``3.5–3.6.
- MTU, fragmentation, error detection — ~1 h, ``4.3, 6.2.
- Hash functions and integrity — ~45 min, `8.3.
- Measurement and introductory statistics — ~1 h 30 min, `1.4 and assignment p.6.

Section numbers refer to the ninth-edition course resources; consult your edition's contents if using another edition.

## References

- Assignment: *TCP vs UDP File Transfer*, 12 November 2026, pp. 1–10.
- Kurose & Ross, [public course resources](https://gaia.cs.umass.edu/kurose_ross/ppt.php).
- [RFC 9293 — TCP](https://www.rfc-editor.org/rfc/rfc9293).
- [RFC 768 — UDP](https://www.rfc-editor.org/rfc/rfc768).

[Back to project overview](../README.md)
