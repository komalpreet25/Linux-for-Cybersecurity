
# 11. Cron Jobs in Linux

## Description

Cron is a time-based job scheduler used in Unix-like operating systems to execute commands or scripts automatically at specified times and intervals.

For Linux administrators and cybersecurity professionals, cron is important for automating routine maintenance, scheduling security checks, collecting system information, rotating logs, and monitoring recurring tasks.

**Learning objectives:**

- Understand cron, crontab, and the cron daemon.
- Learn cron scheduling syntax.
- Create, inspect, edit, and remove scheduled jobs.
- Automate Linux administration and defensive security tasks.
- Investigate suspicious scheduled tasks during security audits.
- Troubleshoot failed or unexpected cron executions.
- Practice cron administration in a controlled Linux environment.

---

## 1. What Is Cron?

### Professional Definition

Cron is a time-based job scheduling mechanism that executes commands or scripts according to schedules defined in cron tables.

### Simple Explanation

Normally, a user manually runs a command whenever it is needed. Cron allows Linux to execute that command automatically at a configured time.

For example, cron can be configured to:

- Run a system health check every morning.
- Generate a daily disk-usage report.
- Search authentication logs periodically.
- Perform routine maintenance.
- Execute an authorized backup script.
- Check whether an important service is running.

### Example

Suppose you want to run a system information script every day at 8:00 AM.

Instead of manually executing:

```bash
bash /home/user/system_info.sh
```

you can schedule the script using cron.

**Important:** Cron schedules execution. It does not guarantee that a job will succeed. Permissions, environment variables, system availability, and script errors can affect the outcome.

---

## 2. Understanding Cron Architecture

Cron-based scheduling commonly involves three components.

### A. Cron Daemon

The cron daemon is a background service that checks schedules and starts commands when they are due.

On many Linux systems, the service is named `cron` or `crond`.

### B. Crontab

A crontab is a table containing scheduled commands and their execution times.

Users can have individual crontabs, while system-wide scheduling can also be configured through system crontabs and directories.

### C. Scheduled Command or Script

This is the command, program, or script that cron executes.

Example:

```bash
/usr/local/bin/security_check.sh
```

### Execution Flow

```text
Cron Schedule
     |
     v
Cron Daemon
     |
     v
Scheduled Command
     |
     v
Output and Exit Status
     |
     v
Logs and Verification
```

The exact logging and notification behavior depends on the system's cron implementation and configuration.

---

## 3. Cron vs Crontab vs systemd Timers

| Feature | Cron | Crontab | systemd Timer |
|---|---|---|---|
| Purpose | Time-based job scheduling | Stores cron schedules | Schedules systemd units |
| Configuration | Depends on cron implementation | Contains scheduled entries | Uses timer and service units |
| Typical use | Recurring commands | Define when commands run | Schedule services and commands |
| Missed-run handling | Usually does not automatically catch up | Depends on the cron implementation | Can support catch-up using `Persistent=true` for suitable calendar timers |
| Dependencies | Basic scheduling | Depends on cron | Can integrate with systemd dependencies |

### When Should You Use Cron?

Cron is useful for straightforward recurring tasks, such as daily reports, routine scripts, and scheduled maintenance.

### When Should You Use systemd Timers?

Systemd timers can be preferable when you need service dependencies, structured logging through the journal, or configurable behavior after a system has been offline.

**Note:** Cron implementations differ. Some distributions also provide tools such as `anacron` for jobs that should be run after missed schedules on systems that are not always powered on.

---

## 4. Check Whether Cron Is Installed and Running

### Check the Service Status

On Debian- and Ubuntu-based systems:

```bash
systemctl status cron
```

On systems that use `crond`:

```bash
systemctl status crond
```

The correct service name depends on your distribution.

### Check Whether Cron Exists

```bash
command -v crontab
```

### Check the Cron Process

```bash
pgrep -a cron
```

If your distribution uses `crond`, try:

