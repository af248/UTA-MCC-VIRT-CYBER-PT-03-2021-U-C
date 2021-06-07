## Week 5 Homework: Archiving and Logging Data

This unit's homework is designed to solidify the following concepts and tools:


- Create a `tar` archive that excludes a directory using the `--exclude=` command option.
- Manage backups using cron jobs.
- Write bash scripts to create system resource usage reports.
- Perform log filtering using `journalctl`.
- Manage log file sizes using `logrotate`.
- Create an auditing system to check for policy and file violations using `auditd`.

Please refer to the student guides and slides from this unit's lessons as you work through the assignment. If you  get stuck, remember you can use Google and man pages for more information.

---
### Scenario

For this assignment, you will play the role of a security analyst for Credico Inc., a financial institution that offers checking, savings, and investment banking services.

- The company collects, processes, and maintains a large database of private financial information for both consumer and business accounts.

- The data is maintained on a local server.

- The company must comply with the Federal Trade Commission's Gramm-Leach-Bliley Act ([GLBA](https://www.ftc.gov/tips-advice/business-center/privacy-and-security/gramm-leach-bliley-act)), which requires that financial institutions explain their information-sharing practices to their customers and protect sensitive data. 

In an effort to mitigate network attacks and meet federal compliance, Credico Inc. developed an efficient log management program that performs:
- Log size management using `logrotate`.
- Log auditing with `auditd` to track events, record the events, detect abuse or unauthorized activity, and create custom reports.

These tools, in addition to archives, backups, scripting, and task automation, contribute to a fully comprehensive log management system.

You will expand and enhance this log management system by learning new tools, adding advanced features, and researching additional concepts.

---

### Lab Environment

To set up your lab environment with the necessary files, complete the following steps.

- Log into your local virtual machine. Use the following credentials:

  - Username: `sysadmin`
  - Password: `cybersecurity`

- Open the terminal within your Ubuntu VM by pressing Ctrl+Alt+T for Windows users or Ctrl+Options+T for Mac users.

  - Alternatively, press Windows+A (Command+A for Mac users),  type "Terminal" in the search bar, and select the terminal icon (not the Xfce Terminal icon).

- Create a directory called `Projects` in your `/home/sysadmin/` directory.

