# Linux Process Management

## 1. What Is a Process?

A **process** is a program that is currently running on a computer. Each process has its own Process ID (PID), which the operating system uses to identify and manage it.

Examples of processes include:

* A web browser running in the background.
* An Nginx web server.
* A Python script.
* A system monitoring service.
* A scheduled backup task.

**Process management** is the process of monitoring, controlling, starting, and stopping running programs.

## 2. Program vs Process

| Program                                | Process                             |
| -------------------------------------- | ----------------------------------- |
| A set of instructions stored in a file | A running instance of a program     |
| Usually exists on disk                 | Uses system resources while running |
| Does not necessarily execute           | Is actively managed by the OS       |

Example:

```bash
python3 script.py
```

The Python file is a program. When it runs, the operating system creates a process for it.

## 3. What Is a PID?

PID stands for **Process ID**.

Linux assigns a unique process identifier to each running process at a given time.

To view processes:

```bash
ps
```

To display processes associated with the current terminal:

```bash
ps -f
```

Example output:

```text
UID       PID  PPID  C STIME TTY          TIME CMD
komal    2410  2300  0 10:20 pts/0    00:00:00 python3 script.py
```

Important fields:

* `UID`: User running the process.
* `PID`: Process ID.
* `PPID`: Parent Process ID.
* `C`: CPU utilization indicator in this output.
* `STIME`: Start time.
* `TTY`: Associated terminal.
* `TIME`: Accumulated CPU time.
* `CMD`: Command used to start the process.

*Example output is illustrative; actual values vary.*

## 4. What Is a PPID?

PPID stands for **Parent Process ID**.

A process may create another process, making the original process its parent.

For example:

```text
Terminal
   |
   └── Shell
        |
        └── Python Script
```

The shell may launch the Python script. The Python process has the shell's PID as its PPID.

View process relationships:

```bash
ps -ef
```

Display processes in a tree:

```bash
pstree
```

If `pstree` is unavailable, install the appropriate package for your distribution.

## 5. Viewing Running Processes with `ps`

The `ps` command displays a snapshot of process information.

### Basic command

```bash
ps
```

### Show all processes in a full-format listing

```bash
ps -ef
```

### Show processes in BSD-style format

```bash
ps aux
```

### Search for a particular process

```bash
ps aux | grep nginx
```

This searches the output for lines containing `nginx`. It may also match the `grep` command itself, so the result is not always proof that Nginx is running.

For a more targeted check:

```bash
pgrep -a nginx
```

No output usually means no matching process was found.

## 6. Monitoring Processes with `top`

The `top` command displays processes and system resource usage in real time.

```bash
top
```

It commonly shows:

* CPU usage.
* Memory usage.
* Load averages.
* Running tasks.
* Process IDs.
* Process owners.
* Process states.

Useful interactive keys include:

| Key | Action                    |
| --- | ------------------------- |
| `P` | Sort by CPU usage         |
| `M` | Sort by memory usage      |
| `k` | Request to kill a process |
| `q` | Quit                      |

The exact behavior may vary slightly by version.

**Cybersecurity use case:** If a system becomes slow, use `top` to identify processes consuming unusually high CPU or memory.

## 7. Using `htop`

`htop` is an interactive process viewer with a more visual interface than `top`.

Run:

```bash
htop
```

If it is not installed, install it through your distribution's package manager.

For Debian-based distributions:

```bash
sudo apt update
sudo apt install htop
```

It helps you inspect CPU usage, memory consumption, process trees, and process ownership.

## 8. Checking CPU and Memory Usage

### Using `top`

```bash
top
```

### Using `ps`

```bash
ps aux --sort=-%cpu | head
```

Displays processes with the highest CPU percentages near the top.

To sort by memory percentage:

```bash
ps aux --sort=-%mem | head
```

### Using `free`

```bash
free -h
```

Displays memory usage in a human-readable format.

### Using `uptime`

```bash
uptime
```

Displays how long the system has been running and its load averages.

**Important:** High load average does not automatically mean high CPU utilization. It can also reflect tasks waiting for resources, including certain I/O operations.

## 9. Finding a Process by Name

### Using `pgrep`

```bash
pgrep nginx
```

Displays the PIDs of matching processes.

To display both PID and command:

```bash
pgrep -a nginx
```

### Using `pidof`

```bash
pidof nginx
```

Displays PIDs associated with the named program when recognized by the tool.

### Using `ps` and `grep`

```bash
ps -ef | grep nginx
```

These commands help locate processes during troubleshooting.

## 10. Stopping a Process with `kill`

The `kill` command sends a signal to a process. It does not always immediately terminate it.

Syntax:

```bash
kill PID
```

Example:

```bash
kill 2410
```

By default, `kill` sends `SIGTERM`, which asks the process to terminate gracefully.

Use the actual PID from your system rather than copying example PIDs.

### Common Signals

