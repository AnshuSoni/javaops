---
title: "Diagnosing High Load: A Step-by-Step Troubleshooting Guide"
date: 2025-04-29T19:47:45
draft: false
description: "Learn how to identify and fix high system load issues on a Linux server with this practical, step-by-step guide."
tags: ["linux", "performance", "monitoring", "sysadmin"]
---

High system load on a Linux server can lead to sluggish performance, dropped connections, or even complete system freezes. This guide walks you through a systematic approach to identifying and resolving high load issues.

## 🔍 Step 1: Understand What "Load" Means

System load represents the average number of processes that are either in a runnable or uninterruptible state. It's different from CPU usage. A load of 1.0 on a single-core system means full utilization; on a 4-core system, it means 25%.

Check current load:
```bash
uptime
```

## 📊 Step 2: Use `top` or `htop` to Get a Live Overview

```bash
top
```
or
```bash
htop
```

Look for:
- High `%CPU` or `%MEM` usage
- Processes in `D` (uninterruptible sleep) or `R` (running) state
- The `load average` values at the top

## 🧠 Step 3: Identify CPU Bottlenecks

Use `mpstat` to get per-core CPU usage:
```bash
mpstat -P ALL 1 5
```

Check if:
- One core is overused while others are idle
- `%idle` is consistently low (indicating CPU saturation)

## 💾 Step 4: Investigate Disk I/O

Use `iostat` or `iotop`:
```bash
iostat -xz 1 5
```
or
```bash
iotop -o
```

Look for:
- High I/O wait times
- Processes causing excessive disk reads/writes

## 📡 Step 5: Check for Memory Pressure

```bash
free -m
```

Also:
```bash
vmstat 1 5
```

Watch for:
- Low free memory and high swap usage
- High `si`/`so` (swap in/out) in `vmstat`

## 🌐 Step 6: Inspect Network Activity

Check network stats:
```bash
iftop
```
or
```bash
nload
```

You can also use:
```bash
ss -tulnp
```
To find open sockets and connected services.

## 🧰 Step 7: Trace Problem Processes

Use:
```bash
pidstat -p ALL 1 5
```
or
```bash
strace -p <PID>
```

To trace system calls made by high-load processes.

## 🚨 Step 8: Check for Potential DoS Attacks

High load may be caused by a Denial-of-Service (DoS) attack, especially if the system is exposed to the internet.

Check for high connection counts:
```bash
netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -n
```
or using `ss`:
```bash
ss -ntu | awk '{print $6}' | cut -d: -f1 | sort | uniq -c | sort -nr | head
```

If a single IP is making thousands of connections, it could be malicious.

### 🛡️ Block Suspicious IPs (Temporarily)

You can block an IP using `iptables`:
```bash
sudo iptables -A INPUT -s <suspicious-ip> -j DROP
```

Or with `ufw` (if enabled):
```bash
sudo ufw deny from <suspicious-ip>
```

For persistent blocking across reboots, consider fail2ban or adding the rule to your firewall config.

---


## 📌 Final Tips

- Set up monitoring (e.g., `Netdata`, `Glances`, `Prometheus + Grafana`)
- Always correlate symptoms with recent changes or logs
- Document every step for future reference

---

By following this structured approach, you'll not only fix the current high load but also improve your ability to proactively manage server performance.

