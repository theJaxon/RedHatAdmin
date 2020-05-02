# Day 1 Lab:
[![made-with-bash](https://img.shields.io/badge/Made%20with-Bash-1f425f.svg)](https://www.gnu.org/software/bash/)
[![made-with-Markdown](https://img.shields.io/badge/Made%20with-Markdown-1f425f.svg)](http://commonmark.org)
### Part 1 [SELinux]:

1- Change your default SELinux mode to permissive and reboot.
```bash
sudo vim /etc/sysconfig/selinux
SELINUX=permissive

sudo systemctl reboot
```

2- After reboot, verify the system is in permissive mode.
```bash
[vagrant@Centos ~]$ sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   permissive
Mode from config file:          permissive
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      31

[vagrant@Centos ~]$ getenforce
Permissive
```

3- Change the default SELinux mode to enforcing.
```bash
sudo vim /etc/sysconfig/selinux
SELINUX=enforcing
```

4- Change the current SELinux mode to enforcing.
```bash
sudo setenforce 1 

[vagrant@Centos ~]$ getenforce
Enforcing
```

5- Copy /etc/resolv.conf file to root's home directory.
```bash
sudo cp /etc/resolv.conf /root
```

6- Observe the SELinux context of the intial /etc/resolv.conf
```bash
[vagrant@Centos ~]$ ls -Z /etc/resolv.conf
-rw-r--r--. root root system_u:object_r:net_conf_t:s0  /etc/resolv.conf
```

7- Move resolv.conf from root's home directory to /etc/resolv.conf
```bash
mv /root/resolv.conf /etc/
```

8- Observe the SELinux of the newly copied /etc/resolv.conf
```bash
[root@Centos vagrant]# ls -Z /etc/resolv.conf
-rw-r--r--. root root system_u:object_r:net_conf_t:s0  /etc/resolv.conf
```

9- Restore the SELinux context of the newly positioned /etc/resolv.conf
```bash
[root@Centos vagrant]# restorecon /etc/resolv.conf
```

10- Observe the SELinux context of the restored /etc/resolv.conf
```bash
[root@Centos vagrant]# ls -Z /etc/resolv.conf
-rw-r--r--. root root system_u:object_r:net_conf_t:s0  /etc/resolv.conf
```
* For some reason, after the cp and mv commands, the context stayed the same !

---

### Part 2 [OpenSSH]:

11- Configure OpenSSH to allow pulic key-based login credentials
```bash
vim /etc/ssh/sshd_config
PubkeyAuthentication yes # Uncomment the line
systemctl restart sshd # restart the sshd server application so that changes take effect
```

12-	Create an SSH key-pair
```bash
[root@Centos vagrant]# ssh-keygen -f ./id_rsa
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in ./id_rsa.
Your public key has been saved in ./id_rsa.pub.
```

13-	Configure to login without the need of a password.
```bash
vim /etc/ssh/sshd_config
PasswordAuthentication no
```
14-	Configure SSH to prevent root logins.
```bash
vim /etc/ssh/sshd_config
PermitRootLogin no
systemctl restart sshd # restart the sshd server application so that changes take effect
```
