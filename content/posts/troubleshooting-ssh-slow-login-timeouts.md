---
title: "Troubleshooting SSH Timeouts, Slow Login, and Authentication Failures"
date: 2025-05-01
description: A practical guide to diagnosing and fixing common SSH connection issues on Linux servers, including timeouts, slow logins, and authentication errors.
tags: ["Linux", "SSH", "Troubleshooting", "DevOps", "Security"]
---

Secure Shell (SSH) is the lifeline for remote Linux system administration, but it can occasionally frustrate with timeouts, painfully slow logins, or cryptic authentication failures. This post explores the **most common causes** and **practical fixes** for SSH connectivity issues.

---

## 🔍 1. Diagnosing SSH Timeouts

**Symptom:** SSH hangs for a long time and then fails or eventually connects.

### ✅ Check Network Reachability
```bash
ping <hostname>
traceroute <hostname>
telnet <hostname> 22
```

- If port 22 is blocked by a firewall or dropped silently, SSH will hang.
- Use `nmap -p 22 <host>` to verify SSH port status.

### 🔥 Check Firewall Rules
- On server:
```bash
sudo iptables -L -n
sudo ufw status
```

- On cloud instances (AWS, Azure, etc.): check **security groups or firewall rules**.

---

## 🐢 2. Troubleshooting Slow SSH Logins

**Symptom:** SSH connects, but login takes 5–30 seconds before showing the shell.

### 🔎 Common Culprits

#### a) **Reverse DNS Lookups**
SSH may try to resolve the client's IP via reverse DNS.

- **Fix:** Edit the server's `sshd_config`:
```bash
sudo nano /etc/ssh/sshd_config
```
Add or change:
```bash
UseDNS no
```
Then restart SSH:
```bash
sudo systemctl restart sshd
```

#### b) **GSSAPI Authentication**
GSSAPI is often enabled by default but rarely used.

- Disable it in `sshd_config`:
```bash
GSSAPIAuthentication no
```

- Also disable it in your SSH client config (optional):
```bash
Host *
  GSSAPIAuthentication no
```

#### c) **Authentication Delay from PAM or LDAP**
If using external authentication (like LDAP), failures or timeouts here can delay logins.

- Inspect:
```bash
sudo journalctl -xe
cat /var/log/auth.log
```

- You might see entries like `pam_ldap: ldap_search() timed out`.

---

## ❌ 3. Debugging Authentication Failures

**Symptom:** You get `Permission denied`, `Authentication failed`, or `Too many authentication failures`.

### 🧪 Enable SSH Client Debug
```bash
ssh -vvv user@host
```

Look for lines like:
```
debug1: Offering public key: ~/.ssh/id_rsa
debug1: Authentications that can continue: publickey,password
```

### ✅ Common Fixes

#### a) **Wrong Permissions on `.ssh` Files**
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

#### b) **Too Many SSH Keys Offered**
Some servers reject connections after too many failed key attempts.

- Use:
```bash
ssh -o IdentitiesOnly=yes -i ~/.ssh/my_key user@host
```

#### c) **Server Doesn't Accept Your Key**
- Make sure your **public key** (`~/.ssh/id_rsa.pub`) is in the server’s:
```
~/.ssh/authorized_keys
```

- And ownership is correct:
```bash
chown -R user:user ~/.ssh
```

---

## 🛡️ 4. SSHD Logs Are Your Friend

Check logs on the **server side**:
```bash
sudo journalctl -u sshd
tail -f /var/log/auth.log
```

Look for:
- Authentication errors
- Key mismatch
- PAM rejections
- Reverse DNS failures

---

## 💡 Pro Tip: Enable SSH Logging in Detail

In `/etc/ssh/sshd_config`:
```bash
LogLevel VERBOSE
```
This logs key fingerprints and helps debug why a key might be rejected.

---

## 🧰 Final Tips

- Restart SSH after changes:
```bash
sudo systemctl restart sshd
```

- Always keep a **backup session open** when testing configs.
- Use `ssh-copy-id` to easily push keys:
```bash
ssh-copy-id user@host
```

---

## ✅ Summary

| Problem                     | Fix                                                                 |
|----------------------------|----------------------------------------------------------------------|
| Timeout on connect         | Check firewalls, DNS, and port 22                                    |
| Slow login                 | Disable UseDNS, GSSAPI, and debug PAM                                 |
| Auth fails with public key | Fix permissions, key paths, and use `ssh -vvv`                        |
| Login hangs on cloud VM    | Review metadata, reverse DNS, or cloud-agent conflicts                |

---

Let me know in the comments if you've run into weird SSH issues — or if you found a clever fix not listed here!
