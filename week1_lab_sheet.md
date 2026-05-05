# LAB SHEET — WEEK 1
## Computer Networks | CDIO Framework | Undergraduate Level

---

| | |
|---|---|
| **Course** | Computer Networks |
| **Week** | 1 — Network Fundamentals, OSI Model & IEEE 802 Standards |
| **Session** | 1.B (3 hours) |
| **CDIO Phase** | Conceive |
| **Student Name** | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |
| **Student ID** | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |
| **Date** | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ |

# 🔍 FRAME DETECTIVE AGENCY
## *A tcpdump Investigation into the IEEE 802.3 Underworld*

---

```
  ╔══════════════════════════════════════════════════════════════╗
  ║   Welcome, Detective.                                        ║
  ║                                                              ║
  ║   Someone has been sending mysterious frames across this     ║
  ║   network. Your job: intercept them, decode them, and        ║
  ║   figure out exactly what is going on at Layer 2.            ║
  ║   Your only tool: tcpdump. No GUI. No clicking.              ║
  ║   Just you, a terminal, and raw packet data.                 ║
  ║                                                              ║
  ║   Good luck. The network doesn't lie.                        ║
  ╚══════════════════════════════════════════════════════════════╝
```

---

## Briefing

| | |
|---|---|
| **Tool** | `tcpdump` (terminal only — no Wireshark) |
| **Evidence file** | `week1.pcap` (download from LMS → save to `~/lab/week1.pcap`) |
| **Environment** | WSL2 (Ubuntu) or any Linux terminal |
| **Mode** | Individual investigation |
| **Total points** | 60 pts across 6 missions + bonus |

**Your terminal must show:** your student ID as the shell prompt before every screenshot.  
Run this once at the start: `export PS1="[STUID-YOURNAME] \$ "` (replace with your actual ID and name)

---

## Tool Orientation — Read Before You Start

`tcpdump` is a command-line packet analyzer used by network engineers, security researchers, and system administrators worldwide. Unlike a GUI, it shows you the raw decoded output in your terminal — which means you must *read* and *interpret* rather than just click.

