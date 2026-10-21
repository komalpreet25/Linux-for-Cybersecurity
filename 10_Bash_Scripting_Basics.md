
# 10. Bash Scripting Basics

## Description

Bash scripting is the process of writing a sequence of commands in a file so that the Bash shell can execute them automatically. It helps Linux administrators and cybersecurity professionals automate repetitive tasks, inspect system information, monitor services, analyze logs, and perform routine security checks.

**Goal:** Learn Bash scripting from beginner to practical cybersecurity automation.

---

## 1. What Is Bash?

### Professional Definition

Bash (Bourne Again Shell) is a Unix shell and command-language interpreter that executes commands, supports scripting constructs, and enables automation of operating-system tasks.

### Simple Meaning

Normally, we type Linux commands one by one in the terminal. With Bash scripting, we save multiple commands in a file and run them together.

### Example

Without a script:

```bash
date
whoami
hostname
uptime
```

With a script:

```bash
#!/usr/bin/env bash

date
whoami
hostname
uptime
```

Save the script as `system_info.sh` and run it whenever needed.

### Why Is Bash Important in Cybersecurity?

- Automating routine security checks.
- Collecting system and network information.
- Checking file permissions.
- Monitoring disk space and services.
- Searching logs for suspicious activity.
- Detecting repeated failed-login events.
- Automating backups and scheduled tasks.

**Important:** Bash can automate defensive security tasks, but the results must still be verified. A script does not automatically make an investigation accurate or a system secure.

---

## 2. Understanding How a Bash Script Works

A typical Bash script follows this process:

1. A user creates a script file.
2. The first line may specify the interpreter.
3. Bash reads and interprets the commands.
4. Commands run in sequence, subject to conditions and loops.
5. The script returns an exit status.

Example:

```bash
#!/usr/bin/env bash

echo "Starting system check"
date
echo "System check completed"
```

Output example:

```text
Starting system check
Wed Oct 21 12:00:00 UTC 2026
System check completed
```

The date and time shown above are illustrative.

### What Is a Shebang?

The shebang is the first line of a script that starts with `#!`. It identifies the interpreter used to execute the script.

```bash
#!/usr/bin/env bash
```

This asks the environment to locate Bash using the `PATH` variable.

Another common form is:

```bash
#!/bin/bash
```

Both are valid when their specified interpreter is available.

---

## 3. Creating and Running Your First Script

### Step 1: Create a File

```bash
nano hello.sh
```

Enter:

```bash
#!/usr/bin/env bash

echo "Hello, Linux!"
echo "Learning Bash for Cybersecurity"
```

Save and exit Nano.

### Step 2: Make the Script Executable

```bash
chmod +x hello.sh
```

The `+x` adds execute permission.

### Step 3: Execute the Script

```bash
./hello.sh
```

Alternatively:

```bash
bash hello.sh
```

The second method runs the script through Bash without requiring the file itself to have execute permission.

### Step 4: Verify Permissions

```bash
ls -l hello.sh
```

Example output:

```text
-rwxr-xr-x 1 user user 78 Oct 21 12:00 hello.sh
```

Permission characters depend on your actual file permissions.

**Troubleshooting:** If `./hello.sh` returns `Permission denied`, check file permissions and whether the directory or filesystem allows execution. You can also try `bash hello.sh` to distinguish an execution-permission problem from a script problem.

---

## 4. Essential Bash Commands

| Command | Purpose |
|---|---|
| `echo` | Display text |
| `printf` | Format output |
| `read` | Accept user input |
| `date` | Display date and time |
| `whoami` | Display the current username |
| `hostname` | Display the system hostname |
| `pwd` | Display the current directory |
| `id` | Display user and group IDs |
| `uname` | Display system information |
| `df` | Display filesystem disk usage |
| `free` | Display memory usage |
| `ps` | Display process information |
| `grep` | Search text using patterns |
| `test` | Evaluate a condition |
| `exit` | End the script with a status code |

---

## 5. Variables in Bash

### Definition

A variable is a named value that a script can store and use later.

Example:

