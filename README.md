# Bare Minimum Debian Hardening Steps

A few absolutely essential hardening steps for a new Debian system.


## Preamble


An in NO WAY comprehensive set of notes for hardening a Debian system.


## License


    Copyright (c) 2023 Dennis R. Gesker.
    Permission is granted to copy, distribute and/or modify this document
    under the terms of the GNU Free Documentation License, Version 1.3
    or any later version published by the Free Software Foundation;
    with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
    A copy of the license is included in the section entitled "GNU


## Goals


Provide a **QUICK** and **DIRTY** Debian hardening notes.

- Full Distribution Upgrade
- Automatic Updates 
- Add REGULAR USER account
- Generate and Copy ssh key from workstation to new system
- Disable Password Authentication
- Install and Enable a Firewall
- Set System Name
    - Match DNS


## Non-Goals


- Guarantee Security
    - Can't be done but can be mitigated a bit
- Provide a substitute for comprehensive system security and administration guides


## Requirements


- Debian 11 (Bullseye)
- Ability to sudo on the system
- Ability to use a text editor and navigate directories
- Assumption: Your main workstation is a Linux
- Assumption: You have just completed a fresh install of Debian 11 (Bullseye)
    - Server or Virtual Instance


## Full System Upgrade


There still may be packages that should be upgraded immediately following a fresh installation. Particularly where the install was performed "offline" and updates were not applied during installation.


```bash
apt update;
apt dist-upgrade;
reboot
```


## Automatic Updates


Automatically installing updates published into the STABLE repositories is a good idea. Debian has a package to help you keep your system upto date so install this package and allow it to help you in your efforts to keeping you system up to date.


```bash
apt install unattended-upgrades
dpkg-reconfigure --priority=low unattended-upgrades
```


This package has some additional options. Consider enabling email notifications in your configuration. Check out [UnattendedUpgrades](https://wiki.debian.org/UnattendedUpgrades) at [Debian](http://wiki.debian.org).


## Add a REGULAR user account


It is not a good idea to use the 'root' account for your everyday needs. The 'root' account is best used only for system administration tasks. Create a regular user account and add that account to the 'sudo' group so that you can issue the occasinal root or system administration command.


```bash
adduser yourChosenRegularUserName
usermod -aG sudo yourChosenRegularUserName
```


## Add your PUBLIC ssh key to the regular user account


On your ***existing local workstation*** issue the ssh-copy-id command to upload your ssh key to the ~./ssh/authorized_keys file on the newly installed Debian system.


```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub yourChosenRegularUserName@new-system-ip-address
```


After issuing that command CHECK that you are able to login from your workstation to the new system.


```bash
ssh yourChosenRegularUserName@new-system-ip-address
```


Don't have an SSH key on your workstation? Just create one. Issue the following command and follow the prompts.


```bash
ssh-keygen -t ed25519
```


*TIP*: Use a specific key for each system you want to reach.
*TIP*: Tired of typing your password? When you generate your key leave the password fields blank.


## Disable Password Authentication on the new system


Since you are now able to access the new system using your SSH key - instead of using a username/password combination - disable BOTH password authentication and root logins on the new system.


```bash
ssh yourChosenRegularUserName@new-system-ip-address
sudo nano /etc/ssh/sshd_config
```


Update or add the following entries to the sshd_config file:

```
PermitRootLogin no
PasswordAuthentication no
```

Save the file and restart the sshd server.


```bash
sudo systemctl restart sshd
```


## Install and enable a firewall


The follwing commands will allow you to install a firewall AND also allow the new system to accept ssh connections on port 22.


```bash
sudo apt install ufw
sudo ufw status
sudo ufw allow 22
sudo ufw enable
```

*TIP*: Want to see what services are waiting for connections on the new system? Is the the ss comand:


```bash
sudo ss -tupln
```


## Avoid confusion - give the new system a name 


Giving the system an easy to understand name will help you avoid confusion. 

*TIP*: Add the name of the system (and it's IP address) to your DNS server.

Often the name of the system is set at install. Still, you can update the name of the system after install.

```bash
sudo vi /etc/hostname
```


Update the name of your new system. Example:

If the name in the hostname file is 'localhost' update the name to 'yourChosenHostname' except all lowercase letters.

Once you know what the IP address of your new system will be share this new name with your system administrator or if you are able update your DNS settings on your own. Optionally, add this information to the /etc/hosts file on the new system


```bash
sudo vi /etc/hosts
```


Example where 123.456.789.123 is the new-system-ip-address:

xxx.yyy.zzz.123 yourChosenHostname.your-domain.com yourChosenHostname


Save the file.


## Addittional Resources


- [Debian Adminsitrator's Handbook](https://debian-handbook.info/)
