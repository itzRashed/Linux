# Linux
📌 Tasks
1️⃣ User & Group Management
Learn about Linux users, groups, and permissions (/etc/passwd, /etc/group).
Task:
Create a user devops_user and add them to a group devops_team.
Set a password and grant sudo access.
Restrict SSH login for certain users in /etc/ssh/sshd_config.
2️⃣ File & Directory Permissions
Task:
Create /devops_workspace and a file project_notes.txt.
Set permissions:
Owner can edit, group can read, others have no access.
Use ls -l to verify permissions.
3️⃣ Log File Analysis with AWK, Grep & Sed
Logs are crucial in DevOps! You’ll analyze logs using the Linux_2k.log file from LogHub (GitHub Repo).
Task:
Download the log file from the repository.
Extract insights using commands:
Use grep to find all occurrences of the word "error".
Use awk to extract timestamps and log levels.
Use sed to replace all IP addresses with [REDACTED] for security.
Bonus: Find the most frequent log entry using awk or sort | uniq -c | sort -nr | head -10.
5️⃣ Process Management & Monitoring
Task:
Start a background process (ping google.com > ping_test.log &).
Use ps, top, and htop to monitor it.
Kill the process and verify it's gone.
6️⃣ Automate Backups with Shell Scripting
Task:
Write a shell script to back up /devops_workspace as backup_$(date +%F).tar.gz.
Save it in /backups and schedule it using cron.
Make the script display a success message in green text using echo -e.


**My Hands-on practice or solution for the Linux Task:**
**✅ 1. User & Group Management**

🔹 Set password for devops_user
ubuntu@ip-172-31-36-175:~$ sudo passwd devops_user
New password:
Retype new password:
passwd: password updated successfully

🔹 Verify current user & switch to new user
ubuntu@ip-172-31-36-175:~$ whoami
ubuntu

ubuntu@ip-172-31-36-175:~$ su devops_user
Password:
$ whoami
devops_user

**✅ 2. File & Directory Permissions**

🔹 Create workspace and file
ubuntu@ip-172-31-36-175:~$ mkdir devops_workspace
ubuntu@ip-172-31-36-175:~$ cd devops_workspace
ubuntu@ip-172-31-36-175:~/devops_workspace$ touch project_notes.txt

🔹 Check default permissions
ubuntu@ip-172-31-36-175:~/devops_workspace$ ls -l
total 0
-rw-rw-r-- 1 ubuntu ubuntu 0 Nov 10 07:06 project_notes.txt

🔹 Set owner: full, group: read, others: none
ubuntu@ip-172-31-36-175:~/devops_workspace$ chmod 740 project_notes.txt
ubuntu@ip-172-31-36-175:~/devops_workspace$ ls -l
total 0
-rwxr----- 1 ubuntu ubuntu 0 Nov 10 07:06 project_notes.txt

**✅ 3. Log File Analysis (grep, awk, sed)**

🔹 Navigate to logs
ubuntu@ip-172-31-36-175:~$ cd ~/logs
ubuntu@ip-172-31-36-175:~/logs$ ls
app.log  auth_failure_ips.txt

🔹 Search for errors
ubuntu@ip-172-31-36-175:~/logs$ grep -i "error" app.log

🔹 Search authentication failures
ubuntu@ip-172-31-36-175:~/logs$ grep -i "authentication failure" app.log
Jun 14 15:16:01 combo sshd(pam_unix)[19939]: authentication failure; logname= uid=0 euid=0 tty=NODEVssh ruser= rhost=218.188.2.4
Jun 14 15:16:02 combo sshd(pam_unix)[19937]: authentication failure; logname= uid=0 euid=0 tty=NODEVssh ruser= rhost=218.188.2.4

🔹 Extract timestamps and IP with awk
ubuntu@ip-172-31-36-175:~/logs$ awk '/authentication failure/{print $1,$2,$3,$12,$13,$14,$15}' app.log
Jun 14 15:16:01 ruser= rhost=218.188.2.4
Jun 14 15:16:02 ruser= rhost=218.188.2.4

🔹 Save extracted data
ubuntu@ip-172-31-36-175:~/logs$ awk '/authentication failure/{print $1,$2,$3,$12,$13,$14,$15}' app.log > falied_ips.txt

🔹 Mask IP addresses using sed
ubuntu@ip-172-31-36-175:~/logs$ sed "s/rhost/[REDACTED]/g" falied_ips.txt
Jun 14 15:16:01 ruser= [REDACTED]=218.188.2.4
Jun 14 15:16:02 ruser= [REDACTED]=218.188.2.4
Jun 15 02:04:59 ruser= [REDACTED]=220-135-151-1.hinet-ip.hinet.net user=root

🔹 Most frequent log entries
ubuntu@ip-172-31-36-175:~/logs$ awk '{print $0}' app.log | sort | uniq -c | sort -nr | head -10

**✅ 4. Process Management & Monitoring**

🔹 Start a background process
ubuntu@ip-172-31-36-175:~$ ping google.com > oing_test.log &

🔹 Monitor processes
ubuntu@ip-172-31-36-175:~$ ps aux | grep ping
ubuntu@ip-172-31-36-175:~$ top
ubuntu@ip-172-31-36-175:~$ htop

🔹 Kill the process
ubuntu@ip-172-31-36-175:~$ kill 1935
ubuntu@ip-172-31-36-175:~$ ps aux | grep ping

🔹 Verify output
ubuntu@ip-172-31-36-175:~$ tail oing_test.log

**✅ 5. Automating Backups with Shell Script + Cron**

🔹 Create backups directory & script
ubuntu@ip-172-31-36-175:~$ sudo mkdir -p /backups
ubuntu@ip-172-31-36-175:~$ sudo nano /backups/backup_devops_workspace.sh
backup_devops_workspace.sh
#!/bin/bash
tar -czf /backups/backup_$(date +%F).tar.gz ~/devops_workspace

if [ $? -eq 0 ]; then
    echo "Backup completed successfully: /backups/backup_$(date +%F).tar.gz"
else
    echo "Backup failed!"
fi

🔹 Make script executable
ubuntu@ip-172-31-36-175:~$ sudo chmod +x /backups/backup_devops_workspace.sh

🔹 Schedule with cron (daily at 02:00 AM)
ubuntu@ip-172-31-36-175:~$ sudo crontab -e
Add:
0 2 * * * /backups/backup_devops_workspace.sh >> /backups/backup_log.txt 2>&1
