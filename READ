In a DevOps interview, shell scripting is often assessed to determine a candidate's ability to automate tasks, handle system operations, and streamline workflows. Below are some of the most common shell scripting programs or questions that are frequently asked in DevOps interviews:

### 1. **Find all files modified in the last 7 days**
   - **Task**: Write a script to find all files modified in the last 7 days in a specific directory.
   - **Answer**:
     ```bash
     find /path/to/directory -type f -mtime -7
     ```

### 2. **Check disk space and send an alert**
   - **Task**: Write a script to check if disk space is above a certain threshold and send an alert if it is.
   - **Answer**:
     ```bash
     #!/bin/bash
     THRESHOLD=80
     USAGE=$(df / | grep / | awk '{ print $5 }' | sed 's/%//g')
     if [ $USAGE -gt $THRESHOLD ]; then
         echo "Disk space is over $THRESHOLD%" | mail -s "Disk Space Alert" admin@example.com
     fi
     ```

### 3. **Count the number of lines in all files in a directory**
   - **Task**: Write a script to count the total number of lines across all files in a directory.
   - **Answer**:
     ```bash
     #!/bin/bash
     total_lines=0
     for file in /path/to/directory/*; do
         if [ -f "$file" ]; then
             lines=$(wc -l < "$file")
             total_lines=$((total_lines + lines))
         fi
     done
     echo "Total lines: $total_lines"
     ```

### 4. **Backup files to a remote server**
   - **Task**: Write a script to backup files to a remote server using `rsync`.
   - **Answer**:
     ```bash
     #!/bin/bash
     rsync -avz /path/to/local/directory/ user@remote:/path/to/remote/backup/
     ```

### 5. **Write a script to monitor the status of a service**
   - **Task**: Write a shell script to check the status of a service (e.g., Apache or Nginx) and restart it if it's not running.
   - **Answer**:
     ```bash
     #!/bin/bash
     SERVICE="apache2"
     if ! systemctl is-active --quiet $SERVICE; then
         echo "$SERVICE is not running. Restarting..."
         systemctl start $SERVICE
     else
         echo "$SERVICE is running."
     fi
     ```

### 6. **Check if a process is running**
   - **Task**: Write a script to check if a given process is running on the system.
   - **Answer**:
     ```bash
     #!/bin/bash
     PROCESS_NAME="nginx"
     if ps aux | grep -v grep | grep $PROCESS_NAME > /dev/null
     then
         echo "$PROCESS_NAME is running."
     else
         echo "$PROCESS_NAME is not running."
     fi
     ```

### 7. **Process log file to find errors**
   - **Task**: Write a script to search for "error" or "fail" in a log file.
   - **Answer**:
     ```bash
     #!/bin/bash
     LOG_FILE="/var/log/application.log"
     grep -i -E "error|fail" $LOG_FILE
     ```

### 8. **Create a user and assign privileges**
   - **Task**: Write a script to create a user and assign sudo privileges.
   - **Answer**:
     ```bash
     #!/bin/bash
     USERNAME=$1
     useradd $USERNAME
     echo "$USERNAME ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
     ```

### 9. **Rotate log files**
   - **Task**: Write a script to rotate log files (keep last 7 days' worth).
   - **Answer**:
     ```bash
     #!/bin/bash
     LOG_DIR="/var/log/myapp"
     find $LOG_DIR -type f -name "*.log" -mtime +7 -exec rm -f {} \;
     ```

### 10. **Automate the cleanup of old files**
   - **Task**: Write a script that automatically deletes files older than 30 days from a directory.
   - **Answer**:
     ```bash
     #!/bin/bash
     find /path/to/directory -type f -mtime +30 -exec rm -f {} \;
     ```

### 11. **Write a script to perform a simple health check on a web server**
   - **Task**: Write a script to check if a web server is responding by checking HTTP status.
   - **Answer**:
     ```bash
     #!/bin/bash
     URL="http://localhost"
     STATUS_CODE=$(curl -o /dev/null -s -w "%{http_code}" $URL)
     if [ "$STATUS_CODE" -eq 200 ]; then
         echo "Web server is up."
     else
         echo "Web server is down."
     fi
     ```

### 12. **Generate a report of all active users**
   - **Task**: Write a script to list all currently active users on the system.
   - **Answer**:
     ```bash
     who | awk '{ print $1 }' | sort | uniq
     ```

### 13. **Schedule tasks with cron**
   - **Task**: Write a script that schedules a backup task to run every day at 3 AM.
   - **Answer**:
     ```bash
     echo "0 3 * * * /path/to/backup_script.sh" | crontab -
     ```

### 14. **Create and manage environment variables**
   - **Task**: Write a script to set an environment variable and check its value.
   - **Answer**:
     ```bash
     #!/bin/bash
     export MY_ENV_VAR="Hello, DevOps!"
     echo $MY_ENV_VAR
     ```

### 15. **Batch rename files**
   - **Task**: Write a script to batch rename files in a directory, for example, changing the extension of all `.txt` files to `.bak`.
   - **Answer**:
     ```bash
     #!/bin/bash
     for file in /path/to/directory/*.txt; do
         mv "$file" "${file%.txt}.bak"
     done
     ```

### 16. **Read input from a file and perform actions**
   - **Task**: Write a script to read a list of IP addresses from a file and ping each one.
   - **Answer**:
     ```bash
     #!/bin/bash
     while read IP; do
         ping -c 1 $IP
     done < /path/to/ip_list.txt
     ```

### 17. **Change permissions of files recursively**
   - **Task**: Write a script to change permissions of all files in a directory recursively.
   - **Answer**:
     ```bash
     chmod -R 755 /path/to/directory
     ```

### 18. **Compress files and create a backup**
   - **Task**: Write a script to compress a directory and create a backup file.
   - **Answer**:
     ```bash
     #!/bin/bash
     tar -czvf /path/to/backup.tar.gz /path/to/directory
     ```

### 19. **Send an email on script failure**
   - **Task**: Write a script that sends an email if any command fails.
   - **Answer**:
     ```bash
     #!/bin/bash
     set -e
     {
         # Your commands here
         cp /path/to/file /destination/
         echo "Success"
     } || {
         echo "Script failed" | mail -s "Script Failure" admin@example.com
     }
     ```

### 20. **Write a script to check the system uptime**
   - **Task**: Write a script to check the system uptime.
   - **Answer**:
     ```bash
     #!/bin/bash
     uptime
     ```

---

### Final Thoughts:
In DevOps interviews, shell scripting is essential for automating daily tasks, system maintenance, and ensuring smooth operation of CI/CD pipelines. These examples not only test basic scripting knowledge but also assess familiarity with system commands, file manipulation, and automation concepts. Be prepared to explain the reasoning behind your solutions and to modify them based on specific requirements.
