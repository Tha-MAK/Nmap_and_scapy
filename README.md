# Nmap & Scapy Lab Notes

## 1) Overview

This lab was a hands-on refresher on two core skill areas in security testing:

* **Reconnaissance / enumeration** with **Nmap**
* **Packet capture and inspection** with **Scapy**

All activity was performed in a controlled internal environment against a single target host.

> Use these commands only on networks/systems you own or have explicit permission to test.

---

## 2) Lab Setup

**Platform:** Kali Linux VM (Ethical Hacker course environment)
**Tools:** Nmap, Scapy
**Target host:** `10.6.6.23`
**Subnet:** `10.6.6.0/24`

---

## 3) Nmap Exercises

### 3.1 Host discovery (ping sweep)

```bash
nmap -sn 10.6.6.0/24
```

**What it does:** Scans the subnet to identify which IPs respond as “up.” 
**Observed outcome:** `10.6.6.23` responded and was listed as reachable.

---

### 3.2 OS fingerprinting

```bash
sudo nmap -O 10.6.6.23
```

**What it does:** Attempts to infer the target OS using TCP/IP fingerprint behavior. 
**Observed outcome:** Nmap returned an OS “Linux 4.15 - 5.8".

---

### 3.3 FTP service and version detection

```bash
nmap -p21 -sV -A -T4 10.6.6.23
```

**What it does:** Checks port 21, identifies the service/version, and runs additional detection (-A).
**Observed outcome:** FTP details were discovered, providing useful enumeration data.

---

### 3.4 SMB share enumeration (port 445)

```bash
nmap --script smb-enum-shares.nse -p445 10.6.6.23
```

**What it does:** Uses an NSE script to list SMB shares and related info (when accessible).
**Observed outcome:** Share information was returned, indicating possible exposure.

---

### 3.5 Manual SMB check (anonymous)

```bash
smbclient //10.6.6.23/print$ -N
```

**What it does:** Tests SMB access without credentials (`-N`), to confirm whether guest/anonymous access is allowed.
**Observed outcome:** Anonymous connection succeeded, which could represent a security weakness depending on configuration.
Exit with:

```bash
exit
```

---

## 4) Scapy Exercises

### 4.1 Launch Scapy

```bash
sudo su
scapy
```

---

### 4.2 Capture traffic (default interface)

```python
sniff()
```

**What it does:** Starts a live capture on the default interface until you stop it.
**How traffic was generated:** From another terminal:

```bash
ping cisco.com
```

**Observed outcome:** Packets such as ICMP and DNS were captured.
Stop capture with **Ctrl + C**, then review:

```python
a = _
a.summary()
```

---

### 4.3 Capture traffic on a specific interface

```python
sniff(iface="br-internal")
```

**What it does:** Sniffs traffic only on the `br-internal` interface (internal bridge).
**How traffic was generated:**

* Browsed to `10.6.6.23`
* Performed pings on the internal network

Then review:

```python
a = _
a.summary()
```

---

### 4.4 Capture only ICMP packets (limited count)

```python
sniff(iface="br-internal", filter="icmp", count=5)
```

**Test traffic:**

```bash
ping 10.6.6.23
```

**Observed outcome:** Exactly 5 ICMP packets were captured (as requested by `count=5`).
Inspect:

```python
a = _
a.summary()
a[3]
```

---

## 5) What I Took Away

* Nmap is effective for quickly mapping live hosts and enumerating exposed services.
* Service/version detection and scripting (NSE) can reveal misconfigurations early.
* SMB checks can highlight risky setups such as guest/anonymous access.
* Scapy makes packet-level visibility practical: you can capture broadly, filter tightly, and inspect individual packets in detail.
* These steps mirror what happens in the early phases of real assessments (recon → enumeration → validation).

---

## 6) Wrap-up

This lab improved my comfort level with both network scanning (Nmap) and traffic inspection (Scapy). Practicing these basics in a safe environment reinforces how attackers gather information—and how defenders can spot and reduce that exposure.

