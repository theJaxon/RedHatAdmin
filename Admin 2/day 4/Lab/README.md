# Day 4 Lab: 

### Part 1 [Networking]:

1. Display your MAC address by 2 different ways.

```bash
ip -a link
ifconfig # deprecated
```

2. Display the network settings of all active interfaces.

```bash
ip link show # displays all the interfaces available
```

3. Display the network setting of all interfaces both active inactive.

```bash
ifconfig -a
```

4. Bring your interface down.
`ip link set dev eth0 down`

5. Configure your network card to have static IP.

```bash
nmtui 
select the wifi network, select manual instead of automaitc then select IPV4 config and enter the address

```

<details><summary>nmtui preview</summary>
<p>

<img src="https://github.com/theJaxon/RedHatAdmin/blob/master/etc/Admin%202/day%204/Lab/Manualnmtui.png">

</p>
</details>

6. Bring your interface up.
`ip link set dev eth0 up`

7. Verify your network setting using ifconfig command.
`ifconfig`

8. Configure your network card to have dynamic IP using network manager command.

```bash
nmcli con show
nmcli con mod "Da Nyt" \
➜ ipv4.address ""

```

9. Check using ifconfig then check its configuration file.

```bash
cat /etc/sysconfig/network-scripts/ifcfg-eth1 
```

10. Reconfigure your network card using system-config-network utility to have static IP.

same as answer number `5`

11. Configure your network card to have 3 IPs and check that they are all working using ifconfig command.

12. Change your host name in your global network file.
```bash
hostname
CentOS-7
vi /etc/hostname then change the hostname to anything else, save 
then hostname new-hostname 
vi /etc/hosts and replace the old hostname with the new hostname there too
```

--- 

1. Using the useradd command, add accounts for the following users in your system: user1, user2, user3, user4, user5, user6 and user7. Remember to give each user a password.

useradd.sh
```bash 
#!/bin/bash
for i in {1..7}
do
    useradd "user${i}"
done
```

```bash
chmod +x useradd.sh
sudo ./useradd.sh
sudo tail /etc/passwd
```
<details><summary>/etc/passwd after useradd</summary>
<p>

<img src="https://github.com/theJaxon/RedHatAdmin/blob/master/etc/Admin%202/day%204/Lab/useradd.png">

</p>
</details>

2. Using the groupadd command, add the following groups to your system.
			Group   GID
			sales    10000
			hr       10001
			web      10002
Why should you set GID in this manner instead of allowing the system to set the GID by default?

```bash
sudo groupadd sales --gid 10000 
sudo groupadd hr --gid 10001 
sudo groupadd web --gid 10002 
```
Setting gid manually enforces that they remain in the same numeric range

3. Using the usermod command to add user1 and user2 to the sales auxiliary group, user3 and user4 to the hr auxiliary group. User5 and user6 to web auxiliary group. And add user7 to all auxiliary groups

```bash
sudo usermod -aG sales,hr,web user7

sudo usermod -aG sales user1
sudo usermod -aG sales user2

sudo usermod -aG hr user3
sudo usermod -aG hr user4
```

4. Login as each user and use id command to verify that they are in the appropriate groups. How else might you verify this information?

```bash
sudo tail /etc/group
```

<details><summary>/etc/group after usermod</summary>
<p>

<img src="https://github.com/theJaxon/RedHatAdmin/blob/master/etc/Admin%202/day%204/Lab/usermod.png">

</p>
</details>

5. Create a directory called /depts with a sales, hr, and web directory within the depts directory.

`mkdir /depts && cd /depts  && mkdir sales hr web`

6. Using the chgrp command, set the group ownership of each directory to the group with the matching name

```bash
sudo chgrp sales sales/
sudo chgrp hr hr/
sudo chgrp web web/
```

<details><summary>ls -l showing dirs with their associated groups</summary>
<p>

<img src="https://github.com/theJaxon/RedHatAdmin/blob/master/etc/Admin%202/day%204/Lab/chgrp.png">

</p>
</details>

7. Set the permissions on the /depts directory to 755, and each subdirectory to 770

```bash
cd /depts 
sudo chmod -R 770 *
```

<details><summary>ls -l after chmod</summary>
<p>

<img src="https://github.com/theJaxon/RedHatAdmin/blob/master/etc/Admin%202/day%204/Lab/chmod%20770.png">

</p>
</details>

`sudo chmod 755 /depts`

8. Set the set-gid bit on each departmental directory

`sudo chmod g+s sales hr web`

9. Use the su command to switch to the user2 account and attempt the following commands:
touch /depts/sales/user2.txt
touch /depts/hr/ user2.txt
touch /depts/web/ user2.txt
Which of these commands succeeded and which failed? 
What is the group ownership of the files that were created?

```bash
su - user2

touch /depts/sales/user2.txt # Succeeds

```

The other 2 touch commands results in a permission denied error
group ownership is `sales`

10. Configure sudoers file to allow user3 and user4 to use /bin/mount and /bin/umount commands, while allowing user5 only to use fdisk command.

```bash
visudo
user3 ALL= (root) NOPASSWD: /bin/mount, /bin/umount
user4 ALL= (root) NOPASSWD: /bin/mount, /bin/umount
user5 ALL= (root) NOPASSWD: /sbin/fdisk
```

11. Login by user3 and try to unmount /boot.
```bash
su - user3
sudo umount /boot
```

12. Login by user4 and remount /boot. Also try to view the partition table using fdisk.

```bash
su - user4
mnt /boot
fdisk /boot
```

13. Create a directory with permissions rwxrwx---, grant a second group (sales) r-x permissions

```bash
sudo mkdir /test
sudo chmod 770 /test

sudo chgrp sales /test
sudo chmod g+r-x /test
```

14. create a file on that directory and grant read and write to a second group (sales)

```bash
sudo cd /test
sudo touch test2
sudo chgrp sales test2    
udo chmod g+r+w test2   
```

15. set the the owning group as the owning group of any newly created file in that directory.
`sudo chown :sales .`
