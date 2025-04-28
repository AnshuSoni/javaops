---
title: "Quick Intro to iostat, vmstat, and netstat for Performance Checks"
date: 2025-04-28T10:00:00Z
description: "Learn how to quickly use iostat, vmstat, and netstat to perform basic system performance checks."
tags: [Linux, Performance Monitoring, SysAdmin]
categories: [DevOps, SysAdmin]
---

System performance checks are crucial for troubleshooting and maintaining healthy servers. Three classic and lightweight tools for quickly assessing Linux system performance are `iostat`, `vmstat`, and `netstat`. In this post, we'll take a brief look at what they do and how to use them.

## iostat

`iostat` (Input/Output Statistics) provides information about CPU utilization and I/O statistics for devices and partitions.

### Install
```bash
sudo apt install sysstat  # Debian/Ubuntu
sudo yum install sysstat  # CentOS/RHEL
```

### Basic Usage
```bash
iostat -xz 1 5
```
- `-x`: Extended statistics
- `-z`: Suppress output for devices with zero activity
- `1 5`: Report every 1 second, 5 times

**Useful Metrics**:
- `%iowait`: High values mean your CPU is often waiting on disk I/O.
- `r/s`, `w/s`: Read and write operations per second.

---

## vmstat

`vmstat` (Virtual Memory Statistics) provides a snapshot of system resource usage including memory, swap, processes, and CPU activity.

### Basic Usage
```bash
vmstat 1 5
```
- Shows reports every 1 second for 5 times.

**Key Columns**:
- `r`: Processes waiting to run.
- `free`: Amount of free memory.
- `si`/`so`: Swap in/out activity.
- `us`, `sy`, `id`, `wa`: CPU usage split between user, system, idle, and wait times.

> Tip: Consistently high `wa` (wait) values may indicate I/O bottlenecks.

---

## netstat

`netstat` (Network Statistics) displays networking information such as active connections, routing tables, and interface statistics.

> **Note**: `netstat` is deprecated on some systems in favor of `ss`, but it's still widely used.

### Basic Usage
```bash
netstat -tulnp
```
- `-t`: TCP connections
- `-u`: UDP connections
- `-l`: Listening sockets
- `-n`: Numeric addresses
- `-p`: Show process IDs and names

**Quick Checks**:
- Identify open ports and associated services.
- Spot unexpected network listeners.

### Alternative (ss)
```bash
ss -tulnp
```

---

## Final Thoughts

While there are advanced tools like `htop`, `atop`, and full observability stacks, `iostat`, `vmstat`, and `netstat` are lightweight, available by default (or easily installable), and perfect for quick health checks, especially on production servers where installing extra software may not be desirable.

Start using them today to get a better feel for your system's health!
