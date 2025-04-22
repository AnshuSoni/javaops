---
title: "What Happens When You Type a Command in Bash?"
date: 2025-04-22
description: "Explore the inner workings of Bash and understand the journey a command takes from your keyboard to execution."
tags: ["bash", "linux", "shell", "cli", "operating system"]
---
Today, I am writing one my favourite interview question about linux terminal, I have asked it for more than 5+ years of experience people, and trust me only two people could answer with confidence.

So, I am asking you, what happens when you enter a command in your favourite terminal, `bash` 😊


When you type a command into the Bash shell and press Enter, it might seem like a simple action. But under the hood, a complex series of steps are set in motion. Let's break down what really happens when you type a command like `ls -l` into Bash.

## 1. **Keyboard Input and Line Editing**

Bash uses the GNU Readline library to provide an interactive input experience. As you type, the characters aren't sent directly to the kernel; instead, they are buffered by Readline, which allows for features like command history, editing shortcuts, and autocompletion.

## 2. **Parsing and Tokenization**

Once you press Enter, Bash reads the full command line and begins parsing it. This involves:
- **Tokenization**: Splitting the command string into tokens (e.g., `ls`, `-l`).
- **Variable/Substitution Expansion**: Replacing variables (`$HOME`), command substitutions (`$(...)`), and more.
- **Quoting Rules**: Managing single and double quotes to group tokens correctly.

## 3. **Built-in or External Command?**

Bash checks if the command is a built-in (like `cd` or `echo`). If not, it looks for the executable by searching through the directories listed in the `$PATH` environment variable.

```bash
echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

It searches each directory in order until it finds an executable file named `ls`.

## 4. **Forking a Process**

For external commands, Bash uses the `fork()` system call to create a new child process. The child process is an almost exact copy of the Bash shell.

## 5. **Executing the Command**

In the child process, the `execve()` system call is used to replace the process image with the command you want to run (`/bin/ls` in this case). This is where your command actually begins execution.

## 6. **Waiting and Collecting Exit Status**

The parent Bash process waits for the child to complete using `wait()`. Once the command finishes, Bash collects the exit status. You can view the last exit status using:

```bash
echo $?
```

## 7. **Displaying Output**

If the command writes to standard output or standard error, the terminal displays the results directly. Bash itself doesn’t handle output redirection unless you explicitly use it (e.g., `ls -l > output.txt`).

---

### Summary Flow:

```mermaid
graph TD;
    A[User types command] --> B[Readline processes input];
    B --> C[Bash parses tokens];
    C --> D{Built-in?};
    D -- Yes --> E[Execute built-in];
    D -- No --> F[Search $PATH];
    F --> G[Found binary];
    G --> H[Fork child process];
    H --> I[Child execve() command];
    I --> J[Command runs];
    J --> K[Parent waits];
    K --> L[Exit status collected];
    L --> M[Output displayed];
```

Understanding these steps not only demystifies how Bash works but also empowers you to troubleshoot and optimize your shell usage more effectively. Next time you type a command, you'll know the intricate ballet happening behind the scenes.