```bash
pgrep -a crond
```

### Start the Service

On a system using the `cron` service:

```bash
sudo systemctl start cron
```

On a system using `crond`:

```bash
sudo systemctl start crond
```

### Enable the Service at Boot

For `cron`:

```bash
sudo systemctl enable cron
```

For `crond`:

```bash
sudo systemctl enable crond
```

Use the service name appropriate for your system.

**Security note:** Starting or enabling a system service changes system state. Check local administrative policy before making changes, particularly on production servers.

---

## 5. Understanding Crontab

The `crontab` command manages a user's scheduled jobs.

### Display Your Current Crontab

```bash
crontab -l
```

If no crontab exists for your user, the command may report that no crontab is installed.

### Edit Your Crontab

```bash
crontab -e
```

This opens the current user's crontab in the configured editor.

### Remove Your Crontab

```bash
crontab -r
```

**Warning:** This removes the current user's entire crontab. It does not merely remove one selected job. Back up and review the crontab before using this command.

### Back Up Your Crontab

```bash
crontab -l > "$HOME/crontab_backup.txt"
```

Check the backup:

```bash
cat "$HOME/crontab_backup.txt"
```

If you later need to restore it, inspect the file first and then use:

```bash
crontab "$HOME/crontab_backup.txt"
```

Only restore a trusted backup that you have reviewed.

### Manage Another User's Crontab

Administrators may inspect another user's crontab:

```bash
sudo crontab -u username -l
```

Replace `username` with the intended account.

Administrative privileges may be required. Use this capability only when authorized.

---

## 6. Understanding Cron Syntax

A standard user crontab entry has five time fields followed by a command.

```text
MINUTE HOUR DAY_OF_MONTH MONTH DAY_OF_WEEK COMMAND
```

### Five Scheduling Fields

| Field | Allowed values | Meaning |
|---|---|---|
| Minute | `0-59` | Minute of the hour |
| Hour | `0-23` | Hour of the day |
| Day of month | `1-31` | Calendar day |
| Month | `1-12` | Month |
| Day of week | `0-7` | Sunday is commonly `0` or `7` |

The interpretation of schedules involving both day-of-month and day-of-week can vary by implementation. In traditional cron, when both are restricted rather than set to `*`, matching either field can cause execution.

### Example

```cron
0 8 * * * /home/user/health_check.sh
```

Meaning:

- Minute: `0`
- Hour: `8`
- Day of month: every day
- Month: every month
- Day of week: every day
- Command: run `health_check.sh`

This schedules the script for 8:00 AM according to the cron daemon's applicable time zone and scheduling rules.

---

## 7. Important Cron Operators

### A. Asterisk (`*`)

Represents every permitted value for a field.

```cron
* * * * * /home/user/check.sh
```

Runs every minute.

### B. Comma (`,`)

Specifies multiple values.

```cron
0 8,16 * * * /home/user/report.sh
```

Runs at 8:00 AM and 4:00 PM daily.

### C. Hyphen (`-`)

Specifies a range.

```cron
0 9 * * 1-5 /home/user/weekday_report.sh
```

Runs at 9:00 AM Monday through Friday on traditional cron implementations.

### D. Slash (`/`)

Specifies a step interval.

```cron
*/15 * * * * /home/user/check.sh
```

Runs every 15 minutes.

### E. Combined Values

```cron
0 9-17 * * 1-5 /home/user/business_hours_check.sh
```

Runs at the top of each hour from 9:00 AM through 5:00 PM, Monday through Friday.

### Important Note

Cron expressions are not universal across every scheduler. Features such as seconds fields, special characters, and day-of-week handling depend on the implementation.

---

## 8. Common Cron Scheduling Examples

