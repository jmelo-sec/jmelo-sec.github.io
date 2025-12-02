---
layout: single
title: "RTMP/HLS Traffic Analysis with Wireshark"
permalink: /projects/rtmp-hls-wireshark/
author_profile: false
---

## Project Overview

This lab exercise focuses on analysing RTMP/HLS streaming traffic using Wireshark.  
The goal is to understand how video streams are delivered over the network, identify the main HTTP requests involved and highlight security implications when this traffic is not properly protected.

---

## Objective

From a Blue Team perspective, I wanted to:

- Capture RTMP/HLS traffic during a real playback session.
- Identify playlist (`.m3u8`) and segment (`.ts`) requests.
- Observe how the client and server communicate over TCP and HTTP.
- Check whether the traffic is encrypted or not.
- Assess what information could be exposed to an attacker on the same network.

---

## Environment and Tools

- **Wireshark** – protocol analysis.
- **Web browser** – HLS client.
- **Streaming server** – serving HLS content on TCP port `8088`.
- Basic Wireshark display filters for HTTP and TCP.

To focus only on the streaming traffic, I used the following display filter:

```text
http && tcp.port == 8088
```
---

## Methodology

1. Start Wireshark capture using the predefined filter.
2. Begin playback of the HLS stream.
3. Inspect the first HTTP requests (playlist and segments).
4. Follow the TCP stream for deeper inspection.
5. Look for TLS handshakes to determine encryption.
6. Identify technical findings and potential risks.

---

## Key Technical Findings

**1. HLS Request Pattern**

Client first requests the main playlist:

```text
GET /hls/stream.m3u8 HTTP/1.1
```

Then downloads multiple .ts segments:

```text
GET /hls/segment-0001.ts
GET /hls/segment-0002.ts
```

**2. HTTP Over TCP**

Observed:

- Standard TCP 3-way handshake (SYN, SYN/ACK, ACK)
- Clear HTTP communication (no TLS)
- Standard headers:
    - Host
    - User-Agent
    - Accept
    - Connection

**3. Behaviour and Performance**

- Regular segment retrieval pattern aligned with playback.
- No packet loss or retransmissions.
- Stream behaviour easy to detect and correlate in monitoring tools.

---

## Security Implications

**Risks**

- Unencrypted traffic allows packet inspection by anyone on the same network.
- Potential content tampering: .ts segments could be intercepted and modified.
- No authentication: If URLs are public, anyone can download video segments.

**Recommended Mitigations**

- Serve all HLS traffic over HTTPS.
- Use signed URLs or token-based access.
- Apply access control to playlists and segments.
- Implement monitoring for unusual access patterns.

---

## What I Learned

- How to quickly isolate and analyse HLS traffic in Wireshark.
- How to distinguish encrypted vs. cleartext traffic at a glance.
- How to map technical packet details into real security risks.
- How to document traffic analysis in a clear, recruiter-friendly format.

---
