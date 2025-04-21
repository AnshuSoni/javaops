---
title: "Init Systems in Containers: Why systemd Isn’t Always There"
date: 2025-04-18
draft: false
description: "Why most Docker containers don’t run systemd and what that means for process management inside containers."
tags: ["init", "systemd", "linux", "containers", "docker"]
categories: ["Linux", "Containers", "DevOps"]
---

When working with containers, especially Docker, one of the first surprises Linux users encounter is the absence of `systemd`, the popular init system used in most modern distributions.

From my personal experience, we have spend 2 sleepless nights debugging an issue around this, so the questions is 

Why is it missing, and should you even care? Let’s break it down.

---

## What Is an Init System?

In traditional Linux systems, the init system is the **first process (PID 1)** that starts during boot. Its job is to:

- Initialize the system
- Launch background services (daemons)
- Handle process reaping (cleaning up zombies)

Popular init systems include:

- `systemd` (most modern distros)
- `sysvinit` (older distros)
- `OpenRC`, `runit`, etc.

---

## Why Containers Don’t Need `systemd`

Containers are **not virtual machines** — they don’t boot like a full Linux OS. Instead:

- A container runs a single process, specified at startup
- The container's lifecycle is tied to this process
- There’s no need to manage multiple services internally

Because of this, there's **no need for an init system** like `systemd` in most containers.

---

## So What’s PID 1 in a Container?

The process you define in your Dockerfile using `CMD` or `ENTRYPOINT` becomes PID 1:

```dockerfile
FROM ubuntu
CMD ["python3", "app.py"]
```

Here, `python3 app.py` becomes PID 1 inside the container.

However, PID 1 in Linux behaves *differently* — it doesn’t handle signals the same way as other processes, and it’s responsible for reaping zombie processes.

---

## Problems Without an Init System

- **Zombie Processes:** If your application spawns child processes and doesn’t reap them, they become zombies.
- **Signal Handling:** PID 1 ignores some signals by default (e.g., SIGTERM), which can cause shutdown issues.

---

## Solutions

### 1. **Use a Minimal Init Process**

Use tools like [`tini`](https://github.com/krallin/tini):

```dockerfile
FROM node:alpine
ENTRYPOINT ["/sbin/tini", "--"]
CMD ["node", "server.js"]
```

Tini acts as a minimal init system that handles signal forwarding and zombie reaping.

### 2. **Use base images with systemd (if really needed)**

If your use case *requires* `systemd` (e.g., running multiple services), you can:

- Use `centos/systemd` or `ubuntu:systemd` base images
- Run Docker containers with `--privileged` and `--cgroupns=host`
- Accept the added complexity and size

```bash
docker run --privileged --cgroupns=host -v /sys/fs/cgroup:/sys/fs/cgroup:ro my-systemd-image
```

But in general, **this is discouraged unless absolutely necessary**.

---

## Best Practices

- Design containers to run a single process
- Use supervisors (like `tini`, `dumb-init`, or `s6`) if needed
- Let orchestration tools (like Kubernetes) manage service lifecycles

---

## Final Thoughts

Containers aren't VMs, and they shouldn’t be treated as such. The absence of `systemd` is by design, not a limitation. By understanding the role of PID 1 and how to manage it properly, you’ll write better, more efficient containers.

---

Have you ever needed `systemd` in a container? What did you do about it?  
Let me know in the comments or on [LinkedIn](https://linkedin.com/in/anshusoni)!