| Schedule | Cron expression | Meaning |
|---|---|---|
| Every minute | `* * * * *` | Every minute |
| Every 5 minutes | `*/5 * * * *` | Every five minutes |
| Every 15 minutes | `*/15 * * * *` | Every fifteen minutes |
| Every hour | `0 * * * *` | At the start of every hour |
| Every day at midnight | `0 0 * * *` | Daily at 12:00 AM |
| Every day at 8 AM | `0 8 * * *` | Daily at 8:00 AM |
| Every day at 11:30 PM | `30 23 * * *` | Daily at 11:30 PM |
| Every Sunday at 2 AM | `0 2 * * 0` | Sunday at 2:00 AM |
| First day of every month | `0 0 1 * *` | Midnight on the first |
| Weekdays at 9 AM | `0 9 * * 1-5` | Monday through Friday |
| Every 30 minutes | `*/30 * * * *` | At minute 0 and 30 |

These are examples for traditional five-field cron syntax.

### Practice

Before installing a cron expression, explain each of its five fields in plain language. This helps prevent incorrect schedules.

---

## 9. Special Cron Strings

Some cron implementations support convenient special strings.

| String | Meaning |
|---|---|
| `@reboot` | Run when the cron daemon or system starts, depending on implementation |
| `@hourly` | Run hourly |
| `@daily` | Run daily |
| `@weekly` | Run weekly |
| `@monthly` | Run monthly |
| `@yearly` | Run yearly |

Example:

```cron
@daily /home/user/daily_report.sh
```

Schedules the script to run daily.

Example:

```cron
@reboot /home/user/startup_check.sh
```

Schedules the command for a startup event supported by the implementation.

**Important:** `@reboot` does not necessarily mean that a job runs after every user login. Cron implementations and service behavior determine when it runs.

---

## 10. Environment Variables in Cron

Cron jobs often execute with a smaller environment than an interactive terminal.

A command that works in your terminal may fail in cron because:

- The `PATH` variable is different.
- The working directory is different.
- Shell startup files are not loaded in the same way.
- Required environment variables are missing.
- The scheduled user lacks required permissions.

### Inspect Your PATH

```bash
echo "$PATH"
```

### Set PATH in Your Crontab

```cron
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

Use a path suitable for your system and commands.

### Use Absolute Paths

Prefer:

```cron
0 8 * * * /home/user/health_check.sh
```

Instead of relying on:

```cron
0 8 * * * health_check.sh
```

### Specify an Interpreter When Needed

For a Bash-specific script:

```cron
0 8 * * * /usr/bin/bash /home/user/health_check.sh
```

Check the Bash path on your system:

```bash
command -v bash
```

### Set a Working Directory Explicitly

```cron
0 8 * * * cd /home/user/project && /usr/bin/bash ./run_check.sh
```

An even better approach is to use absolute paths throughout the script when practical.

---

## 11. Redirect Cron Output and Errors

By default, cron implementations may send job output through a local mail mechanism if configured. Many minimal Linux installations do not have local mail delivery configured.

Explicitly redirect output when you need reliable troubleshooting.

### Save Standard Output

```cron
0 8 * * * /home/user/health_check.sh > /home/user/health_check.log
```

This overwrites the log each time the job runs.

### Append Standard Output

```cron
0 8 * * * /home/user/health_check.sh >> /home/user/health_check.log
```

This preserves earlier output and appends new output.

### Capture Standard Output and Errors

```cron
0 8 * * * /home/user/health_check.sh >> /home/user/health_check.log 2>&1
```

Here:

- `>>` appends standard output.
- `2>&1` sends standard error to the same destination as standard output.

### Keep Separate Logs

```cron
0 8 * * * /home/user/health_check.sh >> /home/user/output.log 2>> /home/user/error.log
```

This separates normal output from error messages.

### Security Considerations

- Restrict access to logs containing sensitive information.
- Avoid writing passwords, tokens, or private keys into logs.
- Prevent unbounded log growth through an appropriate retention policy.
- Choose a writable log location with appropriate ownership and permissions.
- Remember that redirected logs may contain attacker-controlled text.

---

## 12. Practical Lab: Create Your First Cron Job

### Objective

Schedule a harmless script that records the date and hostname.

### Step 1: Create a Lab Directory

```bash
mkdir -p "$HOME/cron_lab"
```

### Step 2: Create a Script

```bash
nano "$HOME/cron_lab/hello_cron.sh"
```

Add:

```bash
#!/usr/bin/env bash