```bash
#!/usr/bin/env bash

name="Komal"
topic="Cybersecurity"

echo "Name: $name"
echo "Topic: $topic"
```

Output:

```text
Name: Komal
Topic: Cybersecurity
```

### Important Rules

1. Do not put spaces around `=` when assigning a value.
2. Use `$variable` to expand a variable.
3. Prefer double quotes around variable expansions.
4. Use `${variable}` when the variable name needs a clear boundary.

Correct:

```bash
username="admin"
echo "$username"
```

Incorrect:

```bash
username = "admin"
```

The incorrect form attempts to execute a command named `username`.

### Double Quotes vs Single Quotes

```bash
name="Linux"

echo "Learning $name"
echo 'Learning $name'
```

Output:

```text
Learning Linux
Learning $name
```

Double quotes allow variable expansion. Single quotes preserve the literal text.

### Command Substitution

Use `$(...)` to capture the output of a command.

```bash
current_user=$(whoami)
current_date=$(date)

echo "User: $current_user"
echo "Date: $current_date"
```

Command substitution is useful when scripts need to collect system information dynamically.

---

## 6. Taking User Input

The `read` command accepts input from the user.

```bash
#!/usr/bin/env bash

read -r -p "Enter your username: " username

echo "Welcome, $username"
```

Example:

```text
Enter your username: analyst
Welcome, analyst
```

### Why Use `-r`?

The `-r` option prevents backslashes from being interpreted as escape characters while reading input.

### Hidden Input

For sensitive interactive input:

```bash
read -r -s -p "Enter password: " password
printf '\n'
```

The `-s` option suppresses terminal echo. This does not encrypt the password or make storing it in a variable inherently secure. Avoid logging secrets or exposing them in command-line arguments.

---

## 7. Positional Arguments

Scripts can accept arguments when they are executed.

Create `greet.sh`:

```bash
#!/usr/bin/env bash

echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "Number of arguments: $#"
```

Run:

```bash
bash greet.sh Komal Linux
```

Output:

```text
Script name: greet.sh
First argument: Komal
Second argument: Linux
Number of arguments: 2
```

### Important Special Parameters

| Parameter | Meaning |
|---|---|
| `$0` | Script name or invocation path |
| `$1`, `$2` | First and second positional arguments |
| `$#` | Number of positional arguments |
| `"$@"` | All positional arguments, preserving each argument separately |
| `$?` | Exit status of the last command |
| `$$` | Process ID of the current Bash shell |
| `$!` | Process ID of the most recently started background job |

### Practical Example: System Information Script

Create `system_info.sh`:

```bash
#!/usr/bin/env bash

echo "User: $(whoami)"
echo "Hostname: $(hostname)"
echo "Current directory: $(pwd)"
echo "Kernel information:"
uname -r
```

Run:

```bash
bash system_info.sh
```

This is a simple starting point for an automated system inventory.

---

## 8. Conditional Statements: if, elif, else

Conditional statements allow a script to make decisions.

### Basic Syntax

```bash
if condition; then
    # Commands when the condition is true
elif another_condition; then
    # Commands when the second condition is true
else
    # Commands when no condition is true
fi
```

### Example: Check Whether a File Exists

```bash
#!/usr/bin/env bash

file="system_info.sh"

if [[ -f "$file" ]]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

### Common File Tests

| Test | Meaning |
|---|---|
| `-e` | Path exists |
| `-f` | Regular file exists |
| `-d` | Directory exists |
| `-r` | File is readable |
| `-w` | File is writable |
| `-x` | File is executable |

Example:

```bash
if [[ -d "/etc" ]]; then
    echo "/etc directory exists"
fi
```

### Numeric Comparisons

```bash
#!/usr/bin/env bash

read -r -p "Enter a number: " number

if [[ "$number" -gt 10 ]]; then
    echo "Greater than 10"
elif [[ "$number" -eq 10 ]]; then
    echo "Equal to 10"
else
    echo "Less than 10"