- Download the following file (you can either slack it to yourself or use the Firefox browser in your Ubuntu machine), and move it to your `~/Projects` directory before you get started:

  - [TarDocs.tar](https://drive.google.com/a/2tor.com/file/d/1fRjFS1vOdS7yfKJgpJxR02_UxeT_qI_u/view?usp=sharing)

---

### Instructions

As you solve each step below, please fill out the [Submission File](SubmissionFile.md). This will be your homework deliverable.

In each of the following sections, you will use and build on your system administration tools and knowledge. Make sure to read the instructions carefully.

#### Step 1: Create, Extract, Compress, and Manage tar Backup Archives

Creating `tar` archives is something you must do everyday in your role at Credico Inc. In this section, you will extract and exclude specific files and directories to help speed up your workflow.

To get started, navigate to the `~/Projects` directory where your downloaded `TarDocs.tar` archive file should be.

1. Extract the `TarDocs.tar` archive file into the current directory (`~/Projects`). Afterwards, list the directory's contents with `ls` to verify that you have extracted the archive properly.


Verify that there is a `Java` subdirectory in the `TarDocs/Documents` folder by running: `ls -l ~/Projects/TarDocs/Documents/`.
```bash
tar -xf TarDocs.tar
```

2. Create a `tar` archive called `Javaless_Docs.tar` that excludes the `Java` directory from the newly extracted `TarDocs/Document/` directory.

      - If you've executed this command properly, you should have a `Javaless_Docs.tar` archive in the `~/Projects` folder.
```bash
# In ~/Projects
tar --exclude=TarDocs/Documents/Java -cf Javaless_Docs.tar TarDocs
```

3. Verify that this new `Javaless_Docs.tar` archive does not contain the `Java` subdirectory by using `tar` to list the contents of `Javaless_Docs.tar` and then piping `grep` to search for `Java`.
```bash
tar -tf Javaless_Docs.tar | grep -i java
```

**Bonus** 
- Create an incremental archive called `logs_backup.tar.gz` that contains only changed files by examining the `snapshot.file` for the `/var/log` directory. You will need `sudo` for this command.
```bash
sudo tar -czf logs_backup.tar.gz --listed-incremental=snapshot.file --level=0 /var/log
```

---

#### Step 2: Create, Manage, and Automate Cron Jobs

In response to a ransomware attack, you have been tasked with creating an archiving and backup scheme to mitigate against CryptoLocker malware. This attack would encrypt the entire server’s hard disk and can only be unlocked using a 256-bit digital key after a Bitcoin payment is delivered.

```cron
0 6 * * 3 tar -czf /auth_backup.tgz /var/log/auth.log
```
---

#### Step 3: Write Basic Bash Scripts

Portions of the Gramm-Leach-Bliley Act require organizations to maintain a regular backup regimen for the safe and secure storage of financial data.


1. Using brace expansion, create the following four directories:
    
```bash
mkdir -p ~/backups/{freemem,diskuse,openlist,freedisk}
```


2. Edit the `system.sh` script file so that it that does the following:

     ```bash
    #!/bin/bash
    # Free memory
    free -h > ~/backups/freemem/free_mem.txt

    # Disk usage. Could also be run as:
    # du -h -d 1 / > ~/backups/diskuse/disk_usage.txt
    # to only show a depth of 1 directory, or
    # du -hs 1 / > ~/backups/diskuse/disk_usage.txt
    # to only show a summary.
    du -h / > ~/backups/diskuse/disk_usage.txt

    # List open files
    lsof > ~/backups/openlist/open_list.txt

    # Free disk space
    df -h > ~/backups/freedisk/free_disk.txt
    ```

3. Save this file and make sure to change or modify the `system.sh` file permissions so that it is executable.

```bash
# Assuming it is in your current working directory...
chmod +x ./system.sh
```
**Optional** 
- Confirm the script ran properly by navigating to any of subdirectories in the `~/backup/` directory and using `cat <filename>` to view the contents of the backup files.
```bash
cd ~/backup
cat freemem/free_mem.txt
cat diskuse/disk_usage.txt
cat openlist/open_list.txt
cat freedisk/free_disk.txt
```
**Bonus**
- Automate your script `system.sh` by adding it to the `weekly` system-wide cron directory.
```bash
sudo cp ~/system.sh /etc/cron.weekly
```
---

#### Step 4. [OPTIONAL] Manage Log File Sizes

**We did not cover this in class so please skip this if you want. For those that want, this is extra learning material.**

You realize that the spam messages are making the size of the log files unmanageable.

You’ve decided to implement log rotation in order to preserve log entries and keep log file sizes more manageable. You’ve also chosen to compress logs during rotation to preserve disk space and lower costs.

1. Run `sudo nano /etc/logrotate.conf` to edit the `logrotate` config file. You don't need to work out of any specific directory as you are using the full configuration file path.
    ```bash
    /var/log/auth.log {
      weekly
      rotate 7
      notifempty
      delaycompress
      missingok
    }
    ```

2. Configure a log rotation scheme that backs up authentication messages to the `/var/log/auth.log` directory using the following settings:

      - Rotates weekly.

      - Rotates only the seven most recent logs.

      - Does not rotate empty logs.

      - Delays compression.

      - Skips error messages for missing logs and continues to next log.

      Don't forget to surround your rotation rules with curly braces `{}`.

---

#### Bonus: Check for Policy and File Violations

In an effort to help mitigate against future attacks, you have decided to create an event monitoring system that specifically generates reports whenever new accounts are created or modified, and when any modifications are made to authorization logs.

1. Verify the `auditd` service is active using the `systemctl` command.
```bash
systemctl status auditd
```

2. Run `sudo nano /etc/audit/auditd.conf` to edit the `auditd` config file using the following parameters. You can run this command from anywhere using the terminal.

      - Number of retained logs is seven.

      - Maximum log file size is 35.
    ```bash
    max_log_file = 35
    num_logs = 7
    ```

3. Next, run `sudo nano /etc/audit/rules.d/audit.rules` to edit the rules for `auditd`. Create rules that watch the following paths:

```bash
  -w /etc/shadow -p wra -k hashpass_audit
  -w /etc/passwd -p wra -k userpass_audit
  -w /var/log/auth.log -p wra -k authlog_audit
```

4. Restart the `auditd` daemon.
```bash
sudo systemctl restart auditd
```

5. Perform a listing that reveals all existing `auditd` rules.
```bash
sudo auditctl -l
```

6. Using `sudo`, produce an audit report that returns results for all user authentications.

```bash
sudo aureport -au
```

7. Now you will shift into hacker mode. Create a user with `sudo useradd attacker` and produce an audit report that lists account modifications. 
```bash
sudo aureport -m
```

8. Use `auditctl` to add another rule that watches the `/var/log/cron` directory.
```bash
sudo auditctl -w /var/log/cron
```

9. Perform a listing that reveals changes to the `auditd` rules took affect.
```bash
sudo auditctl -l
```
---

#### Bonus (Reasearch Activity): Perform Various Log Filtering Techniques

There was a suspicious login from a host on the network during the early morning hours when the office was closed. The senior security manager tasked you with filtering through log files to determine if a system breach occurred.

For the bonus, **write** the `journactl` commands, for each use case below. 

**Hint:** Remember that `journal` tracks each log relative to each system boot. Also, keep in mind that you can sort messages by priority, relative boot, and specific units.

1. Write the `journalctl` command that performs a log search that returns all messages, with priorities from emergency to error, since the current system boot.
```bash
journalctl -b -p 0..3
```

1. Write the `journalctl` command that checks the disk usage of the system journal unit since the most recent boot. You will likely have to pipe this output to `less` if it doesn't fit on the screen.
```bash
journalctl -b -u systemd-journald --disk-usage
```

   - The unit you want is `systemd-journald`.

1. Write the `journalctl` command that removes all archived journal files except the most recent two.
```bash
sudo journalctl --vacuum-files=2
```

1. Write the `journalctl` command that filters all log messages with priority levels between zero and two, and save the results to a file named `Priority_High.txt` in `/home/student/` directory.
```bash
sudo journalctl -p 0..2 >> /home/sysadmin/Priority_High.txt
```

1. Automate the last task by creating a cron job that runs daily in the user crontab.
```bash
@daily journalctl -p 0..2 >> /home/sysadmin/Priority_High.txt
```

  **Note**: You'll need `sudo` to run `journalctl`.

---

### Submission Guidelines 

- Please fill out and submit [SubmissionFile.md](SubmissionFile.md).

---
© 2020 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.