| Signal    |                           Number | Purpose                                                    |
| --------- | -------------------------------: | ---------------------------------------------------------- |
| `SIGHUP`  |                                1 | Often used to request a reload or indicate terminal hangup |
| `SIGINT`  |                                2 | Interrupt a process                                        |
| `SIGKILL` |                                9 | Force termination                                          |
| `SIGTERM` |                               15 | Request graceful termination                               |
| `SIGSTOP` | 19 on common Linux architectures | Stop a process                                             |

Signal numbers can vary across platforms. Signal names are generally more portable.

### Graceful termination

```bash
kill -TERM PID
```

### Force termination

```bash
kill -KILL PID
```

**Important:** Use `SIGKILL` only when necessary. The process cannot catch or handle it to perform normal cleanup.

Before terminating a process, verify its identity and impact.

## 11. Difference Between `kill` and `kill -9`

| `kill PID`                               | `kill -9 PID`                             |
| ---------------------------------------- | ----------------------------------------- |
| Sends `SIGTERM` by default               | Sends `SIGKILL`                           |
| Allows graceful cleanup                  | Forces termination                        |
| Process may handle or ignore the request | Process cannot catch or ignore the signal |
| Preferred first attempt                  | Last resort when appropriate              |

Interview answer:

"`kill` normally sends SIGTERM, which requests graceful termination. `kill -9` sends SIGKILL, which forces the process to stop without allowing normal cleanup."

## 12. Using `pkill` and `killall`

### `pkill`

Sends a signal to processes selected by a pattern.

```bash
pkill -TERM -x testscript
```

This targets processes whose process name exactly matches `testscript`, subject to permissions and the tool's matching rules.

### `killall`

On many Linux distributions, `killall` sends a signal to processes by name.

```bash
killall testscript
```

Be careful: multiple processes may share the same name.

**Best practice:** Inspect the matching processes before sending signals, particularly on production systems.

## 13. Foreground and Background Processes

### Foreground Process

A foreground process occupies the terminal while it runs.

Example:

```bash
ping 127.0.0.1
```

Stop the command using:

```text
Ctrl + C
```

This normally sends an interrupt signal.

### Background Process

A command can be started in the background by adding `&`.

```bash
sleep 300 &
```

The shell typically prints a job number and PID.

Check shell jobs:

```bash
jobs
```

Bring a job to the foreground:

```bash
fg %1
```

The job number may differ on your system.

## 14. Using `nohup`

`nohup` runs a command so that it ignores hangup signals, which can help it continue after the terminal closes.

Example:

```bash
nohup python3 script.py > output.log 2>&1 &
```

Explanation:

* `nohup`: Ignores the hangup signal.
* `python3 script.py`: Runs the script.
* `> output.log`: Redirects standard output to a log file.
* `2>&1`: Redirects standard error to the same destination.
* `&`: Starts the command in the background.

`nohup` does not provide full service supervision. For long-running production services, a service manager such as `systemd` is generally more appropriate.

## 15. Process States in Linux

Processes can exist in different states.

| State | Meaning                                         |
| ----- | ----------------------------------------------- |
| `R`   | Running or runnable                             |
| `S`   | Interruptible sleep                             |
| `D`   | Uninterruptible sleep, often waiting for I/O    |
| `T`   | Stopped or traced                               |
| `Z`   | Zombie process                                  |
| `I`   | Idle kernel thread in relevant process listings |

Check process states:

```bash
ps -eo pid,ppid,state,comm
```

The displayed state can include additional modifier characters.

## 16. What Is a Zombie Process?

A zombie is a process that has finished executing but still has an entry in the process table because its parent has not yet collected its exit status.

Example:

```text
PID   PPID  S  COMMAND
2410  2300  Z  testscript
```

A zombie is not actively executing and generally does not consume CPU like a running process.

Investigate its parent:

```bash
ps -o pid,ppid,state,cmd -p 2410
```

Then inspect the parent:

```bash
ps -fp 2300
```

Replace the example PIDs with actual values.

A zombie usually needs its parent to collect its exit status. Killing the zombie itself is not the normal solution.

## 17. What Is an Orphan Process?

An orphan process is a process whose original parent has exited while the child continues running.

On Linux, such a process is generally adopted by an appropriate system process or subreaper.

Orphan processes are not inherently malicious. Investigate them in context if their behavior or origin is unexpected.

## 18. Managing Processes with `systemctl`

Many Linux distributions use `systemd` to manage system services.

### Check a service

```bash
systemctl status nginx
```

### Start a service

```bash
sudo systemctl start nginx
```

### Stop a service

```bash
sudo systemctl stop nginx
```

### Restart a service

```bash
sudo systemctl restart nginx
```

### Enable a service at boot

```bash
sudo systemctl enable nginx
```

### Disable automatic startup

```bash
sudo systemctl disable nginx
```

A process is a running instance of a program; a service is a managed unit that may start, stop, and supervise one or more processes.

## 19. Investigating a Suspicious Process

If a process looks unusual, avoid terminating it immediately without understanding its role.

### Step 1: Identify the Process

```bash
ps -ef
```

### Step 2: Check Its Executable Path

```bash
readlink -f /proc/PID/exe
```