fi
```

This example expects a valid integer. For production scripts, validate input before performing numeric comparisons.

| Operator | Meaning |
|---|---|
| `-eq` | Equal |
| `-ne` | Not equal |
| `-gt` | Greater than |
| `-ge` | Greater than or equal |
| `-lt` | Less than |
| `-le` | Less than or equal |

### String Comparisons

```bash
if [[ "$username" == "admin" ]]; then
    echo "Username matches admin"
fi
```

This only compares text. It does not prove that the account has administrator privileges.

---

## 9. Loops in Bash

Loops repeat commands without requiring you to write the same command repeatedly.

### A. For Loop

```bash
#!/usr/bin/env bash

for item in Linux Networking Bash; do
    echo "Learning: $item"
done
```

Output:

```text
Learning: Linux
Learning: Networking
Learning: Bash
```

### B. While Loop

A `while` loop runs as long as its condition remains true.

```bash
#!/usr/bin/env bash

count=1

while [[ "$count" -le 5 ]]; do
    echo "Count: $count"
    ((count += 1))
done
```

Output:

```text
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
```

### C. Loop Through Files

```bash
#!/usr/bin/env bash

for file in ./*.txt; do
    [[ -f "$file" ]] || continue
    printf 'Found file: %s\n' "$file"
done
```

The `continue` statement skips the current iteration when the pattern has no matching regular file.

### D. Break and Continue

- `break` exits the current loop.
- `continue` skips the remainder of the current iteration.

Use loops to process multiple log files, check services, or inspect a list of approved hosts.

---

## 10. Functions in Bash

### Definition

A function is a reusable block of commands that can be called by name.

Example:

```bash
#!/usr/bin/env bash

show_system_info() {
    echo "Current user: $(whoami)"
    echo "Hostname: $(hostname)"
    echo "Kernel: $(uname -r)"
}

show_system_info
```

### Function with an Argument

```bash
#!/usr/bin/env bash

greet() {
    local name="$1"
    printf 'Hello, %s!\n' "$name"
}

greet "Komal"
greet "Security Analyst"
```

`local` limits the variable's scope to the function.

### Why Functions Matter

- Reduce duplicated code.
- Make scripts easier to maintain.
- Separate tasks into logical units.
- Simplify testing and troubleshooting.

---

## 11. Exit Status and Error Handling

Every command returns an exit status.

- `0` generally means success.
- A nonzero status indicates an error or another unsuccessful outcome.

Example:

```bash
#!/usr/bin/env bash

grep -q "root" /etc/passwd

if [[ "$?" -eq 0 ]]; then
    echo "Match found"
else
    echo "No match or an error occurred"
fi
```

A better approach is often to use the command directly in the condition:

```bash
if grep -q "root" /etc/passwd; then
    echo "Match found"
else
    echo "No match or an error occurred"
fi
```

Note that `grep` commonly returns `1` when no match exists and `2` for an error. For important automation, distinguish these cases when necessary.

### Understanding `$?`

```bash
true
echo "$?"
```

Output:

```text
0
```

Now run:

```bash
false
echo "$?"
```

Output:

```text
1
```

`$?` must be checked immediately when you need the status of a particular command because the next command overwrites it.

### Useful Error-Handling Options

```bash
set -euo pipefail
```

These options mean:

- `-e`: Exit on many unhandled command failures.
- `-u`: Treat unset variables as errors.
- `-o pipefail`: A pipeline fails if a command within it fails, rather than relying only on the last command's status.

**Important:** These options have exceptions and subtle behavior, especially inside conditionals, functions, and pipelines. They are not a substitute for deliberate error handling and validation.

---

## 12. Input and Output Redirection

Redirection controls where command output and errors go.

| Symbol | Purpose |
|---|---|
| `>` | Write standard output, replacing existing file contents |
| `>>` | Append standard output |
| `<` | Read standard input from a file |
| `2>` | Redirect standard error |
| `2>>` | Append standard error |
| `&>` | Redirect standard output and standard error in Bash |
| `\|` | Pass one command's standard output to another command |

### Example: Save System Information

```bash
uname -a > system_report.txt
date >> system_report.txt
whoami >> system_report.txt
```

### Example: Separate Errors

```bash
find /etc -name "*.conf" > found_files.txt 2> errors.txt
```

- `found_files.txt` stores matching paths.
- `errors.txt` stores error messages.

Permissions and system configuration determine which files can be accessed.

### Example: Search a Log

```bash
grep -i "failed" /var/log/auth.log
```

The log location depends on the Linux distribution and logging configuration. Some systems use the systemd journal instead.

---

## 13. Cybersecurity Practical: System Health Check

### Scenario

You are a junior security analyst responsible for checking a Linux workstation before beginning an investigation. You want a repeatable report containing basic system details and resource usage.

Create `health_check.sh`:

```bash
#!/usr/bin/env bash

set -u

echo "===== SYSTEM HEALTH CHECK ====="
echo "Date: $(date)"
echo "User: $(whoami)"
echo "Hostname: $(hostname)"
echo "Kernel: $(uname -r)"

echo
echo "===== DISK USAGE ====="
df -h

echo
echo "===== MEMORY USAGE ====="
free -h

echo
echo "===== UPTIME ====="
uptime

echo
echo "===== CHECK COMPLETED ====="
```

Run:

```bash
chmod +x health_check.sh
./health_check.sh
```

To save the report:

```bash
./health_check.sh > health_report.txt 2>&1
```

### What You Learn

- Collecting basic host information.
- Checking disk and memory utilization.
- Using command substitution.
- Combining multiple commands in a single script.
- Saving output for later review.

**Security note:** A health report is not a complete security audit. It does not establish whether the system is free from compromise.

---

## 14. Cybersecurity Practical: Check File Permissions

### Scenario

You are reviewing a directory of security scripts and want to identify regular files that are executable by everyone.

Create `permission_audit.sh`:

```bash
#!/usr/bin/env bash

set -u

target_dir="${1:-.}"

if [[ ! -d "$target_dir" ]]; then
    echo "Error: Directory does not exist: $target_dir" >&2
    exit 1
fi

echo "Reviewing regular files in: $target_dir"

find "$target_dir" -type f -perm -0001 -print
```

Run it against a directory you own or are authorized to audit:

```bash
chmod +x permission_audit.sh
./permission_audit.sh .
```

Or:

```bash
./permission_audit.sh /path/to/approved/directory
```

### Understanding the Permission Test

`-perm -0001` checks for the *other-execute* permission bit. The script reports matching regular files, but it does not automatically change their permissions.

Do not blindly remove execute permissions. Some files require them to function correctly, and permission changes should follow an informed review.

---

## 15. Cybersecurity Practical: Detect Failed-Login Patterns

### Scenario

A security analyst needs to search an authorized authentication log for failed-login messages.

Create `failed_login_search.sh`:

```bash
#!/usr/bin/env bash

set -u

log_file="${1:-/var/log/auth.log}"

if [[ ! -r "$log_file" ]]; then
    echo "Error: Cannot read log file: $log_file" >&2
    echo "Check the path and your authorized access." >&2
    exit 1
fi

echo "Searching: $log_file"

if grep -Ein 'failed password|authentication failure|invalid user' "$log_file"; then
    echo "Search completed. Review the matching events."
else
    status=$?

    if [[ "$status" -eq 1 ]]; then
        echo "No matching events found."
    else
        echo "Error while searching the log." >&2
        exit "$status"
    fi
fi
```

Run:

```bash
chmod +x failed_login_search.sh
./failed_login_search.sh
```

Or specify a log file:

```bash
./failed_login_search.sh /path/to/authorized/auth.log
```

### What Does This Script Do?

1. Selects a log file.
2. Checks whether the file is readable.
3. Searches for several common authentication-failure phrases.
4. Reports matching lines.
5. Handles the difference between no matches and a search error.

### Important Limitations

- The default path is common on Debian-based systems but may not exist on your system.
- Some systems store authentication events in the systemd journal.
- Log formats vary between distributions and services.
- Matching text is only an investigative lead, not proof of an attack.
- A complete brute-force investigation needs timestamps, source addresses, account names, event frequency, and contextual verification.

Never attempt to access logs or systems without authorization.

---

## 16. Scheduling a Script with Cron

Cron schedules commands to run automatically.

First, test your script manually. Then open your personal crontab:

```bash
crontab -e
```

Example entry to run a script every day at 8:00 AM:

```cron
0 8 * * * /home/user/health_check.sh >> /home/user/health_check.log 2>&1
```

Replace `/home/user/` with the actual absolute path to your script and log file.

### Cron Field Order

```text
minute hour day-of-month month day-of-week
```

For example:

```cron
0 8 * * *
```

means minute `0`, hour `8`, every day of the month, every month, and every day of the week.

### Cron Best Practices

- Use absolute paths.
- Set appropriate file permissions.
- Log useful output and errors.
- Avoid embedding passwords or API secrets in scripts.
- Test scheduled tasks under the intended account.
- Monitor logs to confirm that jobs actually run.

---

## 17. Bash Scripting Best Practices

1. **Quote variables:** Prefer `"$variable"` to avoid unintended word splitting and pathname expansion.
2. **Validate inputs:** Check arguments and user-supplied values before using them.
3. **Use meaningful names:** Names such as `log_file` are clearer than `x`.
4. **Check errors:** Do not assume every command succeeds.
5. **Use absolute paths in automation:** Cron and other scheduled environments may have a different working directory or `PATH`.
6. **Avoid unnecessary root privileges:** Run scripts with the minimum permissions required.
7. **Protect sensitive information:** Never print passwords, tokens, or private keys into logs.
8. **Test in a lab:** Validate scripts using test files and disposable virtual machines.
9. **Use shell quoting carefully:** Especially when filenames contain spaces or special characters.
10. **Review destructive operations:** Never run commands such as `rm -rf` on variable-derived paths without strict validation and testing.
11. **Use version control:** Track changes to scripts with Git.
12. **Add comments:** Explain complex logic and assumptions.

### Common Mistakes

| Mistake | Better approach |
|---|---|
| `name = "Linux"` | `name="Linux"` |
| `echo $filename` | `echo "$filename"` |
| Forgetting the shebang | Add an appropriate interpreter line |
| Forgetting execute permission | Use `chmod +x script.sh` when needed |
| Ignoring exit statuses | Check command results and handle errors |
| Hard-coding a relative path in cron | Use an absolute path |
| Assuming all Linux systems use the same log file | Check the distribution and logging configuration |

---

## 18. Troubleshooting Bash Scripts

### Error: `Permission denied`

Check:

```bash
ls -l script.sh
```

If appropriate:

```bash
chmod +x script.sh
```

Or execute with:

```bash
bash script.sh
```

### Error: `command not found`

Check spelling and command availability:

```bash
command -v bash
command -v grep
```

A scheduled environment may also have a different `PATH`.

### Error: `bad interpreter`

Inspect the first line:

```bash
head -n 1 script.sh
```

Check that the specified interpreter exists. Files edited on Windows may contain CRLF line endings, which can also cause interpreter errors.

### Debugging a Script

Run:

```bash
bash -x script.sh
```

The `-x` option prints commands as Bash executes them. Be careful: debugging output can expose sensitive variable values.

For syntax checking without executing the script:

```bash
bash -n script.sh
```

---

## 19. Practical Lab Challenges

Complete these exercises in your Linux virtual machine.

### Beginner

- [ ] Create a script that prints your username, hostname, and current date.
- [ ] Accept a username using `read`.
- [ ] Pass your name as a positional argument.
- [ ] Check whether a specified file exists.

### Intermediate

- [ ] Write a script that checks disk usage and saves a report.
- [ ] Use a loop to print numbers from 1 to 10.
- [ ] Write a function that displays basic system information.
- [ ] Create a script that checks whether a specified directory exists.
- [ ] Use redirection to separate standard output from standard error.

### Cybersecurity-Focused

- [ ] Create a read-only script that audits executable permissions in a test directory.
- [ ] Search a sample authentication log for failed-login patterns.
- [ ] Write a script that checks whether a known service is active using `systemctl is-active`.
- [ ] Schedule a health-check report using your personal crontab.
- [ ] Add argument validation and useful error messages to one of your scripts.

For every challenge, test normal input, missing files, permission errors, and unexpected input.

---

## 20. Interview Questions and Answers

### Q1. What is Bash scripting?

Bash scripting is the practice of writing commands and control structures in a file that the Bash interpreter executes to automate tasks.

### Q2. What is the difference between Bash and a shell?

A shell is a command interpreter and user interface to operating-system services. Bash is one particular shell with features such as variables, functions, conditionals, loops, and command-line editing.

### Q3. What is a shebang?

A shebang is a line beginning with `#!` that identifies the interpreter used to execute a script.

### Q4. What is the difference between `$1` and `$#`?

`$1` contains the first positional argument, while `$#` contains the number of positional arguments.

### Q5. What is the difference between `>` and `>>`?

`>` redirects output and replaces existing file contents. `>>` appends output to the end of a file.

### Q6. What is an exit status?

An exit status is a numeric result returned by a command or script. Zero conventionally indicates success, while a nonzero value indicates failure or another unsuccessful outcome.

### Q7. What is the purpose of `chmod +x`?

It adds execute permission according to the file's existing permissions and the applicable system rules.

### Q8. Why should variables be quoted?

Quoting generally prevents unintended word splitting and pathname expansion, making scripts more reliable when values contain spaces or wildcard characters.

### Q9. What is the difference between a `for` loop and a `while` loop?

A `for` loop iterates over a list or sequence. A `while` loop continues executing as long as its condition evaluates to true.

### Q10. How can Bash help a security analyst?

Bash can automate log searches, collect system information, check permissions, monitor services, and prepare repeatable reports.

### Q11. How would you troubleshoot a failing script?

I would reproduce the issue, inspect the error message, verify permissions and paths, run `bash -n` for syntax checking, and use `bash -x` when necessary while protecting sensitive information.

### Q12. Is Bash suitable for every cybersecurity task?

No. Bash is effective for command-line automation and system administration, but complex data processing, larger applications, and some security workflows may be better suited to Python or specialized tools.

---

## 21. Tricky Interview Questions

**Q1. Does `chmod +x` mean that only the owner can execute a file?**

No. It adds execute permission for applicable classes according to the existing permissions and umask rules. Inspect the resulting permissions with `ls -l`.

**Q2. Does a zero exit status always prove that a security operation succeeded completely?**

No. It means the command reported success according to its own semantics. Additional verification may be necessary.

**Q3. Is `set -e` sufficient for reliable error handling?**

No. Its behavior has exceptions, and it does not validate inputs or verify that a task achieved its intended result.

**Q4. Does finding a failed-login message prove a brute-force attack?**

No. It could represent a mistyped password, an outdated service configuration, or malicious activity. Correlate multiple events and investigate their context.

**Q5. Why might a script work in a terminal but fail in cron?**

Cron may use a different environment, working directory, `PATH`, permissions, and shell configuration. Use absolute paths and capture errors in logs.

---

## 22. Revision Notes

- **Bash:** A command-language interpreter and shell.
- **Shebang:** Specifies the script interpreter.
- **Variable:** Stores a value for later use.
- **`read`:** Accepts input.
- **`$1`:** First positional argument.
- **`$#`:** Number of positional arguments.
- **`if`:** Executes logic based on a condition.
- **`for` / `while`:** Repeat commands.
- **Function:** Reusable block of commands.
- **`$?`:** Exit status of the most recent command.
- **`>` / `>>`:** Replace output / append output.
- **`2>`:** Redirect standard error.
- **`chmod +x`:** Add execute permission.
- **`bash -n`:** Check syntax without executing the script.
- **`bash -x`:** Trace commands during execution.
- **Cron:** Schedule commands or scripts.

---

## Summary

Bash scripting turns individual Linux commands into repeatable automation. A cybersecurity learner should understand variables, arguments, conditionals, loops, functions, exit statuses, redirection, permissions, and error handling before building larger scripts.

The best way to learn is to write small scripts, test them in a controlled Linux environment, understand their output, and gradually combine the techniques into defensive security automation.

**Next file:** `11_Process_Management.md`
