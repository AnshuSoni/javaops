---
title: "How DNS Works Internally: Primary/Secondary Servers, Delegation, Poisoning, and Common Attacks"
date: 2025-04-28T10:30:00Z
description: "Explore the internal workings of DNS, the roles of primary and secondary servers, DNS delegation, poisoning, common DNS attacks, and utilities to debug DNS issues."
tags: [DNS, Networking, Security]
categories: [Networking, Security]
---

The Domain Name System (DNS) is the internet's phonebook, translating domain names like `example.com` into IP addresses like `93.184.216.34`. Under the hood, DNS is a complex and critical part of how the internet functions.

In this post, we'll cover how DNS works internally, the role of primary and secondary servers, DNS delegation, DNS poisoning, common DNS attacks, and introduce common Linux utilities to debug DNS.

## How DNS Works Internally

When you type a URL into your browser, a multi-step process unfolds:

1. **Local Cache Check**: Your system first checks its DNS cache.
2. **Recursive Query**: If not cached, a DNS resolver (usually operated by your ISP) is queried.
3. **Root Servers**: The resolver queries a root DNS server to find the Top-Level Domain (TLD) server (e.g., `.com`).
4. **TLD Servers**: The TLD server points to the authoritative server for the domain.
5. **Authoritative Server**: The authoritative DNS server provides the actual IP address.

The resolver caches the result and returns it to your device.

## Primary and Secondary DNS Servers

**Primary DNS Server**:
- The master server where zone files are created and updated.
- It's the authoritative source for the domain's DNS information.

**Secondary DNS Server**:
- Holds a read-only copy of the primary's zone file.
- Updates itself by zone transfers (AXFR/IXFR protocols).
- Provides redundancy — if the primary fails, the secondary can still serve DNS queries.

**Best Practice**: Always have at least one secondary server for high availability and resilience.

## DNS Delegation

Delegation is when a DNS server hands off responsibility for a subdomain to another DNS server.

Example:
- `example.com` could delegate `blog.example.com` to a separate DNS server.

This delegation is achieved by configuring NS (Name Server) records pointing to the delegated server.

**Benefits**:
- Scalability: Different teams can manage subdomains independently.
- Performance: Specialized servers can optimize for different services.

## DNS Poisoning (Cache Poisoning)

DNS poisoning tricks a DNS resolver into caching incorrect information, redirecting users to malicious sites.

### How it Happens:
- An attacker floods a resolver with forged responses.
- If the resolver accepts a fake response before a legitimate one, it caches and uses the malicious IP.

**Impact**:
- Users are unknowingly redirected to phishing or malware sites.

**Defenses**:
- DNSSEC (DNS Security Extensions)
- Using trusted DNS resolvers
- Implementing query ID randomization

## Common DNS Attacks

1. **DNS Amplification Attack**:
   - Type of DDoS attack.
   - Exploits open DNS resolvers to flood a target with massive traffic.

2. **Domain Hijacking**:
   - Attackers gain control over a domain's registration.
   - Changes DNS settings to reroute traffic or steal sensitive information.

3. **Subdomain Takeover**:
   - Happens when a subdomain points to an external service that is no longer claimed.
   - Attackers claim the abandoned resource to control the subdomain.

4. **DNS Tunneling**:
   - Exfiltration of data through DNS queries and responses.
   - Bypasses traditional security measures.

## Common Linux Utilities to Test and Debug DNS Servers

Several utilities are essential for troubleshooting DNS issues:

### host
A simple command-line utility to perform DNS lookups.

Example:
```bash
host example.com
```
- Returns the A record (IP address) for the domain.

### dig
Powerful tool to query DNS servers directly.

Example:
```bash
dig example.com
```
- Returns detailed information including query time, server response, and DNS record sections.

Query a specific DNS server:
```bash
dig @8.8.8.8 example.com
```

### nslookup
Older tool, still widely used for querying DNS.

Example:
```bash
nslookup example.com
```

Specify a DNS server:
```bash
nslookup example.com 8.8.8.8
```

### systemd-resolve
Modern utility in systemd-based systems.

Example:
```bash
systemd-resolve example.com
```
- Useful for debugging local system resolver issues.

### tcpdump
Useful for capturing DNS traffic to debug complex issues.

Example:
```bash
tcpdump -n port 53
```
- Captures DNS query and response packets.

## Final Thoughts

DNS is often invisible to end-users but is foundational to the internet's functionality and security. Understanding its internal workings, how servers are organized, how attacks are executed, and how to debug issues is crucial for system administrators and security professionals.

> **Tip**: Always monitor DNS configurations, apply best practices for redundancy, and ensure DNSSEC is in place where possible.

---

Stay tuned for deeper dives into advanced DNS topics like Anycast DNS, DNS over HTTPS (DoH), and DNS over TLS (DoT)!