set -u

echo "Cron job executed"
echo "Timestamp: $(date)"
echo "Hostname: $(hostname)"
echo "User: $(whoami)"
echo "-------------------------"
```

Save the file.

### Step 3: Make It Executable

```bash
chmod 700 "$HOME/cron_lab/hello_cron.sh"
```

This gives the owner read, write, and execute permissions while denying access to group and other users.

### Step 4: Test It Manually

```bash
"$HOME/cron_lab/hello_cron.sh"
```

Confirm that it prints the expected information.

### Step 5: Add a Temporary Cron Schedule

Open your personal crontab:

```bash
crontab -e
```

Add:

```cron
*/5 * * * * /home/REPLACE_WITH_YOUR_USERNAME/cron_lab/hello_cron.sh >> /home/REPLACE_WITH_YOUR_USERNAME/cron_lab/hello_cron.log 2>&1
```

Replace the example path with your actual home directory.

You can determine it using:

```bash
printf '%s\n' "$HOME"
```

For this exercise, every five minutes is convenient for testing.

### Step 6: Wait and Verify

After the next scheduled execution:

```bash
cat "$HOME/cron_lab/hello_cron.log"
```

You should see the timestamp, hostname, username, and confirmation text.

### Step 7: Remove the Test Entry

Edit the crontab again:

```bash
crontab -e
```

Remove only the temporary test entry.

**Lab safety:** Use your personal crontab. Do not replace the entire crontab or edit system-wide scheduling files for this exercise.

---

## 13. Cybersecurity Use Case: Automated System Health Checks

### Scenario

A security analyst wants a daily report containing basic host information, disk usage, and memory usage.

Create:

```bash
nano "$HOME/cron_lab/daily_health_check.sh"
```

Add:

```bash
#!/usr/bin/env bash

set -u

echo "===== DAILY SYSTEM HEALTH CHECK ====="
echo "Timestamp: $(date)"
echo "Hostname: $(hostname)"
echo "Current user: $(whoami)"

echo
echo "===== DISK USAGE ====="
df -h

echo
echo "===== MEMORY USAGE ====="
free -h

echo
echo "===== SYSTEM UPTIME ====="
uptime

echo
echo "===== CHECK COMPLETED ====="
```

Set permissions:

```bash
chmod 700 "$HOME/cron_lab/daily_health_check.sh"
```

Test:

```bash
"$HOME/cron_lab/daily_health_check.sh"
```

Schedule it to run daily at 8:00 AM:

```cron
0 8 * * * /home/REPLACE_WITH_YOUR_USERNAME/cron_lab/daily_health_check.sh >> /home/REPLACE_WITH_YOUR_USERNAME/cron_lab/daily_health_check.log 2>&1
```

### What This Demonstrates

- Basic automation.
- Scheduled system inventory.
- Resource monitoring.
- Output collection.
- Repeatable administration.

This script is a basic health check, not a complete security audit or intrusion-detection system.

---

## 14. Cybersecurity Use Case: Detect Changes in a Critical File

### Scenario

An administrator wants to record a SHA-256 checksum of an important configuration file and compare it with a previously recorded baseline.

A checksum change can indicate a configuration update or an unexpected modification. It does not, by itself, prove malicious activity.

### Step 1: Select a File You Are Authorized to Monitor

For a lab, create a sample file:

```bash
mkdir -p "$HOME/cron_lab/monitored"
printf 'mode=production\n' > "$HOME/cron_lab/monitored/app.conf"
```

### Step 2: Create a Baseline

```bash
sha256sum "$HOME/cron_lab/monitored/app.conf" > "$HOME/cron_lab/app.conf.baseline"
```

### Step 3: Compare the Current Checksum

Run:

```bash
sha256sum -c "$HOME/cron_lab/app.conf.baseline"
```

If the file has not changed, the output should indicate `OK`.

If its content changes, the checksum comparison should fail.

### Step 4: Schedule a Check

Create a script:

```bash
nano "$HOME/cron_lab/check_config.sh"
```

Add:

```bash
#!/usr/bin/env bash

