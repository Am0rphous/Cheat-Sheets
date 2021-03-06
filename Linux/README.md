# L i n u x

- [CDROM](#cdrom)
  - [Add virtual CDROM](#add-virtual-cdrom)
  - [DVD Ripping](#dvd-ripping)
- [**Disk**](#disk)
- [**File Permissions and Ownership**](#File-Permissions-and-Ownership)
  - [Files and Folders](#Files-and-Folders)
    - [File and directory commands](#file-and-directory-commands)
    - [File compression](#file-compression)
    - [Find large files](#find-large-files)
- [**Help**](#Help)
- [**Jobs/schedule a task with Crontab**](#jobsschedule-a-task-with-crontab)
- [**Keeping your system up-to-date**](#keeping-your-system-up-to-date)
  - [Cron-apt](#cron-apt)
  - [Unattended Upgrades](#unattended-upgrades)
- [**Kernel**](#Kernel)
   - [Kernel Security](#kernel-security)
   - [Kernel Install/Removal](#kernel-installremoval)
- [**Loops**](#Loops)
- [**Peripherals**](#peripherals)
  - [USB- and PCI-devices](#usb--and-pci-devices)
- [**Power**](#Power)
- [**Remoting**](#remoting)
  - [Monitoring SSH](#monitoring-ssh)
- [**Searching for stuff**](#searching-for-stuff)
  - [Find](#find)
  - [Grep - utility for string and pattern matching](#grep---utility-for-string-and-pattern-matching)
    - [Locate a file](#locate-a-file)
    - [Whereis - locate the binary, source, and manual page files for a command](#whereis---locate-the-binary-source-and-manual-page-files-for-a-command)
    - [Which](#which)
    - [Zgrep - search within compressed files](#zgrep---search-within-compressed-files)
- [**Services**](#Services)
- [**Text**](#text)
- [**Time and date**](#time-and-date)
- [**Users and groups**](#users-and-groups)
  - [Random password generation](#Password-generation)
  - [Run script with another users privileges](#run-script-with-another-users-privileges)
  - [User info](#user-info)
- [**Variables**](#Variables)

<br >
<br >

## CDROM
### Add virtual CDROM
- [CDEmu](https://cdemu.sourceforge.io)
````powershell
sudo add-apt-repository ppa:cdemu/ppa           #install repo
sudo apt install cdemu-client                   #install client
cdemu load 0 ubuntu.iso                         #mounts iso file in device 0
cdemu load 0 ubuntu.iso centos.iso mint.iso     #mounts multiple iso files
cdemu add-device                                #adds extra device
cdemu load 1 ubuntu.iso                         #loads iso to the second device
cdemu status                                    #display status
cdemu unload                                    #unloads iso files
cdemu remove-device
````
To use GUI open `gcdemu` (graphical-cdemu).

### DVD Ripping
1. 
````powershell
[root@pc ~]# isoinfo -d -i /dev/sr1 | grep -i -E 'block size|volume size'
Logical block size is: 2048
Volume size is: 350320
[root@pc ~]#
````
2. 
````powershell
[root@pc ~]# dd if=/dev/sr1 of=/mnt/folder/test.iso bs=2048 count=350320
350320+0 records in
350320+0 records out
700640 bytes (700 MB) copied, 72.405 s, 10.4 MB/s
[root@pc ~]#
````
3.
````powershell
sha256sum /mnt/folder/test.iso      #E.g. c58ea020874bae8712d5715a...
sha256sum /dev/sr0                  #E.g. c58ea020874bae8712d5715a...
````

## Jobs/schedule a task with Crontab
- [Crontab Calculator](https://crontab.guru)
- [Crontab.cronhub.io](https://crontab.cronhub.io/)
Enter crontab: `crontab -e`
````powershell
tar -cJpf /media/NAS/backups/backup.tar.xz /home
0 0 * * 0 root (apt-get update && apt-get -y -d upgrade) > /dev/null
minute - hour - day of month - month - day of week
````
Cron routines can be found in these files and folders
- /ect/crontab
  - /etc/cron.daily
  - /etc/cron.hourly
  - /etc/cron.weekly
  - /etc/cron.monthly
 - /etc/cron.d

## Keeping your system up-to-date
If you're scripting, then use `apt-get` (more stable output), if not, use `apt` which is a newer command.
````powershell
sudo apt update
sudo apt dist-upgrade -y                            #Install all updates available
sudo apt update && sudo apt list --upgradable       #list upgradable packets
sudo apt show package_name                          #displays info about package_name
sudo apt-get install --only-upgrade NAME            #only upgrade packet NAME
sudo add-apt-repository ppa:what/whatever           #add repo in /etc/apt/sources.list or .d
sudo add-apt-repository -r ppa:na/name              #removes ppa "na/name"
````

 ### cron-apt
 ````powershell
 sudo apt install cron-apt
 /etc/cron-apt/config            #configuration path
 /etc/cron.d/cron-apt            #default crontab entry 
 /usr/sbin/cron-apt              #testing cron-apt
 ````
 ### Unattended Upgrades
 - [Documentation](https://wiki.debian.org/UnattendedUpgrades)
 - [Enable-Automatic-Updates.sh](https://github.com/Am0rphous/Bash/blob/master/Security/Enable-Automatic-Updates.sh) - Script that automatic install and enable automatic updates with smart settings.
 - [Unattended-upgrades](https://github.com/mvo5/unattended-upgrades) - Automatic installation of security upgrades on apt based systems.
 Installation
 ````powershell
 sudo apt update && sudo apt dist-upgrade -y
 sudo apt install unattended-upgrades update-notifier-common
 sudo dpkg-reconfigure unattended-upgrades
 ````
 Run `sudo nano /etc/apt/apt.conf.d/50unattended-upgrades` and make sure it contains
 ````powershell
 Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}";
        "${distro_id}:${distro_codename}-security";
     // "${distro_id}ESMApps:${distro_codename}-apps-security";
        "${distro_id}ESM:${distro_codename}";
        "${distro_id}ESM:${distro_codename}-infra-security";
        "${distro_id}:${distro_codename}-updates";
        "${distro_id}:${distro_codename}-proposed";
        "${distro_id}:${distro_codename}-backports";
        // The Onion Router
        // "origin=TorProject";
};
Unattended-Upgrade::Package-Blacklist {
};
Unattended-Upgrade::DevRelease "auto";
Unattended-Upgrade::AutoFixInterruptedDpkg "true";
Unattended-Upgrade::MinimalSteps "true";
Unattended-Upgrade::InstallOnShutdown "false";
Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-WithUsers "false";
Unattended-Upgrade::Automatic-Reboot-Time "06:00";
Unattended-Upgrade::Verbose "true";
Unattended-Upgrade::Debug "true";
 ````
 Then open and edit `nano /etc/apt/apt.conf.d/20auto-upgrades`
 ````powershell
APT::Periodic::Enable "1";
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "30";
 ````
 Test config
 ````
 sudo unattended-upgrade -d -v
 ````

- Log files
````powershell
sudo cat /var/log/unattended-upgrades/unattended-upgrades.log
sudo tail -f /var/log/unattended-upgrades/unattended-upgrades.log
sudo grep 'linux-image' /var/log/unattended-upgrades/unattended-upgrades.log
````
 
## File Permissions and Ownership
- [Linux File Permissions and Ownership Explained with Examples](https://linuxhandbook.com/linux-file-permissions/)
````powershell
chattr -R folder                #chattr=manipulate attributes. R=recursively.
lsattr file/folder              #list attributes. R=Recm V=verbose, a=list all files
````

  ### Files and Folders
  ````powershell
  head file list.txt                              #displays 10 first line of file
  head -n 5 list.txt                              #displays 5 first line of file
  mkdir myfolder                                  #creates folder
  mkdir -v test                                   #v=verbose
  mkdir folder1/folder2/folder3/folder4/folder5   #makes every folder if they dont exist
  touch myfile                                    #creates empty file
  ````
    #### Find large files
    ````powershell
    du -a /var | sort -n -r | head -n 10
    du -hs * | sort -rh | head -n 10
    du -hsx * | sort -rh | head -10
    cd /path/to/directory && du -hsx * | sort -rh | head -10
    sudo du -a /home | sort -n -r | head -n 10
    find / -size +100M -ls
    find / -size +100M -size -200M -ls
    sudo find / -type f -printf “%s\t%p\n” | sort -n | tail -1
    find $HOME -type f -printf ‘%s %p\n’ | sort -nr | head -10
    ````
    Find top 10 files and directories consuming maximum disk space [source](https://sourcedigit.com/24840-how-to-find-large-files-in-linux-ubuntu/)
    ````powershell
    alias ducks='du -cks * | sort -rn | head'
    ducks
    ````

    #### File compression
    File compression's main advantage is when transferring files. Transfering 100 1KB files takes longer than transfering one 100 KB size file.
    ````powershell
    7z x archive.7z                             # sudo apt install p7zip-full
    gzip -k core.c                              # compress core.c file and removes the original file
    sudo apt install xz-utils                   # usually installed as default
    tar -xf file.tar.xz                         # x = extract. f=filename
    tar -xvfz file.tar/file.tgz                 # extracts .tar or .tgz files
    tar -xf file_name.tar -C /target/folder     # extracts to new folder
    ````
    Extract each file to new folder with same name
    ````powershell
    for i in *.zip; do unzip "$i" -d "${i%%.zip}"; done
    ````

## Help
- [Linux User and Programmer's Manual - Manpages](https://www.systutorials.com/docs/linux/man/)
````powershell
command --help
ipconfig --help
man command
man ipconfig
````
## Kernel
- [Kernel Hacking Guides](https://www.kernel.org/doc/html/latest/kernel-hacking/index.html)
Commands
````
cat /proc/version
uname -a
uname -r                            #kernel version
uname -sr                           #kernel name and version
dpkg --list | grep linux-image      # list installed kernels
````
  ### Kernel Security
  - [AppArmor](https://www.apparmor.net)
  ````powershell
  aa-status                       # display various information about the current AppArmor policy.
  ````
  Check for unsigned kernel modules
  ````powershell
  for mod in $(lsmod | tail -n +2 | cut -d' ' -f1); do modinfo ${mod} | grep -q "signature" || echo "no signature for module: ${mod}" ; done
  ````
   Checks if Secure Boot is enabled:
   ````powershell
   sudo mokutil --sb-state
   ````

  ### Kernel Install/Removal
  - [Source for commands: askubuntu.com(...)](https://askubuntu.com/questions/1207958/error-24-write-error-cannot-write-compressed-block)
  - Don't touch these packages
  ````powershell
  dpkg -l | egrep "linux-(signed|modules|image|headers)" | grep $(uname -r)
  ````
  - List old kernels we don't need with
  ````powershell
  dpkg -l | egrep "linux-(signed|modules|image|headers)" | grep -v $(uname -r | cut -d - -f 1)
  ````
  - Purge them with this command
  ````powershell
  dpkg -l | egrep "linux-(signed|modules|image|headers)" | grep -v $(uname -r | cut -d - -f 1) | awk {'print $2'} | xargs sudo apt purge -y
  ````

## Loops
- [Source 1 - How To Unix For Loop 1 to 100 Numbers](https://www.cyberciti.biz/faq/unix-for-loop-1-to-10)
- [Source 2 - Loops! ](https://ryanstutorials.net/bash-scripting-tutorial/bash-loops.php)
The purpose of loops is to take a series of commands and re-run them with minimal code. Often used in automation and repetitive tasks. Syntax:
````powershell
while [ <something> ]
do
  <command>
done
````
Example
````powershell
#!/bin/bash
counter=1
while [ $counter -le 10 ]      #lt=less than. -le=less than or equal (will print up until nine)
do
  echo $counter
  ((counter++))
done
echo "done!"
````

A For loop in bash:
````powershell
#!/bin/bash
for ((i=1;i<=10;i++)); 
do 
   #Doing lots of stuff here
   echo $i
done
````

  ### Random password generation
  ````powershell
  cat /dev/urandom | tr -dc 'a-z A-Z'              generates lots of gibberish forever. Press ctrl+c to stop it
  dd if=/dev/urandom count=1 bs=128 | sha512sum    creates a block and hashes it with sha512
  head -c 10 /dev/random | sha256sum               reads from /dev/Random and calculates a hash from the first 10 bytes
  head -c 10 /dev/urandom | sha256sum              read the first 10 bytes from /dev/Urandom and hash it with sha256
  head -c 1024 /dev/urandom | sha256sum            read the first 1024 bytes from /dev/Urandom and hash it with sha256
  head -c 100 /dev/urandom | od | sha256sum        dumps it to octal and hashes the output
  openssl rand -base64 32                          generates a 32 character long password
  openssl rand -base64 40                          generates a 40 character long password
  sudo passwd root                                 change the password of root
  sudo passwd                                      change the password of current user
  ````

## Peripherals
### USB- and PCI-devices
Listing
````powershell
lsusb
ls /proc/bus/usb
lspci
usb-devices 
````

Webcam
````powershell
sudo apt-get install v4l-utils
v4l2-ctl --list-devices
v4l2-ctl --list-formats-ext
````
Play from webcam
````powershell
sudo apt install ffmpeg
ffplay /dev/video0
````

## Searching for stuff

  ### Find
````powershell
  find filename
  find / -empty               #searches for empty files and folders in your system
  find ~ filename             #searches through the active user's home folder
  find . filename             #searches through current and nested folders
  find / filename             #searches from root directory
  find / -executable          #searches for executable files
  find / -name *.mp3          #searches for files with .mp3 extension
  find / -name myfile -exec nano '{}' \;      #searches for files with 'myfile' names and opens nano
  ````
### Grep - utility for string and pattern matching
- [grep](https://www.cyberciti.biz/faq/howto-use-grep-command-in-linux-unix/%20)
````zsh
grep -r testing                             # grep recursivly
grep -v success *                           # list everything except "success"
grep -i test                                # i= ignore case
grep -R -i memfree .                        # R = recursive dereference
grep -q error syslog.log                    # silence the output
echo $?                                     # results in 0 or 1 depending on true or false
grep -E -w -i "failed|error" syslog.log     # E=search for multiple words
grep -Evi "success|warning" syslog.log      # Removes success or warning
````
Extract IP-addresses from a file [source](https://github.com/dwisiswant0/awesome-oneliner-bugbounty)
`grep -E -o '(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)' MYFILE.txt`

#### Locate a file
````powershell
locate file                             #locates file on harddrive
updatedb                                #updates the database
 sudo /usr/libexec/locate.updatedb      #updates database on MacOS
````
#### Whereis - locate the binary, source, and manual page files for a command
| Key/Command | Description | Installation |
| ----------- | ----------- |------------  |
| whereis command | Finds command |
| whereis program | Finds program |
| whereis -b date | Search for only binary files |
| whereis -s date | Search only for source code files |

#### Which
- [which](https://www.cyberciti.biz/faq/unix-linux-which-command-examples-syntax-to-locate-programs/%20)

#### zgrep - search within compressed files
Same syntax
````powershell
zgrep -c "error" errorlog.txt.gz            # prints matching lines
zgrep -h "linux" GFG.txt.gz                 # Display the matched lines but not file names
````

## Power
- [PowerPanel](https://www.cyberpowersystems.com/product/software/power-panel-personal/powerpanel-for-linux/)
- [UPower](https://upower.freedesktop.org) - UPower is an abstraction for enumerating power devices, listening to device events and querying history and statistics.
 ````powershell
 acpi                               #shows battery status and other ACPI information
 sudo lshw -c power
 sudo dmidecode --type 39           #DMI table decoder
 ````

## Remoting
````powershell
ssh username@domain-name.com
ssh username@ip
ssh -p 1234 user@domain
ssh-keygen -q -f /etc/ssh/ssh_host_rsa_key -N '' -b 4096 -t rsa
ssh-keygen -q -f /etc/ssh/ssh_host_ecdsa_key -N '' -b 521 -t ecdsa
````
### Monitoring SSH
````powershell
last -a | grep -i still
netstat -tnpa | grep 'ESTABLISHED.*sshd'
ps auxwww | grep sshd: | grep -v grep
ps -fC sshd
ss | grep -i ssh
w
who
````

## Services
````powershell
service ssh status                                      #option 1: shows service status
systemctl status ssh                                    #option 2: shows service status
systemctl enable ssh                                    #enables service ssh
systemctl enable tor1                                   #enable new service tor1
systemctl --type=service                                #list loaded services
sudo systemctl list-unit-files | grep enabled           #lists enabled services
````

## Text
| Key/Command | Description |
| ----------- | ----------- |
| string="A String With Text" | Creates a string with text |
| echo "${string,,}" | Converts to lower case |
| echo "${string^^}" | Converts to upper case |
| column -s, -t <tmp.csv | Pretty print a simple csv |

Sorting (sources [1](https://stackoverflow.com/questions/15984414/bash-script-count-unique-lines-in-file),
````powershell
sort ips.txt | uniq -c
sort ips.txt | uniq -c | sort -bgr
````
Counting number of uniqe lines
````powershell
sort ips.txt | uniq | wc -l
awk '!seen[$0]++' ips.txt | wc -l
````


## Time and date
````powershell
cal                                 #Displays a calender in terminal
date
ncal                                #Another calender commmand
ncal -w                             #Print the number of the week below each week column
ncal year                           #Displays calender for year 'year'
ncal 2001                           #Displays calender for year 2001
now=$(date +%Y%m%d-%H%M%S)          #creates variable in format YearMonthDay-HourMinutesSeconds
time command                        #times a command
time lsof                           #times the lsof command
````


User Information	groups • id • lastcomm • last • lid/libuser-lid • logname • members • users • whoami • who • w
 https://www.cyberciti.biz/faq/unix-linux-whereis-command-examples-to-locate-binary/

## Users and groups
````powershell
groups peter
su - peter                          #switch to user peter
sudo adduser mike
sudo usermod -aG sudo mike
````
sudoers: `/etc/sudoers`
````
sudo touch /etc/sudoers.d/peter
echo "peter  ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/peter
````

### Run script with another users privileges
- [source](https://blog.mypapit.net/)
````powershell
su -c "/usr/bin/executable" -s /bin/sh username
````

### User info
- **who** It is used to get information about currently logged in user on to system. If you don't provide any option or arguments, the command displays the following information for each logged-in user.

    1. Login name of the user
    2. User terminal
    3. Date & Time of login
    4. Remote host name of the user

   ```bash
   $ who
   sudheer :0 2019-08-04 01:21 (:0)
   ```

Notes for later
````
- https://github.com/ghsecurity/LinuxCheatSheet/blob/master/LinuxCheatSheet
- https://gto76.github.io/linux-cheatsheet/
- https://github.com/jeroendoggen/Linux-cheat-sheet
- https://www.computerhope.com/unix/test.htm
````


## Variables
````powershell
read -p "Write the path: " variableName
````
