---
layout: post
title: Evil Twin AP Attacks
---

## What is Evil Twin AP Attack?

An evil twin is a malicious Wi-Fi access point (AP) that appears to be legitimate but is set up to eavesdrop on traffic, establish "man-in-the-middle" (MitM) positions with the intention of stealing sensitive information like usernames and passwords. Laptops, tablets and smartphones cannot distinguish between fake and real APs that have the same name (SSID).

```mermaid
sequenceDiagram
Eve ->> WiFi-AP: Eve jams channel 6
Alice-->>Eve: Alice roams to evil twin ap on channel 11 and associates
Eve--x Alice: Eve has DHCP server setup to issue IP address to Alice
```

```mermaid
sequenceDiagram
Alice ->> Bob: Hello Bob, how are you?
Bob-->>John: How about you John?
Bob--x Alice: I am good thanks!
Bob-x John: I am good thanks!
Note right of John: Bob thinks a long<br/>long time, so long<br/>that the text does<br/>not fit on a row.

Bob-->Alice: Checking with John...
Alice->John: Yes... John, how are you?
```

## Defense Against The Attack
use VPN
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjExNjYyMDU4NywzMDE5MDU5NTBdfQ==
-->