set -u

file="$HOME/cron_lab/monitored/app.conf"
baseline="$HOME/cron_lab/app.conf.baseline"
log="$HOME/cron_lab/config_check.log"

if [[ ! -r "$file" || ! -r "$baseline" ]]; then
    printf '%s ERROR: File or baseline is missing or unreadable\n' "$(date -Is)" >> "$log"
    exit 1
fi

if sha256sum -c "$baseline" >> "$log" 2>&1; then
    printf '%s File matches baseline\n' "$(date -Is)" >> "$log"
else
    printf '%s WARNING: Checksum verification failed\n' "$(date -Is)" >> "$log"
    exit 1
fi
```

Protect the script, baseline, and log appropriately:

```bash
chmod 700 "$HOME/cron_lab/check_config.sh"
chmod 600 "$HOME/cron_lab/app.conf.baseline"
```

Test it:

```bash
"$HOME/cron_lab/check_config.sh"
```

Schedule it every hour:

```cron
0 * * * * /home/REPLACE_WITH_YOUR_USERNAME/cron_lab/check_config.sh
```

### Important Limitations

- The baseline must be established from a trusted file.
- A user who can modify both the monitored file and the baseline may defeat this check.
- A checksum change can be legitimate.
- This lab records results locally; it does not provide tamper-proof alerting.

For production file-integrity monitoring, consider protected baselines, centralized logging, suitable integrity-monitoring tools, and a documented change-management process.

---

## 15. Security Auditing: Investigate Suspicious Cron Jobs

Attackers sometimes abuse scheduled tasks to maintain persistence. A security analyst should therefore inspect cron configurations during an authorized investigation.

### A. Inspect Your User's Crontab

```bash
crontab -l
```

### B. Inspect Root's Crontab

```bash
sudo crontab -l
```

Use administrative access only when authorized.

### C. Inspect Common System-Wide Locations

```bash
ls -la /etc/cron.d/
ls -la /etc/cron.hourly/
ls -la /etc/cron.daily/
ls -la /etc/cron.weekly/
ls -la /etc/cron.monthly/
```

Some directories may not exist on every distribution.

### D. Inspect the System Crontab

```bash
sudo cat /etc/crontab
```

Unlike a typical user crontab, system crontab entries include a user field:

```text
minute hour day month weekday user command
```

Example format:

```cron
0 3 * * * root /usr/local/sbin/maintenance_check.sh
```

This is an example of the format, not a command to install without review.

### E. Inspect a Suspect Script

For a script you are authorized to investigate:

```bash
stat /path/to/script.sh
```

```bash
file /path/to/script.sh
```

```bash
sed -n '1,200p' /path/to/script.sh
```

Review its ownership, permissions, modification time, contents, interpreter, and referenced commands.

### F. Search for Scheduled Task References

```bash
sudo grep -RniE 'curl|wget|base64|/tmp/' /etc/cron.d /etc/crontab
```

This is a preliminary text search, not a malware detector. It may produce false positives and may miss malicious jobs stored in other locations or encoded in different ways.

### Indicators Worth Investigating

- Unexpected scripts running from temporary or writable directories.
- Commands that download and execute remote content without a documented reason.
- Unexpected privileged scheduled jobs.
- Obfuscated shell commands.
- Recently modified scheduling files without an approved change.
- Commands that hide output or execute at unusual intervals.
- Scripts owned by unexpected accounts.

**Important:** These indicators are not proof of compromise. Preserve evidence, verify ownership and change history, and follow your incident-response procedures before disabling or deleting a scheduled job.

---

## 16. Cron Logs and Troubleshooting

### A. Check Cron Service Status

For a Debian- or Ubuntu-based system:

```bash
systemctl status cron
```

For a system using `crond`:

```bash
systemctl status crond
```

### B. Inspect the System Journal

Where cron logs are sent to journald:

```bash
sudo journalctl -u cron --since today
```

On systems using `crond`:

```bash
sudo journalctl -u crond --since today
```

The journal may not contain all job output, especially if output is redirected elsewhere.

### C. Inspect Authentication and System Logs

Depending on your distribution and logging configuration:

```bash
sudo grep -i cron /var/log/syslog
```

or:

```bash
sudo grep -i cron /var/log/cron
```

These files may not exist on your system.

### D. Check Whether the Job Is Installed

```bash
crontab -l
```

Confirm that the expression and command path are correct.

### E. Run the Command Manually

If the scheduled command is:

```cron
0 8 * * * /home/user/cron_lab/hello_cron.sh
```

test the underlying script directly:

```bash
/home/user/cron_lab/hello_cron.sh
```

Manual execution helps identify script errors but does not reproduce every detail of cron's execution environment.

### F. Check File Permissions

```bash
ls -l /home/user/cron_lab/hello_cron.sh
```

Verify that the scheduled account can read the script, execute it where required, and access its directories and output files.

### G. Check the Time Zone

```bash
timedatectl
```

Cron schedules normally use the system's applicable local time unless a supported configuration specifies otherwise.

Time-zone changes and daylight-saving transitions can affect when some jobs run.

---

## 17. Common Cron Problems

| Problem | Possible cause | Troubleshooting |
|---|---|---|
| Job never runs | Cron service is stopped | Check service status |
| Command works manually but fails in cron | Different environment or PATH | Use absolute paths and explicit environment settings |
| Permission denied | Incorrect file or directory permissions | Inspect ownership and permissions |
| No output appears | Output is redirected or mail is not configured | Add explicit log redirection |
| Job runs at an unexpected time | Incorrect expression or time zone | Verify fields and system time |
| Script works in a terminal only | Shell startup variables are missing | Define required variables explicitly |
| Script runs but produces incorrect results | Input assumptions or application errors | Inspect output, exit status, and logs |
| Multiple copies of a job overlap | Previous execution is still running | Add suitable concurrency controls |
| Job misses execution while the machine is off | Traditional cron does not normally catch up missed jobs | Consider anacron or a suitable systemd timer |

### Prevent Overlapping Jobs

For scripts that must not run concurrently, `flock` can provide a simple lock.

Example:

```cron
*/5 * * * * /usr/bin/flock -n /tmp/my_check.lock /home/user/cron_lab/hello_cron.sh
```

This is illustrative. Confirm that `flock` exists on your system and select a suitable lock location and permissions for your environment.

For production jobs, use a lock path with appropriate ownership and avoid trusting a lock file in an insecure directory when attackers can manipulate that directory.

---

## 18. Cron Security Best Practices

### 1. Apply Least Privilege

Run jobs as the least-privileged account that can perform the required task.

Do not use root merely because it makes permission errors disappear.

### 2. Protect Scripts

Use appropriate ownership and permissions.

Example for a personal script:

```bash
chmod 700 "$HOME/cron_lab/hello_cron.sh"
```

For privileged system jobs, store scripts in an administrator-controlled directory and prevent unauthorized modification.

### 3. Use Absolute Paths

Use explicit paths for scripts, interpreters, and important executables.

This reduces failures caused by a restricted or unexpected `PATH`.

### 4. Validate Inputs

Scripts that process filenames, arguments, or external data should validate them carefully.

Avoid constructing shell commands from untrusted text.

### 5. Protect Logs

Restrict log permissions and define a retention policy.

Logs may reveal usernames, file paths, host information, and security events.

### 6. Audit Scheduled Tasks

Review user crontabs, system crontabs, and cron directories during routine security checks.

Investigate unexpected changes and document legitimate scheduled jobs.

### 7. Avoid Unnecessary Downloads

Do not download and immediately execute remote scripts from cron without strong verification and an approved operational reason.

Prefer trusted package sources, integrity verification, and controlled deployment procedures.

### 8. Monitor Failures

Record meaningful output and error messages. A scheduled job that silently fails can create a false sense of security.

### 9. Review Privileged Commands

A root cron job can have substantial impact. Check every command it executes, its dependencies, its input files, and who can modify those files.

### 10. Use Version Control and Change Management

Track script changes with Git, document the intended schedule, and review changes before deployment.

---

## 19. Hands-On Lab Challenges

Perform these exercises in your own Linux VM or another authorized environment.

### Beginner Level

- [ ] Display your current user's crontab.
- [ ] Create a script that prints the date and hostname.
- [ ] Schedule the script to run every five minutes.
- [ ] Redirect its output to a log file.
- [ ] Remove the test entry after verifying it works.

### Intermediate Level

- [ ] Create a daily system health report.
- [ ] Schedule a disk-usage report at 9:00 AM.
- [ ] Create separate output and error logs.
- [ ] Troubleshoot a script that uses a relative path.
- [ ] Configure a job that must not overlap with an earlier execution.

### Cybersecurity Level

- [ ] Audit the cron entries belonging to your lab account.
- [ ] Inspect a sample system crontab and identify its user field.
- [ ] Create a checksum baseline for a sample configuration file.
- [ ] Schedule a read-only integrity check.
- [ ] Simulate a legitimate file modification and observe the checksum failure.
- [ ] Write a report describing a suspicious cron entry without immediately deleting it.
- [ ] Explain how least privilege and protected logs improve cron security.

### Challenge Questions

1. Why might a cron job work manually but fail when scheduled?
2. What is the difference between a user crontab and `/etc/crontab`?
3. Why is a root-owned script safer than a root-scheduled script writable by ordinary users?
4. How can an attacker abuse scheduled tasks for persistence?
5. What evidence should be preserved before modifying a suspicious scheduled job?

---

## 20. Interview Questions and Answers

### Q1. What is cron in Linux?

Cron is a time-based job scheduler that executes commands and scripts automatically according to configured schedules.

### Q2. What is crontab?

Crontab is the table used to define scheduled commands and their execution times. The `crontab` command manages user crontabs.

### Q3. What are the five fields in a standard cron expression?

Minute, hour, day of month, month, and day of week.

### Q4. What does `*/5 * * * *` mean?

It schedules a job every five minutes in traditional five-field cron syntax.

### Q5. What is the difference between `crontab -l` and `crontab -e`?

`crontab -l` lists the current user's crontab. `crontab -e` edits it.

### Q6. What does `crontab -r` do?

It removes the current user's entire crontab. It should be used cautiously because it can delete multiple scheduled jobs.

### Q7. Why should absolute paths be used in cron jobs?

Cron often runs with a limited environment and may use a different working directory or `PATH` from an interactive terminal.

### Q8. How do you redirect cron output to a log?

For example:

```cron
0 8 * * * /home/user/health_check.sh >> /home/user/health_check.log 2>&1
```

This appends standard output and standard error to the same log.

### Q9. How can you check whether cron is running?

Depending on the distribution:

```bash
systemctl status cron
```

or:

```bash
systemctl status crond
```

### Q10. What is the difference between cron and a systemd timer?

Cron uses cron-table scheduling, while systemd timers activate systemd units and can integrate with service dependencies, journal logging, and catch-up behavior where configured.

### Q11. Can cron jobs run with root privileges?

Yes. Root's crontab and authorized system-wide scheduling configurations can run commands as root. Such jobs require careful review and access control.

### Q12. What is cron persistence in cybersecurity?

It refers to using scheduled tasks to maintain recurring execution on a system. Legitimate administration uses scheduling routinely, but attackers may abuse it to execute malicious commands repeatedly.

### Q13. Why might a cron job not run when a computer is powered off?

Traditional cron generally does not automatically replay every missed job. Depending on the requirement, anacron or a systemd timer with appropriate persistence settings may be more suitable.

### Q14. How would you investigate a suspicious cron job?

I would record the relevant configuration and timestamps, inspect ownership and permissions, examine the referenced script and command line, review associated logs, correlate other system activity, and follow incident-response procedures before changing the job.

### Q15. How can you prevent overlapping cron executions?

Use an appropriate locking mechanism such as `flock`, or configure the job through a mechanism that provides suitable concurrency controls.

---

## 21. Scenario-Based Interview Questions

### Scenario 1: A Daily Backup Job Is Not Running

**Question:** Your backup script works manually but fails every morning when cron runs it. What would you investigate?

**Answer:**

1. Verify the cron expression.
2. Confirm that the correct crontab contains the job.
3. Check the cron daemon's status.
4. Use absolute paths for the script and dependencies.
5. Verify the scheduled user's permissions.
6. Capture standard output and standard error.
7. Inspect the script's exit status and logs.
8. Confirm that required storage and network resources are available.

### Scenario 2: A Root Cron Job Executes a Script in `/tmp`

**Question:** Why might this be dangerous?

**Answer:**

The temporary directory may be writable by untrusted users. If an attacker can replace or manipulate a script or one of its dependencies, a privileged scheduled job could execute attacker-controlled code.

The appropriate response is to investigate ownership, permissions, symlinks, dependencies, and change history. Privileged scripts should generally reside in an administrator-controlled location.

### Scenario 3: A Security Report Has Not Been Updated

**Question:** How would you investigate?

**Answer:**

Check the schedule, service status, execution logs, script permissions, environment variables, output path, disk space, and exit status. Also determine whether the machine was available at the scheduled time.

### Scenario 4: An Unknown Job Downloads a Script Every Ten Minutes

**Question:** What would you do?

**Answer:**

Treat it as suspicious until verified. Preserve the cron configuration and relevant logs, identify the account and referenced commands, inspect the script and network activity, and correlate the evidence with approved change records. Follow the organization's containment and incident-response procedures rather than immediately deleting evidence.

---

## 22. Quick Revision Notes

- **Cron:** Time-based job scheduler.
- **Crontab:** Stores scheduled job definitions.
- **Cron daemon:** Checks schedules and starts due commands.
- **`crontab -l`:** List the current user's crontab.
- **`crontab -e`:** Edit the current user's crontab.
- **`crontab -r`:** Remove the current user's entire crontab.
- **Five cron fields:** Minute, hour, day of month, month, day of week.
- **`*`:** Every permitted value.
- **`*/5`:** Every five units in the relevant field.
- **`>`:** Redirect output and overwrite the destination.
- **`>>`:** Append output.
- **`2>&1`:** Redirect standard error to the current standard-output destination.
- **`systemctl status cron`:** Check the cron service on systems using that service name.
- **`journalctl`:** Inspect journal entries where the relevant logs are available.
- **`flock`:** A tool that can help prevent overlapping executions.
- **Security auditing:** Review scheduled jobs, permissions, scripts, and logs.

---

## Summary

Cron is an important Linux administration tool that allows commands and scripts to execute automatically on a schedule.

For cybersecurity professionals, cron knowledge supports routine monitoring, defensive automation, maintenance, file-integrity checks, and investigations into suspicious scheduled execution.

The key skills are understanding cron syntax, using crontab safely, managing execution environments, collecting logs, troubleshooting failures, applying least privilege, and investigating unexpected scheduled tasks.

**Next file:** `12_System_Logs.md`
