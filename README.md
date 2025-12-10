 This is the modified line that I am writing from my local Linux machine.
📌 Tasks<br>
1️⃣ User & Group Management<br>
Learn about Linux users, groups, and permissions (/etc/passwd, /etc/group).<br>
Task:<br>
Create a user devops_user and add them to a group devops_team.<br>
Set a password and grant sudo access.<br>
Restrict SSH login for certain users in /etc/ssh/sshd_config.<br>
2️⃣ File & Directory Permissions<br>
Task:<br>
Create /devops_workspace and a file project_notes.txt.<br>
Set permissions:<br>
Owner can edit, group can read, others have no access.<br>
Use ls -l to verify permissions.<br>
3️⃣ Log File Analysis with AWK, Grep & Sed<br>
Logs are crucial in DevOps! You’ll analyze logs using the Linux_2k.log file from LogHub (GitHub Repo).<br>
Task:<br>
Download the log file from the repository.<br>
Extract insights using commands:<br>
Use grep to find all occurrences of the word "error".<br>
Use awk to extract timestamps and log levels.<br>
Use sed to replace all IP addresses with [REDACTED] for security.<br>
Bonus: Find the most frequent log entry using awk or sort | uniq -c | sort -nr | head -10.<br>
5️⃣ Process Management & Monitoring<br>
Task:<br>
Start a background process (ping google.com > ping_test.log &).<br>
Use ps, top, and htop to monitor it.<br>
Kill the process and verify it's gone.<br>
6️⃣ Automate Backups with Shell Scripting<br>
Task:<br>
Write a shell script to back up /devops_workspace as backup_$(date +%F).tar.gz.<br>
Save it in /backups and schedule it using cron.<br>
Make the script display a success message in green text using echo -e.<br>


**My Hands-on practice or solution for the Linux Task:**<br>
**✅ 1. User & Group Management**<br>

🔹 Set password for devops_user<br>
ubuntu@ip-172-31-36-175:~$ sudo passwd devops_user<br>
New password:<br>
Retype new password:<br>
passwd: password updated successfully<br>

🔹 Verify current user & switch to new user<br>
ubuntu@ip-172-31-36-175:~$ whoami<br>
ubuntu<br>

ubuntu@ip-172-31-36-175:~$ su devops_user<br>
Password:<br>
$ whoami<br>
devops_user<br>

**✅ 2. File & Directory Permissions**<br>

🔹 Create workspace and file<br>
ubuntu@ip-172-31-36-175:~$ mkdir devops_workspace<br>
ubuntu@ip-172-31-36-175:~$ cd devops_workspace<br>
ubuntu@ip-172-31-36-175:~/devops_workspace$ touch project_notes.txt<br>

🔹 Check default permissions<br>
ubuntu@ip-172-31-36-175:~/devops_workspace$ ls -l<br>
total 0<br>
-rw-rw-r-- 1 ubuntu ubuntu 0 Nov 10 07:06 project_notes.txt<br>

🔹 Set owner: full, group: read, others: none<br>
ubuntu@ip-172-31-36-175:~/devops_workspace$ chmod 740 project_notes.txt<br>
ubuntu@ip-172-31-36-175:~/devops_workspace$ ls -l<br>
total 0<br>
-rwxr----- 1 ubuntu ubuntu 0 Nov 10 07:06 project_notes.txt<br>

**✅ 3. Log File Analysis (grep, awk, sed)**<br>

🔹 Navigate to logs<br>
ubuntu@ip-172-31-36-175:~$ cd ~/logs<br>
ubuntu@ip-172-31-36-175:~/logs$ ls<br>
app.log  auth_failure_ips.txt<br>

🔹 Search for errors<br>
ubuntu@ip-172-31-36-175:~/logs$ grep -i "error" app.log<br>

🔹 Search authentication failures<br>
ubuntu@ip-172-31-36-175:~/logs$ grep -i "authentication failure" app.log<br>
Jun 14 15:16:01 combo sshd(pam_unix)[19939]: authentication failure; logname= uid=0 euid=0 tty=NODEVssh ruser= rhost=218.188.2.4<br>
Jun 14 15:16:02 combo sshd(pam_unix)[19937]: authentication failure; logname= uid=0 euid=0 tty=NODEVssh ruser= rhost=218.188.2.4<br>

🔹 Extract timestamps and IP with awk<br>
ubuntu@ip-172-31-36-175:~/logs$ awk '/authentication failure/{print $1,$2,$3,$12,$13,$14,$15}' app.log<br>
Jun 14 15:16:01 ruser= rhost=218.188.2.4<br>
Jun 14 15:16:02 ruser= rhost=218.188.2.4<br>

🔹 Save extracted data<br>
ubuntu@ip-172-31-36-175:~/logs$ awk '/authentication failure/{print $1,$2,$3,$12,$13,$14,$15}' app.log > falied_ips.txt<br>

🔹 Mask IP addresses using sed<br>
ubuntu@ip-172-31-36-175:~/logs$ sed "s/rhost/[REDACTED]/g" falied_ips.txt<br>
Jun 14 15:16:01 ruser= [REDACTED]=218.188.2.4<br>
Jun 14 15:16:02 ruser= [REDACTED]=218.188.2.4<br>
Jun 15 02:04:59 ruser= [REDACTED]=220-135-151-1.hinet-ip.hinet.net user=root<br>

🔹 Most frequent log entries<br>
ubuntu@ip-172-31-36-175:~/logs$ awk '{print $0}' app.log | sort | uniq -c | sort -nr | head -10<br>

**✅ 4. Process Management & Monitoring**<br>

🔹 Start a background process<br>
ubuntu@ip-172-31-36-175:~$ ping google.com > oing_test.log &<br>

🔹 Monitor processes<br>
ubuntu@ip-172-31-36-175:~$ ps aux | grep ping<br>
ubuntu@ip-172-31-36-175:~$ top<br>
ubuntu@ip-172-31-36-175:~$ htop<br>

🔹 Kill the process<br>
ubuntu@ip-172-31-36-175:~$ kill 1935<br>
ubuntu@ip-172-31-36-175:~$ ps aux | grep ping<br>

🔹 Verify output<br>
ubuntu@ip-172-31-36-175:~$ tail oing_test.log<br>

**✅ 5. Automating Backups with Shell Script + Cron**<br>

🔹 Create backups directory & script<br>
ubuntu@ip-172-31-36-175:~$ sudo mkdir -p /backups<br>
ubuntu@ip-172-31-36-175:~$ sudo nano /backups/backup_devops_workspace.sh<br>
backup_devops_workspace.sh<br>
#!/bin/bash<br>
tar -czf /backups/backup_$(date +%F).tar.gz ~/devops_workspace<br>

if [ $? -eq 0 ]; then<br>
    echo "Backup completed successfully: /backups/backup_$(date +%F).tar.gz"<br>
else<br>
    echo "Backup failed!"<br>
fi<br>

🔹 Make script executable<br>
ubuntu@ip-172-31-36-175:~$ sudo chmod +x /backups/backup_devops_workspace.sh<br>

🔹 Schedule with cron (daily at 02:00 AM)<br>
ubuntu@ip-172-31-36-175:~$ sudo crontab -e<br>
Add:<br>
0 2 * * * /backups/backup_devops_workspace.sh >> /backups/backup_log.txt 2>&1<br>
