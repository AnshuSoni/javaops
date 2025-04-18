---
title: "Using tmux to Manage Persistent SSH Sessions: Which One Is Better — screen or tmux?"
date: 2025-04-18
draft: false
description: "Explore the advantages of using tmux over screen for managing persistent SSH sessions, and learn how to use tmux effectively."
tags: ["tmux", "screen", "ssh", "terminal", "linux", "devops"]
categories: ["Linux", "Productivity", "Tools"]
---

When working on remote servers over SSH, there’s always that dreaded moment when your connection drops, taking all your terminal sessions with it. Enter terminal multiplexers like `screen` and `tmux`, which let you keep your sessions alive even after disconnection.

But which one should you choose? Let’s dive into why `tmux` is the better tool for most developers and sysadmins, and how to get started with it.

---

## Why You Need Persistent Sessions

Imagine this:

- You SSH into a server
- Start a long-running job or open multiple terminal panes
- Then your internet hiccups… and poof! Everything’s gone

Using a terminal multiplexer like `tmux` or `screen`, you can:

- Detach from sessions and reconnect later
- Keep jobs running even when you disconnect
- Create multiple windows and panes within a single terminal

## `screen` vs `tmux`: A Quick Comparison

| Feature                  | `screen`             | `tmux`               |
|--------------------------|----------------------|----------------------|
| Actively Maintained      | No (very minimal)     | Yes (actively)       |
| Configurability          | Limited               | Highly configurable  |
| Scripting API            | Primitive             | Powerful              |
| User Interface           | Basic                 | Modern, colored       |
| Split Panes              | Yes (but clunky)      | Yes (intuitive)       |
| Learning Curve           | Steeper               | Easier                |

**Verdict:**  
While `screen` is a classic and still widely used, `tmux` offers a more modern, customizable, and user-friendly experience. If you're starting today, go with `tmux`.

---

## Getting Started with `tmux`

### Install `tmux`

On Debian/Ubuntu:

```bash
sudo apt install tmux
```

On RHEL/CentOS:

```bash
sudo yum install tmux
```

### Basic Commands

- Start a new session:

  ```bash
  tmux
  ```

- Start with a session name:

  ```bash
  tmux new -s mysession
  ```

- Detach from a session:

  Press `Ctrl + b`, then `d`

- Reattach to a session:

  ```bash
  tmux attach -t mysession
  ```

- List sessions:

  ```bash
  tmux ls
  ```

### Split Panes

- Horizontal split: `Ctrl + b`, then `"`
- Vertical split: `Ctrl + b`, then `%`

Navigate between panes with `Ctrl + b` followed by arrow keys.

---

## tmux Tips for Power Users

- Customize your `~/.tmux.conf`:

  ```bash
  set -g mouse on
  set -g history-limit 10000
  bind r source-file ~/.tmux.conf \; display-message "Config Reloaded!"
  ```

- Use plugins like [tmux plugin manager (TPM)](https://github.com/tmux-plugins/tpm) for better features.
- Combine `tmux` with `ssh` and `mosh` for even more resilience.

---

## Final Thoughts

If you’re still using `screen` today, it might be time to consider switching to `tmux`. With its superior features, active development, and vibrant community, `tmux` is hands-down the better tool for managing persistent SSH sessions.

---

**What do you use — `screen` or `tmux`?**  
Drop your thoughts in the comments. Happy hacking!