**Reading from a saved capture file** (what you'll do today — no live capture needed):
```bash
tcpdump -r week1.pcap
```

**The flags you'll need today:**

| Flag | What It Does | Example |
|------|-------------|---------|
| `-r file` | Read from a saved pcap file | `tcpdump -r week1.pcap` |
| `-e` | **Print MAC addresses** (link-layer headers) | `tcpdump -r week1.pcap -e` |
| `-n` | Do not resolve hostnames or ports to names | `tcpdump -r week1.pcap -n` |
| `-c N` | Stop after N packets | `tcpdump -r week1.pcap -c 5` |
| `-q` | Quiet mode — shorter output lines | `tcpdump -r week1.pcap -q` |
| `-v` | Verbose — more detail per packet | `tcpdump -r week1.pcap -v` |
| `-vv` | Very verbose | `tcpdump -r week1.pcap -vv` |
| `-xx` | Print hex dump of each frame (including headers) | `tcpdump -r week1.pcap -xx` |
| `-XX` | Print hex dump + ASCII side-by-side | `tcpdump -r week1.pcap -XX` |
| `-A` | Print payload in ASCII only | `tcpdump -r week1.pcap -A` |

**Combining flags** (stack them — order does not matter):
```bash
tcpdump -r week1.pcap -n -e -c 10
```

**Filtering by protocol or address:**
```bash
tcpdump -r week1.pcap arp
tcpdump -r week1.pcap 'ether broadcast'
tcpdump -r week1.pcap 'ether proto 0x0800'
tcpdump -r week1.pcap 'ether src aa:bb:cc:dd:ee:ff'
tcpdump -r week1.pcap 'ether dst ff:ff:ff:ff:ff:ff'
```

> 💡 **Pro tip:** Pipe output through `less` to scroll: `tcpdump -r week1.pcap -e | less`  
> Press `q` to quit less. Press `/` then type a word to search.

---

## Setup Check (do this first — 2 minutes)

Open your terminal and run:
```bash
mkdir -p ~/lab
cp /path/to/week1.pcap ~/lab/
cd ~/lab
tcpdump -r week1.pcap -c 3
```

If you see packet output → you are ready. If you see an error → raise your hand.

---

---

## 🕵️ MISSION 1 — First Contact
### *"Get a feel for the evidence"*
**Points: 8 | Estimated time: 10 min**

---

Every good detective starts by surveying the scene before touching anything. Your first mission is to understand the scope of the evidence.

**Step 1.1 — Count the frames**

```bash
tcpdump -r week1.pcap -q | wc -l
```

> 📌 **What this does:** `-q` gives one short line per frame; `wc -l` counts lines.

**📋 Record:** Total number of frames in the capture: \_\_\_\_\_\_\_\_\_

---

**Step 1.2 — Get a first look with MAC addresses visible**

```bash
tcpdump -r week1.pcap -n -e -c 20
```

Look at the output carefully. Each line is one frame. The format is:

```
timestamp  SRC_MAC > DST_MAC  ethertype  PROTOCOL  info
```

**📋 Record two different source MACs you observe:**

| Source MAC | Frame # (approx) |
|------------|-----------------|
| | |
| | |

---

**Step 1.3 — Identify the cast of characters**

Run this to extract all unique source MAC addresses in the capture:

```bash
tcpdump -r week1.pcap -n -e -q | awk '{print $2}' | sort | uniq -c | sort -rn
```

> 📌 **What this does:** `awk` extracts the source MAC column, `sort | uniq -c` counts unique occurrences, `sort -rn` orders by frequency descending.

**📋 Record the top 3 most active senders:**

| Rank | MAC Address | # of Frames Sent |
|------|------------|-----------------|
| 1 | | |
| 2 | | |
| 3 | | |

---

**Step 1.4 — What protocols are present?**

```bash
tcpdump -r week1.pcap -n -q | awk '{print $3}' | sort | uniq -c | sort -rn
```

**📋 Record what you find:**

| Protocol / EtherType | Count |
|---------------------|-------|
| | |
| | |
| | |

**❓ Mission 1 Question:** Looking at your protocol list — which IEEE 802 standard governs the framing of ALL of these protocols, regardless of their type? Why?

&emsp;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

---

## 🕵️ MISSION 2 — The Broadcast Storm
### *"Someone is shouting at everyone"*
**Points: 10 | Estimated time: 15 min**

---

Your informant tells you there are frames addressed to *everyone on the network simultaneously*. In Layer 2, this is called a **broadcast**. Your job: find them, count them, and figure out why they exist.

**Step 2.1 — Isolate all broadcast frames**

```bash
tcpdump -r week1.pcap -n -e 'ether broadcast'
```

**📋 Record:**

| | |
|---|---|
| Number of broadcast frames | \_\_\_\_\_ |
| Destination MAC in every broadcast frame | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |
| Protocol(s) seen in broadcast frames | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |

---

**Step 2.2 — Decode one broadcast frame completely**

Pick the first broadcast frame and dump its full hex with:

```bash
tcpdump -r week1.pcap -n -e -XX 'ether broadcast' -c 1
```

The output will look something like:

```
14:23:01.123456 aa:bb:cc:11:22:33 > ff:ff:ff:ff:ff:ff, ethertype ARP (0x0806), length 42
        0x0000:  ffff ffff ffff aabb cc11 2233 0806 0001  ........."..3...
        0x0010:  0800 0604 0001 aabb cc11 2233 c0a8 0101  ......."..3.....
        0x0020:  0000 0000 0000 c0a8 010a                 ..........
```

Using the hex dump, fill in the frame dissection table below.  
*(Bytes are listed left to right in the hex dump. Each pair of hex digits = 1 byte.)*

**📋 Ethernet frame hex dissection:**

```
Bytes 0–5   (6 bytes):  Destination MAC  = __ __ __ __ __ __
Bytes 6–11  (6 bytes):  Source MAC       = __ __ __ __ __ __
Bytes 12–13 (2 bytes):  EtherType        = 0x__ __
Bytes 14+   (payload):  Upper layer data (ARP/IPv4/etc.)
```

Fill in the values from YOUR capture:

| Ethernet Field | Raw Hex Bytes | Decoded Value |
|----------------|--------------|---------------|
| Destination MAC | | |
| Source MAC | | |
| EtherType | | `0x` \_\_\_\_ = \_\_\_\_\_\_\_\_ protocol |

---

**Step 2.3 — The broadcast question**

A student says: *"If I send a broadcast frame, every computer in the world receives it."*  

**❓ Is this correct? Explain what actually bounds a broadcast domain at Layer 2, and why the statement is wrong.**

&emsp;Correct / Incorrect (circle one)

&emsp;Explanation: \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  
&emsp;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

---

## 🕵️ MISSION 3 — Identity Check
### *"Every suspect has a manufacturer"*
**Points: 10 | Estimated time: 15 min**

---

MAC addresses are not random — the first 3 bytes (OUI) identify the manufacturer of the network interface. In this mission you will identify *who made* the devices sending frames in this capture.

**Step 3.1 — Extract the top senders with full MAC addresses**

```bash
tcpdump -r week1.pcap -n -e -q | awk '{print $2}' | sort | uniq -c | sort -rn | head -5
```

Pick **two different source MACs** from the output.

**MAC under investigation #1:** \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  
**MAC under investigation #2:** \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

**Step 3.2 — Decode the OUI bits manually**

For each MAC, fill in the table:

| | MAC #1 | MAC #2 |
|---|--------|--------|
| Full MAC | | |
| First byte (hex) | 0x\_\_ | 0x\_\_ |
| First byte (binary) | \_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_ |
| Bit 0 (IG bit) | \_\_ | \_\_ |
| Unicast or Multicast? | | |
| Bit 1 (LG bit) | \_\_ | \_\_ |
| Globally unique or local? | | |
| OUI (first 3 bytes) | \_\_:\_\_:\_\_ | \_\_:\_\_:\_\_ |

---

**Step 3.3 — OUI lookup**

Go to the IEEE OUI registry:  
**`https://regauth.standards.ieee.org/standards-ra-web/pub/view.html#registries`**

Click **"MA-L (OUI)"**, search using format `XX-XX-XX` (dashes, not colons).

| OUI | Vendor Name |
|-----|------------|
| \_\_:\_\_:\_\_ | |
| \_\_:\_\_:\_\_ | |

---

**Step 3.4 — Verify with tcpdump's built-in OUI resolution**

Run the same command *without* the `-n` flag (hostname resolution on):

```bash
tcpdump -r week1.pcap -e -q | awk '{print $2}' | sort | uniq -c | sort -rn | head -5
```

Notice that tcpdump now shows the vendor prefix embedded in the MAC (e.g., `Apple_3a:11:02` instead of `c8:cb:b8:3a:11:02`).

**📋 Does tcpdump's vendor match your OUI lookup?**  

| MAC | OUI Registry Says | tcpdump Says | Match? |
|-----|-------------------|-------------|--------|
| | | | Y / N |
| | | | Y / N |

**❓ Mission 3 Question:** The IEEE OUI registry has over 40,000 entries. Why does this database need to be maintained carefully, and what security risk arises if a device presents a locally-administered (LG bit = 1) MAC address?

&emsp;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  
&emsp;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

---

## 🕵️ MISSION 4 — The EtherType Codebook
### *"Every frame carries a secret about what's inside"*
**Points: 10 | Estimated time: 15 min**

---

The **EtherType** field in every Ethernet frame is a 2-byte code that reveals what protocol is hiding in the payload. Your mission: map every EtherType in the capture to its protocol and explain what each one means.

**Step 4.1 — Extract every unique EtherType in the capture**

```bash
tcpdump -r week1.pcap -n -e -q | grep -oP 'ethertype \w+ \(0x\w+\)' | sort | uniq -c | sort -rn
```

**📋 Record all EtherTypes found:**

| EtherType (hex) | Protocol Name | Count | OSI Layer of encapsulated protocol |
|----------------|--------------|-------|------------------------------------|
| 0x | | | |
| 0x | | | |
| 0x | | | |
| 0x | | | |

---

**Step 4.2 — Isolate each type and observe differences**

Run each filter and note what the output line looks like:

**IPv4 frames:**
```bash
tcpdump -r week1.pcap -n -e -c 3 'ether proto 0x0800'
```
Output sample (copy one line here): \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**ARP frames:**
```bash
tcpdump -r week1.pcap -n -e -c 3 'ether proto 0x0806'
```
Output sample: \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**If 802.1Q frames exist (EtherType 0x8100):**
```bash
tcpdump -r week1.pcap -n -e -c 3 'ether proto 0x8100'
```
Output sample (or write "none found"): \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

**Step 4.3 — Count ARP requests vs ARP replies**

ARP has two sub-types: *who-has* (request) and *is-at* (reply). tcpdump labels them in the info field.

```bash
tcpdump -r week1.pcap -n 'arp' | grep -c "who-has"
tcpdump -r week1.pcap -n 'arp' | grep -c "is-at"
```

**📋 Record:**

| ARP Type | Count |
|----------|-------|
| ARP Requests (who-has) | |
| ARP Replies (is-at) | |

**❓ Mission 4 Question:** Why are there typically *more* ARP requests than ARP replies in a normal capture? What would a very high ratio of requests-to-replies tell a security analyst?

&emsp;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  
&emsp;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

---

## 🕵️ MISSION 5 — The Invisible Field
### *"The FCS is real — but where is it?"*
**Points: 8 | Estimated time: 10 min**

---

You know from the lecture that every Ethernet frame ends with a **4-byte FCS (Frame Check Sequence)**. But when you look at tcpdump output, you never see it. Is the FCS real, or is it a myth?

**Step 5.1 — Look for FCS in a hex dump**

```bash
tcpdump -r week1.pcap -n -e -XX -c 1
```

Study the hex output. Count the bytes from the first byte after the SFD (i.e., from Dst MAC) to the last byte shown.

**📋 Observation:**

| | |
|---|---|
| Total bytes shown in hex dump (from Dst MAC to last byte) | \_\_\_\_\_ |
| Does the hex dump end with 4 bytes of FCS? | Yes / No |

---

**Step 5.2 — Check frame length metadata**

```bash
tcpdump -r week1.pcap -n -e -v -c 5
```

With `-v`, tcpdump shows frame length. Look for the `length` field in each line.

**📋 Record frame lengths for 3 frames:**

| Frame # | Length (bytes) | Does this include FCS? |
|---------|---------------|------------------------|
| 1 | | |
| 2 | | |
| 3 | | |

---

**Step 5.3 — The FCS mystery explained**

Read this and answer the question:

> When a network interface card (NIC) receives an Ethernet frame, it immediately performs CRC-32 validation of the FCS **in hardware** — before the frame ever reaches the operating system. If the FCS is valid, the NIC strips it and delivers the frame to the OS. If invalid, the NIC silently drops the frame. Since tcpdump captures frames from the OS network stack, it receives frames *after* the NIC has already processed and stripped the FCS.

**❓ Mission 5 Question (two parts):**

**(a)** At which IEEE 802 sub-layer does FCS validation occur — LLC or MAC? Why?

&emsp;Sub-layer: \_\_\_\_\_\_\_\_\_\_ &emsp; Reason: \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**(b)** A student says: "Since tcpdump doesn't show FCS, maybe Ethernet doesn't actually use error detection." How would you correct this misunderstanding?

&emsp;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  
&emsp;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

---

## 🕵️ MISSION 6 — Closing the Case
### *"Connect the evidence to the theory"*
**Points: 14 | Estimated time: 15 min**

---

Every piece of evidence you've collected maps back to a concept from Session 1.A. This final mission connects the raw packet data to the theory — closing the investigation.

---

**Step 6.1 — Build your traffic profile**

Using everything you've gathered, complete the network profile table:

**📋 Network profile from `week1.pcap`:**

| Question | Your Finding |
|----------|-------------|
| Total frames captured | |
| Number of unique source MACs | |
| Most active sender MAC | |
| Vendor of most active sender | |
| Most common EtherType | |
| Protocol carried by most common EtherType | |
| Number of broadcast frames | |
| ARP request count | |
| 802.1Q VLAN frames present? | Yes / No |

---

**Step 6.2 — The EtherType as the LLC multiplexer**

Run this to see all EtherTypes side by side:

```bash
tcpdump -r week1.pcap -n -e -q | awk '{print $5, $6}' | sort | uniq -c | sort -rn
```

**❓ Q6.2:** In Session 1.A, you learned that the **LLC sub-layer** was designed so that Layer 2 can carry multiple different Layer 3 protocols. In modern Ethernet, the EtherType field performs this multiplexing role.

Using your results above, write a one-paragraph explanation of how the EtherType field in your capture demonstrates the LLC concept — even though explicit LLC headers are not present.

```
┌──────────────────────────────────────────────────────────────────┐
│                                                                  │
│                                                                  │
│                                                                  │
│                                                                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

**Step 6.3 — CDIO reflection**

You just spent the past 90 minutes in **tcpdump observation mode** — examining a real network capture to understand what actually happens at Layer 2 before any configuration is done.

**❓ Q6.3:** Which CDIO phase best describes today's lab — C, D, I, or O?

&emsp;Phase: \_\_\_\_\_

In 2–3 sentences, explain why we do this phase *before* touching any device configuration:

&emsp;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  
&emsp;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

---

## 🏆 BONUS MISSIONS
*For fast detectives — complete any for extra credit*

---

### Bonus A — The Most Chatty Pair (3 pts)

Find the pair of MAC addresses (src → dst) that communicated with each other the most times:

```bash
tcpdump -r week1.pcap -n -e -q | awk '{print $2, $3, $4}' | sort | uniq -c | sort -rn | head -5
```

**Most active conversation:**  
From: \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ → To: \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Count: \_\_\_\_\_

**What does this conversation tell you about the network?** \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

### Bonus B — Frame Size Distribution (3 pts)

Run this to see the size distribution of captured frames:

```bash
tcpdump -r week1.pcap -n -e -q | awk '{print $NF}' | sort -n | uniq -c
```

*(Note: `$NF` = last field in the line, which is the frame length)*

**📋 Record:**

| Frame Size Range | Count |
|-----------------|-------|
| 64 bytes (minimum) | |
| 65 – 256 bytes | |
| 257 – 1024 bytes | |
| 1025 – 1500 bytes | |
| > 1500 bytes (jumbo?) | |

**Why is 64 bytes the minimum Ethernet frame size?** \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

---

### Bonus C — Build Your Own Filter (3 pts)

Write a single `tcpdump` command that shows **only broadcast frames that are NOT ARP** — i.e., broadcasts carrying some other protocol.

```bash
# Your command:
tcpdump -r week1.pcap -n -e ____________________________________
```

Run it. How many frames match? \_\_\_\_\_ What protocol are they? \_\_\_\_\_\_\_\_\_\_\_\_\_

---

### Bonus D — The Hidden Message (5 pts)

One frame in the capture has an unusually crafted payload containing a hidden ASCII message. Find it and decode it.

```bash
tcpdump -r week1.pcap -A | grep -v "^[0-9]" | grep -v "^$" | grep -v "\.\."
```

**Hidden message:** \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

*(Instructors: embed a plaintext string in one frame payload when generating the PCAP — e.g., `CN2025_LAYER2_DETECTIVE` — using scapy or Packet Tracer custom PDU)*

---

---

## 📊 Mission Score Card

| Mission | Description | Max Pts | Your Pts |
|---------|-------------|---------|----------|
| Mission 1 | First Contact | 8 | |
| Mission 2 | The Broadcast Storm | 10 | |
| Mission 3 | Identity Check | 10 | |
| Mission 4 | The EtherType Codebook | 10 | |
| Mission 5 | The Invisible Field | 8 | |
| Mission 6 | Closing the Case | 14 | |
| Bonus A | Most Chatty Pair | 3 | |
| Bonus B | Frame Size Distribution | 3 | |
| Bonus C | Custom Filter | 3 | |
| Bonus D | Hidden Message | 5 | |
| **TOTAL** | | **60 + 14 bonus** | |

---

## Submission

**What to submit to LMS before Week 2 Session A:**
1. This completed lab sheet (scan or clear photo of every page)
2. A single text file `commands_YOURID.txt` containing every tcpdump command you ran, in order — copy-paste from your terminal history: `history | grep tcpdump >> commands_YOURID.txt`

---

## tcpdump Quick-Reference Card
*Keep this open while working*

```bash
# ── READING FILES ─────────────────────────────────────────────────
tcpdump -r week1.pcap                  # Basic read
tcpdump -r week1.pcap -e               # + MAC addresses
tcpdump -r week1.pcap -n               # No name resolution
tcpdump -r week1.pcap -c 10            # First 10 frames only
tcpdump -r week1.pcap -n -e -q         # Short one-line output

# ── HEX DUMP ──────────────────────────────────────────────────────
tcpdump -r week1.pcap -xx              # Hex (no ASCII)
tcpdump -r week1.pcap -XX             # Hex + ASCII
tcpdump -r week1.pcap -A              # ASCII payload only

# ── FILTERS ───────────────────────────────────────────────────────
tcpdump -r week1.pcap arp
tcpdump -r week1.pcap 'ether broadcast'
tcpdump -r week1.pcap 'ether proto 0x0800'   # IPv4
tcpdump -r week1.pcap 'ether proto 0x0806'   # ARP
tcpdump -r week1.pcap 'ether proto 0x86dd'   # IPv6
tcpdump -r week1.pcap 'ether proto 0x8100'   # 802.1Q
tcpdump -r week1.pcap 'ether src aa:bb:cc:dd:ee:ff'
tcpdump -r week1.pcap 'ether dst ff:ff:ff:ff:ff:ff'
tcpdump -r week1.pcap 'not arp'              # Exclude ARP

# ── USEFUL COMBOS ─────────────────────────────────────────────────
tcpdump -r week1.pcap -n -e -XX arp -c 1     # First ARP frame, full hex
tcpdump -r week1.pcap -n -e 'ether broadcast' | wc -l   # Count broadcasts
tcpdump -r week1.pcap -A | grep -i "http"    # Find HTTP strings in payload

# ── PIPING & COUNTING ─────────────────────────────────────────────
tcpdump -r week1.pcap -q | wc -l             # Total frame count
... | awk '{print $2}' | sort | uniq -c      # Count by src MAC
... | grep -c "who-has"                      # Count ARP requests
```

---

*Lab 1.1 — Frame Detective Agency | Computer Networks Week 1 | CDIO: Conceive | v3.0*
