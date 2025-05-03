---
title: "Why Is My Cron Job Not Running? Debugging Silent Failures"
date: 2025-05-02
description: "A practical guide to diagnosing and fixing silent cron job failures on Linux."
tags: ["cron", "linux", "scheduling", "debugging", "sysadmin"]
---

# Why Is My Cron Job Not Running? Debugging Silent Failures

Cron is a powerful scheduling utility on Unix-like systems, but it can be frustratingly silent when things go wrong. If your cron jobs are not executing as expected and you're not seeing any errors, you're not alone. This guide walks you through practical steps to debug and resolve common cron issues.

---

## ✅ Step 1: Check the Cron Syntax

Cron jobs use a specific syntax:

```
* * * * * /path/to/command
```

Each asterisk represents:

1. Minute
2. Hour
3. Day of month
4. Month
5. Day of week

🔍 **Common Pitfall**: Writing the command in a format that works in an interactive shell but not in cron.

---

## ✅ Step 2: Use Full Paths to Everything

Cron runs in a minimal environment. Use absolute paths for commands and scripts.

**Bad:**

```bash
python3 script.py
```

**Good:**

```bash
/usr/bin/python3 /home/user/scripts/script.py
```

---

## ✅ Step 3: Redirect Output

Cron jobs fail silently unless output is captured.

```bash
* * * * * /path/to/script.sh >> /var/log/myscript.log 2>&1
```

This sends both standard output and errors to a log file.

---

## ✅ Step 4: Environment Variables Are Not Set

Cron doesn't load your profile (`~/.bashrc`, `~/.profile`, etc.).

If your script depends on environment variables or PATH, define them in the script:

```bash
#!/bin/bash
export PATH=/usr/bin:/bin:/usr/local/bin
export ENV_VAR=value
```

---

## ✅ Step 5: Check Cron Logs

### 🔍 System Log

- Debian/Ubuntu: `/var/log/syslog`
- RedHat/CentOS: `/var/log/cron`

```bash
grep CRON /var/log/syslog
```

### 🔍 User-specific logs

Some systems use `/var/log/cron.log` or `journalctl -u cron`.

---

## ✅ Step 6: File Permissions and Executability

Make sure your script is:

- Executable: `chmod +x script.sh`
- Owned by the right user
- Located in a readable/executable path

---

## ✅ Step 7: Cron Daemon Running?

Check that the cron service is active:

```bash
sudo systemctl status cron
```

---

## ✅ Step 8: Special Characters in the Script

If your script contains `%`, escape it as `\%` in cron or move the logic to an external script.

---

## ✅ Step 9: Debug with a Dummy Job

Add a test job to confirm cron is executing:

```bash
* * * * * echo "Cron is alive at $(date)" >> /tmp/cron_test.log
```

---

## 🔚 Conclusion

Cron jobs can be frustrating when they fail silently, but systematic debugging usually reveals the issue. Use logging, check environments, and always test your cron setup with known-good jobs before going live.

---

**Happy debugging!** 🛠️ If this helped you, consider sharing it with fellow sysadmins and SREs.

