# Day 2 Lab:
[![made-with-bash](https://img.shields.io/badge/Made%20with-Bash-1f425f.svg)](https://www.gnu.org/software/bash/)
[![made-with-Markdown](https://img.shields.io/badge/Made%20with-Markdown-1f425f.svg)](http://commonmark.org)

### Part 1 [Cron & at]:
1- List system process every 10 minutes.
```bash
crontab -e
*/10 * * * * logger ps aux
```

2- Create an empty directory file /tmp/myat.trail after 2 minutes from now.
```bash
[vagrant@Centos ~]$ at now + 2 minutes
at> touch /tmp/myat.trial
at> <EOT>
job 2 at Sun May  3 15:50:00 2020
[vagrant@Centos ~]$ atq
2       Sun May  3 15:50:00 2020 a vagrant
```

3- Deny guest account from using at command.
```bash
vim /etc/at.deny
guest
```

4- What happens if the user guest is listed in both at.allow and at.deny?

> after adding the user vagrant to both at.allow and at.deny , the user is allowed to use the `at` command so that allow overrides the deny.

5- Schedule the system to display system time at 1:00 every 1st of May
```bash
echo "$(date)" > /etc/cron.monthly/showdate # This displays earch month but not at 1:00 specifically 
```

```bash
crontab -e
0 1 1 5 * echo "$(date)"
```

6. You want to know some information about the status of the system every ten minutes daily between the hours of 8:00 AM and 5:00 PM. 
to help investigate some performance issues you have been having in disk space usage.
```bash
crontab -e 
*/10 8-17 * * logger df -h 
```

7. Use mail as the root user to check for e-mail from the cron jobs you have scheduled.
```bash
cat /var/spool/mail/root
```

8- How could you send the output from these cron jobs to another e-mail address (the manager user)?
by adding 
```
MAILTO=manager
```
at the beginning of the crontab -e file

---

### Part 2 [logrotate]:


9- Configure logrotate default setting to compress log files when they are rotated.
```bash
vim /etc/logrotate.conf
# uncomment this if you want your log files compressed
compress
```