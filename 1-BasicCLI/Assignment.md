### Assignment - Basic Linux

### Updates

1. sudo apt get update - gets availeble updates, if any 
2. sudo apt get upgrade - upgrades the system, downloads and applies updates
3. sudo reboot - restarts the system 

### User Stuff

4. sudo su - prompt changes to something like this: root@DESKTOP-7U1SG72:~#
5. useradd doesnt do much, adduser does a lot more, as per the image
![Picture of running adduser and useradd](https://github.com/koleada/Cybersecurity-Class/blob/main/1-BasicCLI/addingUsers.png?raw=true)
6. su sally - prompt looks like this: sally@DESKTOP-7U1SG72:~$
7. Cannot add user as sally as she is not root and is not allowed to run privledged commands at all on this machine.
8. Delete Bobby - **sudo deluser bobby** (use --remove-home to remove home directory if needed)
9. changed password using **sudo passwd** 
10. Why is staying on as root bad? 
- Its bad for a few reasons, if your in an office and leaving a root shell open, poeple or attackers could use that shell. Applications and services running as root are a security risk for things like privledge escelation. Limiting the exposure of admin level accounts is always best practice. 
11. **id** - shows you user ID , group ID current user, eg: uid=1002(sally) gid=1002(sally) groups=1002(sally)

### Groups
12. Sally only belongs to the group "sally". Can be seen using command **groups <username** or **id**(currnetly logged in user only)
13. **sudo usermod -aG sudo <username>** - this adds specified user to the sudoers file so they can perform privledged actions
14. **sudo groupadd <group-name>** - creates a group of the specified name
15. **sudo usermod -aG <groupname> <username>** - adds specified user to specified group
