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