Replace `PID` with the actual process ID.

### Step 3: Inspect Its Command Line

```bash
tr '\0' ' ' < /proc/PID/cmdline
```

### Step 4: Inspect Its Owner and Resource Usage

```bash
ps -o pid,ppid,user,%cpu,%mem,etime,cmd -p PID
```

### Step 5: Inspect Open Files

```bash
sudo lsof -p PID
```

`lsof` may need to be installed. Access to process details can be restricted by permissions and system configuration.

### Step 6: Review Relevant Logs

Check appropriate system and application logs for errors or unexpected activity.

**Security note:** An unfamiliar process name alone does not prove that a process is malicious. Consider its executable path, owner, command line, parent process, network activity, and expected system behavior.

## 20. Practical Lab: Create and Monitor a Process

Use your own Linux VM for this exercise.

### Step 1: Start a Test Process

```bash
sleep 300 &
```

### Step 2: Find Its PID

```bash
pgrep -a sleep
```

### Step 3: Inspect It

```bash
ps -ef | grep '[s]leep'
```

### Step 4: Monitor System Processes

```bash
top
```

### Step 5: Stop the Test Process Gracefully

Use the PID returned by `pgrep`:

```bash
kill -TERM PID
```

### Step 6: Verify It Has Stopped

```bash
pgrep -a sleep
```

If other `sleep` processes are running, inspect the output to determine whether your specific test process has exited.

## 21. Scenario-Based Troubleshooting

### Scenario 1: The System Is Slow

**Problem:** A Linux server is responding slowly.

**Approach:**

```bash
uptime
top
free -h
ps aux --sort=-%cpu | head
ps aux --sort=-%mem | head
```

Check CPU consumption, memory pressure, load averages, and the processes using the most resources before deciding on a fix.

### Scenario 2: A Process Is Not Responding

**Approach:**

```bash
pgrep -a process_name
ps -fp PID
kill -TERM PID
```

Allow time for graceful termination. Consider `SIGKILL` only if the process remains stuck and forced termination is appropriate.

### Scenario 3: Nginx Is Not Running

**Approach:**

```bash
systemctl status nginx
ps -ef | grep '[n]ginx'
```

If authorized, review service logs and attempt to start the service if the cause is understood:

```bash
sudo systemctl start nginx
```

### Scenario 4: A Process Is Consuming Too Much Memory

**Approach:**

```bash
free -h
ps aux --sort=-%mem | head
```

Identify the process, check its logs and workload, and determine whether the behavior is expected before restarting or terminating it.

### Scenario 5: A Suspicious Process Appears

**Approach:**

```bash
ps -fp PID
readlink -f /proc/PID/exe
tr '\0' ' ' < /proc/PID/cmdline
```

Collect relevant evidence and follow your incident-response procedure rather than assuming every unfamiliar process is malicious.

## 22. Important Interview Questions

**Q1. What is a process?**

A process is a running instance of a program that the operating system manages and assigns resources to.

**Q2. What is a PID?**

A PID is the Process ID used to identify a process.

**Q3. What is a PPID?**

A PPID is the Process ID of a process's parent.

**Q4. What is the difference between `ps` and `top`?**

`ps` provides a snapshot of process information, while `top` provides an interactive, continuously refreshed view of processes and system resource usage.

**Q5. How do you find a process using high CPU?**

```bash
top
ps aux --sort=-%cpu | head
```

**Q6. What is the difference between SIGTERM and SIGKILL?**

SIGTERM requests graceful termination; SIGKILL forces termination and cannot be handled by the target process.

**Q7. What is a zombie process?**

A zombie is a process that has exited but whose parent has not yet collected its exit status.

**Q8. What is a background process?**

A background process runs without occupying the terminal as the active foreground job.

**Q9. What is the difference between a process and a service?**

A process is a running program instance. A service is a managed system function, often controlled by a service manager such as `systemd`.

**Q10. How would you investigate a suspicious process?**

I would check its PID, owner, parent process, executable path, command line, resource usage, open files, and relevant logs before deciding on a response.

## 23. Quick Revision

```text
ps             → Snapshot of process information
top            → Live process monitoring
htop           → Interactive process viewer
pgrep          → Find process IDs by pattern
pidof          → Find PIDs associated with a program
kill           → Send a signal to a process
pkill          → Send a signal to matching processes
jobs           → View current shell jobs
fg             → Bring a job to the foreground
nohup          → Run a command while ignoring hangup signals
free -h        → View memory usage
uptime         → View uptime and load averages
systemctl      → Manage systemd units
```

## 24. Summary

Process management helps administrators understand what is running, diagnose resource problems, and control applications and services.

For cybersecurity, focus on:

* PID and PPID.
* CPU and memory monitoring.
* Process states.
* Signals and safe termination.
* Foreground and background jobs.
* Service management with `systemctl`.
* Investigating suspicious processes.
* Troubleshooting high resource consumption.

**Key takeaway:** Before stopping a process, identify what it does, who owns it, and what impact stopping it could have.
