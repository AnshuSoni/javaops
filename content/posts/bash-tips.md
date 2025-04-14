---
title: "10 Bash Scripting Tips for Everyday DevOps"
date: 2025-04-13
tags: ["bash", "linux", "scripting", "devops"]
---

Bash scripting is a powerful skill for any developer or DevOps engineer. Whether you're automating deployments, setting up environments, or writing health checks, here are **10 useful Bash tips and tricks** to level up your scripting game.

---

## 1️⃣ Set `-e` and `-u` for Safer Scripts

```bash
#!/bin/bash
set -eu
```

- -e: Exit on error
- -u: Treat unset variables as errors

> Use this at the top of your scripts for better safety.

## 2️⃣ Use `trap` for Cleanup

```bash
trap 'echo "Cleaning up..."; rm -f /tmp/mytempfile' EXIT
```
This ensures your script does cleanup even if interrupted.

## 3️⃣ Check for Root Privileges

```bash
if [[ $EUID -ne 0 ]]; then
  echo "Please run as root"
  exit 1
fi
```
Useful for install or system-modifying scripts.

## 4️⃣ Parse Arguments with getopts

```bash
while getopts "u:p:" opt; do
  case $opt in
    u) USERNAME=$OPTARG ;;
    p) PASSWORD=$OPTARG ;;
  esac
done
```

Great for CLI-style tools with `-u` user `-p` pass style flags. For example, `superscript -u <username> -p ******`

## 5️⃣ Loop Over Files Safely

```bash
find . -type f -name "*.log" | while IFS= read -r file; do
  echo "Processing $file"
done
```

Avoid for f in * to handle filenames with spaces or special characters.

## 6️⃣  Use Heredocs for Multiline Output

```bash
cat <<EOF > /etc/myconfig.conf
[Service]
ExecStart=/usr/bin/myapp
Restart=always
EOF
```

Perfect for config files or templating.

## 7️⃣ Inline Function for Logging

```bash
log() {
  echo "[LOG $(date +'%F %T')] $*"
}

log "Script started"
```

Cleaner logs, better debugging!, I usually follow this practices , it helps me to write more cleaner code.

## 8️⃣ Check if Command Exists

```bash
if ! command -v docker &>/dev/null; then
  echo "Docker not found!"
  exit 1
fi
```

Useful in scripts that depend on system binaries.

## 9️⃣ Background Tasks with & and wait

```bash
./heavy-task.sh & 
./another-task.sh & 
wait
echo "All tasks done!"
```

Parallelize long-running tasks to speed things up. This is almost same as writing `Async` in `Spring framework`, I will cover more about this topic.

## 🔟 Use `mktemp` for Safe Temp Files

```bash
TMPFILE=$(mktemp)
echo "Doing something" > "$TMPFILE"
```

Avoid naming collisions and handle temp data safely.

## 1️⃣1️⃣ Find files larger than 100 MB

```bash
# to see in current directory
find . -type f -size +100M -exec ls -lh {} \;

# to see in root
find / -type f -size +100M -exec ls -lh {} \;

# to see in /path/to/dir
find /path/to/dir -type f -size +100M -exec ls -lh {} \;

```


