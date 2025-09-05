# Linux Commmands Cheatsheet
## To the reader
> This cheatsheet was written with AlmaLinux 9 and Ubuntu 22.04 in mind, so some things may differ from the distribution that you are using.

> Even though there are certain steps for enabling and starting services after some packages have been installed, this isn't always necessary on e.g. Ubuntu.

## Users and groups

**Overview**
```sh
/etc/passwd             # Contains all user accounts
/etc/groups             # Contains all groups
/etc/shadow             # Contains all user accounts passwords

/etc/login.defs                     # Defaults for new user accounts
/etc/skel                           # Skeleton dir for new user accounts
/etc/security/pwquality.conf        # System wide config file for password requirements
```

**Create user**
```sh
useradd <username>
useradd -r <username>           # Adds a system account (different UID)
```

**Set or change a user's password**
```sh
passwd <username>
echo "<username>:<password>" | chpasswd           # chpasswd can be used to batch update users password.
```

**Check ID on user or group**
```sh
id USERNAME
```

**Delete user**
```sh
userdel <username>
userdel -r <username>               # Deletes the user along with the home directory
```

**Create group**
```sh
groupadd <group name>
```

**Delete group**
```sh
groupdel <group name>
```

**Add or remove user from group**
```sh
usermod -a -G user group                    # Adds the user to the group
gpasswd -a user group 
                      
gpasswd -d user group                       # Remove the user from the group
```

**Disable or enable user**
```sh
usermod -l <username>           # Lock user account
usermod -u <username>           # Unlock user account
```

**Set shell for user**
```sh
usermod -s /bin/sh                  # Set shell to Bourne shell
usermod -s /sbin/nologin <username>       # When the user tries to login, it will be politely told that a user doesn't have a shell
usermod -s /bin/false <username>      # When the user tries to login, the connection will immediately drop
```

**set password parameters for user**
```sh
chage -m mindays -M maxdays -d lastday -I -E expiredate -W warndays <username>
```

**Become root**
```sh
su -
```

**Run a command with sudo**
```sh
sudo <command>
```

**Run sudo in interactive mode**
```sh
sudo -i
```

**Become another user and change to their home directory**
```sh
su - <username>
```

**Add user to admin**
```sh
usermod -a -G sudo <username>               # For Debian based OS
usermod -a -G wheel <username>              # For Red Hat based OS
```

**Don't require password for sudo**
1. Run `visudo`.
2. Add `<username>   ALL=(ALL) NOPASSWD:ALL         # Gives user permission to run sudo without password.` under `# User privilege specification`. Replace `<username>` with desired user.

## Permissons

### Permission fundamentals

**List all files with tmp**
```ls -l tmp```

**Remove write from group on file/folder**
```chmod g-w tmp```

**Remove read from others on file/folder**
```chmod a-r tmp```

**Remove write from user on file/folder**
```chmod u-w tmp```

**Add read and write to user on file/folder**
```chmod u+rw```

**Add read and execute to use on file**
```chmod u+x```

**Add read, write and execute to all users on file/folder**
```chmod a+rwx```

! Even though user hasn't any read permission, the user will still be able to delete the file

! You need execute permission to "cd" (ls) into folder

**Test if file or folder exists**
```sh
if test -f "<file>"
then
    echo true
fi

if test -d "<folder>"
then
    echo true
fi
```



**Change owner or group recursive**
```
chown -R USER
chgrp -R GROUP
```

**View partition, mounts and filesystem
```df -hT```

**View files and sort by size in reverse order
```du -h ~ | sort -nr```

**See indivial cores on cpu in top
```top AND THEN PRESS 1```

**List open files
```lsof | grep PATTERN```

**Output all network interface to file
```tcpdump -i INTERFACE > FILE```

**Move/Copy hidden files (Zsh)**
```sh
setopt glob_dots
mv Foo/* Bar/
unsetopt glob_dots
```

**Move/Copy hidden files (Bash)**
```sh
shopt -s dotglob
mv Foo/* Bar/
shopt -u dotglob
```

### SetUID, SetGID and Sticky bit

**Overview**
```sh
rwsrwxrwx   mark marketing          # if we run the executable, it will run as user mark
rwxrwsrwx   mark marketing          # if we run the executable, it will run as group marketing
rwxrwsrwx   mark marketing          # if it's an directory, all file placed within the directory will have marketing as owner

rwx-rwxrwT                          # others can't execute
rwx-rwxrwt                          # others can execute
```

**Enable or disable SetUID**
```sh
chmod u+s
chmod u-s
```

**Enable or disable SetGID**
```sh
chmod g+s
chmod g-s
```

**Enable or disable Sticky bit**
```sh
chmod g+s
chmod g-s
```

### ACL

**Set or remove ACL on file or directory**
```
setfacl -m u:USER:rwx PATH
setfacl -m g:GROUP:r PATH
setfacl -Rm g:GROUP:r PATH
setfacl -x g:GROUP PATH
```

**Remove all ACL from file or directory**
```
setfacl -b PATH
```

**Get current ACL on file or directory**
```
getfacl
```
> ! Permission write with ACL doesn't allow deletion of files

## Processes
**List processes**
```sh
ps -ef | grep <process>
ps -ef --sort=-%cpu | head -10
ps -ef --sort=-%mem | head -10
pstree
```

**Grep PID of process**
```sh
pgrep <process>
```

**Kill process**
```sh
kill <PID>
pkill <process>

kill -9 <PID>                   # Force
pkill -9 <process name>
```

**Force killing all processes with certain name 
```sh
killall -s 9 apache2
```


systemctl --all

**Reload configuration for a service**
```systemctl reload application.service```

**
```systemctl status/start/stop/restart application.service```

**Enable or disable service at boot time**
```systemctl enable/disable application.service```


**Enable or disable service completely**
```systecmctl mask/unmask application.service```

**Search for specific service**
```sh
systemctl list-units --no-pager | grep -i <search term>
```

## Misc
**Verify checksum of file**
```sh
echo checksum file | sha256sum -c

OR

echo $(cat checksumfile) file | sha256sum -c
```

**Calculate checksum of multiple files**
```
find . -type f -exec sha256sum {} \;                # Calculate checksum of files in the current directory.

OR

find /etc -type f -exec sha256sum {} \;             # Calculate checksum of files in a specific directory.

```
**List how many matches a grep results in**
```
cat file | grep string | wc -l
```

**Grep this OR that**
```
grep -E "this|that"
```

**List unique lines**
```
cat file | uniq
```

**Download output from URL**
```
wget -O FILE URL                # Download output as file from URL
```




dmesg

**Stream file
```tail -f FILE```


**Search input file for regex matches (in this case for a MAC address) and output matches and their respective line numbers**
cat FILE | grep -n -i [0-9a-f][0-9a-f]:[0-9a-f][0-9a-f]:[0-9a-f][0-9a-f]:[0-9a-f][0-9a-f]:[0-9a-f][0-9a-f]:[0-9a-f][0-9a-f]




**Check if package is installed
```rpm -qa | grep PACKAGE```



#############**TIME

**Set and view time and date configuration**
```timedatectl```

**View time zones**
```timedatectl list-timezones```

**Set time zone**
```timedatectl set-timezone Europe/Stockholm```

**Set time**
```
timedatectl set-time 20:15:50
```
OR
```
timedatectl set-time '2021-08-18 20:15:50'
```
OR
```
date -s '2021-08-18 20:15:50'
```
**Sync time with NTP
```timedatectl set-ntp true```

### NTP
**Install NTP client and server**
```sh
dnf install chrony ntpstat
systemctl status chronyd
```

**Check current time lagging**
```sh
ntpstat
```

**Check current time servers ver**
```sh
chronyc sources -v
```

**Edit NTP server configuration**
```sh
vi /etc/chrony.conf
systemctl restart chronyd
```

**Force time sync**
```sh
chronyc makestep
```

chronyd

/etc/chrony.conf
/var/log/chrony.log

systemctl status chronyd

! Only one daemon should be running and syncing NTP servers

**Set keymap temporary**
```sh
loadkeys se
```

**Set keymap persistent**
```sh
localectl set-keymap se
```

## SSH

Configuration /etc/ssh/sshd_config

##Client time out
ClientAliveInterval 600
ClientAliveCountMax 1

-> systemctl restart sshd


**Disable root login
PermitRootLogin no

-> systemctl restart sshd

**Disable Empty Passwords
PermitEmptyPasswords no

-> systemctl restart sshd

**Allow certain users
Allow user1 user2

-> systemctl restart sshd

**Change port
Port 2222

-> systemctl restart sshd




##**Analyze servers and get support
```
sosreport
sos report
```

**Install and access cockpit
```
dnf install cockpit
systemctl enable --now cockpit
https://IP:9090
```



## Package management


**Install certain package**
```
yum install PACKAGE
```

**Remove certain package**
```
yum remove PACKAGE
```

**List all installed packages in the system**
```
rpm -qa
```

**Search for package in the current system**
```
rpm -qa | grep PACKAGE
```

**Installs, verify and get hash of a local package**
```
rpm -ihv PACKAGE.RPM
```

**Remove package**
```
rpm -e PACKAGE.RPM
```

/etc/yum.repos.d/

rpm - locally install package

apt-get - Debian-based

**Check when system was last updated**
```sh
cat /var/log/apt/history.log
rpm -qi --last
```

**Get current version**
```cat /etc/redhat-release```

**Minor upgrade (6.0 -> 6.1), but will preserve current packages**
```yum update```

**Minor upgrade (6.0 -> 6.1), but will remove packages and replace with newer onces**
```yum upgrade```


**Query package for info**
```rpm -qi PACKAGE```

**List configuration files for package**
```rpm -qc PACKAGE```

**Check what executable is assiciatted with what package**
```rpm -qf PACKAGE/FULL PATH TO EXECUTABLE```

**Rename all files ending with specific file extension in the current folder**
```sh
rename -vn currentname newname *.png      # Dry run
rename -v currentname newname *.png       # Actually doing it
```

**Compress and extract files**
```
tar -cvf FILE.TAR ~     # Create an archive from file/folder
tar -xvf FILE.TAR       # Extract an archive

tar -cvzf FILE.TAR.GZ ~     # Create an gzip compress archive from file/folder
tar -xvzf FILE.TAR.GZ       # Extract an gzip compress archive

tar -cvjf FILE.TAR.BZ2 ~    # Create an bz2 compress archive from file/folder
tar -xvjf FILE.TAR.BZ2      # Extract an bz2 compress archive

zip --password MY_SECRET secure.zip file1 file2 file3
unzip secure.zip

7za a -tzip -pMY_SECRET -mem=AES256
7za e secure.zip
```

**Find something (case insensitive) and supress permission issues**
find / -iname nanorc 2>/dev/null

**Do something with results**
```find -iname nanorc | xargs cat```

**Block process termination**
```nohup apt-get update```

## Parsing
### jq (or yq)

**Return all items with the name property**
```sh
command | jq .[].name
command | jq -r .[].name        # raw ouput
```

**Return the first item with the name property**
```sh
command | jq .[0].name
command | jq -r .[0].name       # raw output
```

/etc/vsftpd/vsftpd.conf


anonymous_enable=NO
ascii_upload_enable=YES
ascii_download_enable=YES
ftpd_banner= Welcome to bla bla bla
use_localtime=YES


ftp X.X.X.X
Login
bi = binary mode to transfer files
hash = progress bar
put FILE
bye


**Transfer file to via SSH (SCP)
```scp FILE USER@X.X.X.X:/home/USER```

**NetworkManager
nmcli = cli editor for NetworkManager
nmtui = ncurses editor for NetworkManager


/etc/sysconfig/network-scripts = upstart script for network configuration
/etc/hosts 
/etc/hostname = hostname for machine
/etc/resolve.conf = DNS server for name resolving
/etc/nsswitch.conf = order for name lookup


**Spawn new process with desired priority**
```sh
nice -n <priority> <process>
```

**Change running process priority**
```sh
renice -n <priority> <process>
```

Userspace
    Highest priority = -20
    Lowest priority = 19

System
    Highest priority = -99
    Lowest priority = 39


## SELinux
! Is default enabled in Redhat, CentOS and Fedora
Enforcing = Enabled
Permissive = Disable, but logs the activity
Disable = Disable

**Check SELinux status**
```sh
sestatus
getenforce
```

**Change SELinux mode temporary**
```sh
setenforce 0 = Permissive/Disable
setenforce 1 = Enable
```

**Configuration**
```sh
vi /etc/selinux/config

SELINUX=enforcing

OR

SELINUX=disable
```

**Before enabling SELinux, relabel filesystem**
```sh
touch /.autorelabel                 # May take a long time!
```

**List label of file**
```sh
ls -lZ FILE
```

**List label for process**
```sh
ps axZ | grep -i <process name>

OR

ps efZ | grep -i <process name>
```

**Change SELinux file type context**
```sh
chcon system_u:object_r:shadow_t:s0 /etc/shadow
```

**Restore file type context on folder recursive with verbose mode**
```sh
restorecon -Rv <path to folder>
```

**Change label on folder**
```sh
semanage fcontext -a -t <type context> "<path to directory or file(/.*)?" 
```

**List active SELinux fcontext type contexts**
```sh
semanage fcontext -l
```

**Remove SELinux type contexts**
```sh
semanage fcontext -d -t <type context> "<path to directory or file(/.*)?" 
```

**List SELinux port type contexts and grep for specific service**
```sh
semanage port -l | grep <service>
```

**Add SELinux port type context**
```sh
semanage port -a -t <type context> -p <protocol> <port>
```

**Modify SELinux port type context**
```sh
semanage port -m -t <type context> -p <protocol> <port>
```

**Modify SELinux port type context**
```sh
semanage port -d -t <type context> -p <protocol> <port>
```

**Show SELinux manuals**
```sh
man semanage-fcontext
man semanage-port
man semanage-boolean
```

**List SELinux port type contexts**
```sh
semanage port -l | grep <service>           # Take a note of the port type context for the service
vi /etc/httpd/conf/httpd.conf
semanage port -a -t httpd_port_t -p <protocol> <port>           # Replace -a with a -d to delete port.
systemctl restart httpd
```

**List SELinux boolean**
```sh
semanage boolean -l
semanage boolean -l | grep <search pattern>
```

**Set SELinux boolean**
```sh
setsebool <policy> <boolean>
sestebool -P <policy> <boolean>      # The -P makes the change persistent across reboots.
```

**Troubleshooting SELinux**
```sh
dnf install setroubleshoot setools
sealert -a /var/log/audit/audit.log
```

SELinux
    Labeling



**List label of directory
```
ls -dZ DIRECTORY
```



**List label of socket
```
netstat -tnlpZ | grep PROCESS
```



semanage boolean


**List of boolean
getsebool -a 
semanage boolean -l

**Active or disable boolean
setsebool -P BOOLEANNAME on/off

**Change the type of label
chcon -t TYPE FILE
semanage -t TYPE FILE

/.autorelabel ??

##**Machine operations and target levels
! shutdown and reboot is symbolic links to systemctl

systemctl poweroff = shutdown and power off system
systemctl reboot = shutdown and reboot

**Get current target or run-level**
```sh
systemctl get-default
who -r      # Output current run level
```

**List targets**
```sh
ls -al /lib/systemd/system/runlevel*
```

**Set current target on system**
```sh
systemctl isolate graphical.target      # Normal desktop

OR

systemctl isolate multi-user.target     # No grapical interface
```

**Set default target on system**
```sh
systemctl set-default graphical.target      # Normal desktop

OR

systemctl set-default multi-user.target     # No grapical interface
```

**Comment**
```sh
systemctl default                           # Directly get into default mode
```

**Switch between consoles**
```sh
ALT+Fx              # E.g. ALT+F3
```

**Copy hidden files**
```sh
shopt -s dotglob
cp folder/ ../test

OR

mv * ../test 

shopt -u dotglob
```

**Creating a soft link**
! removing a soft link does not remove the actual data
```sh
ln -s <target> <name>
```

> hard links create another link to the same inode. Does not take up space on the harddrive. A copy is another copy of the data, thus take up space on the harddrive.

**List section of a man page**
```sh
man -f <command>
```

**Search after man page**
```sh
man -k <keyword>
man -k '^<keyword>'         # Searches after man pages that begins with keyword
```
> Other documentation can be found under /usr/share/doc

**Compress and uncompress files with Gzip and Bzip7**
```sh
gzip <uncompressed file>
# gzip -c <uncompressed file> <compressed target>
gzip -d <compressed file> # decompress file, replacing the archive file
gzip -c -d <compressed file> > <uncompressed file> # decompress file to target file

bzip2 <uncompressed file>
bziped -d <compressed file> 
bzip2 -d -c <compressed file> > <decompressed target> # decompress file to target file
```

**search for a specific term within a file**
```sh
cat <file> | grep <search term>
cat <file> | grep -i <search term> # ignore case
OR

grep <search term> <file>
grep -i <search term> <file>        # ignore case
```

**search files for a specific term within the file and output**
```sh
grep -a r -i <search term> .
grep -a r -i --exclude <files e.g. *.iso> <search term> .
```

**Search for a specific term in input and output file, but mark matches**
```sh
cat <file> | grep -z <search term>
```

**Search for a specific term in input and output only three lines after first match**
```sh
cat <file> | grep -A 10 <search term>
```

**ssh command on another server**
```sh
ssh user@machine <command>
```

**ssh command on another server (force password auth.)**
```sh
ssh user@machine <command>
ssh -o PubkeyAuthentication=no -o PreferredAuthentications=password user@machine
```

**copy files between machines using terminal**
```sh
# Copy remote file to local machine
scp user@machine <file>
sftp user@machine <file>

# Copy local file to remote machine
scp <file> user@machine 
sftp <file> user@machine 
```

**List files with permissions as numbers**
```sh
stat -c "%a %n" *
```

**Set permission on file(s) och folder(s)**
```sh
chmod +x        # Add execute to all users
chmod u+w       # Add write to user
chmod g-r       # Subtract read for group
chmod o+x       # Add execute for others
chmod go+x      # Add execute for group and others
```

**Input direction**
```sh
cat <file> > <another file>      # Redirect standard output to another file (overwrite)
cat <file> >> <another file>     # Redirect standard output to another file (append)
cmd 2> <file>                    # Only redirect errors to file
cmd &> <file>                    # Redirect all output to file
cmd > <file> 2> <file2>          # Redirect standard output to file and output errors to file2
```

**Piping**
```sh
ls | wc -l                       # Count output lines
ls | grep <search term>          # Grep after specific file name from ls
ls | sort --reverse              # Reverse ls output 
```

**Remove metadata/EXIF from files**
```sh
exiftool -All= *.jpg
exittool -All= -overwrite_original *.png
```

**Reset root password on Red Hat (when SELinux enabled)**
**Comment**
```sh
edit GRUB using e key
replace "quiet" with "rd.break" under Linux
init=/bin/bash
ctrl+x
mount -o remount,rw /sysroot OR /
(chroot /sysroot)
ls -lZ /etc/shadow
passwd root
ls -lZ /etc/shadow
chcon system_u:object_r:shadow_t:s0 /etc/shadow OR touch /.autorelabel)                                         # You can skip this step if SELinux is not enabled
(exit)
exec /sbin/init
```

**Reset root password on Ubuntu
edit GRUB using e key
After the /swap type:
replace "ro quiet splash $vt_handoff” with “rw init=/bin/bash” under Linux
ctrl+x
mount | grep -w /
passwd root
REBOOT

## Containers
```
dnf install podman
```

**Check podman specs**
```
podman info
```

**Start container**
```
podman run -dt -p 3000:3000 redmine
```

**Generate podman file**
```
podman generate systemd --new --files --name redmine
cp /root/container-redmine.service /etc/systemd/system
systemctl enable container-redmine.service
systemctl start container-redmine.service
```

## NFS
**Server
> ! nosuid
```
dnf install nfs-utils libnfssidmap
systemctl enable rpcbind
systemctl enable nfs-server
systemctl start rpcbind
systemctl start nfs-server
systemctl start rpc-statd
systemctl start nfs-idmapd
mkdir /data
chmod a+rwx /data
Modify /etc/exports
**/data 192.168.12.7 (rw,sync,root_squash)
**/data * (rw,sync,root_squash)
exportfs -rv
```
**Client
```
dnf install nfs-utils rpcbind
systemctl start rpcbind
showmount -e x.x.x.x
mkdir /mnt/data
mount x.x.x.x:/data /mnt/data
df -h
```
## Samba
Server
```
dnf install samba samba-client samba-common
firewall-cmd --add-service=samba --permanent
firewall-cmd --reload
mkdir -p /data2
chmod a+rwx /data2
chmod 770 /data3
chown -R nobody:nobody /data2
chown -R USER:GROUP /data3
chcon -t samba_share_ /data2
Modify configuration for samba at /etc/samba/smb.conf
```
```
[global]
    workgroup = WORKGROUP
    netbios name = centos
    security = user
    map to guest = bad user
    dns proxy = no

[guest_share]
    path = /data2
    browsable = yes
    writable = yes
    guest ok = yes
    guest only = yes
    read only = no

[users_share]
    path = /data3
    valid users = @samba
    browsable = yes
    writable = yes
    guest ok = no
```
```
testparm
```
**Client
```
dnf install cifs-utils samba-client
mkdir -p /mnt/data2
mount -t cifs //192.168.0.10/guest_share /mnt/data2
```

## Shell scripting
**Script parameters**
```sh
# The shell script below contains the following lines:

#       #!/bin/sh
#       echo $1
#       echo $2
#       echo $3

./script.sh orange banana kiwi
$1
$2
$3
```

**Script exit codes**
```sh
if $test
then
    echo 1
    exit 23
else
    echo 2
    exit 24
done

echo $?
```

## Shell
**Get current shell**
```sh
echo $0
cat /etc/passwd | grep YOURUSERNAME
```

**List available shells**
```sh
cat /etc/shells
```

## Networking

### General
**List all processes using one or more ports (TCP and UDP)**
```sh
ss -tulpn
netstat -tupan
```

**Add system-wide proxy**
1. Add the below to `/etc/environment`:

```sh
no_proxy=localhost,127.0.0.0/8,::1,*.domain.com
https_proxy=http://proxy.domain.com:8080/
HTTPS_PROXY=http://proxy.domain.com:8080/
HTTP_PROXY=http://proxy.domain.com:8080/
http_proxy=http://proxy.domain.com:8080/
```
2. Edit proxy for you current desktop environment via respective desktop environment's settings/control 

### Configure network (Red Hat)
**Change DNS resolution order**
```sh
vi /etc/nsswitch.conf
```

```sh
General
man nmcli-examples

nmcli con show PROFILENAME  # Display settings from profile
nmcli con up INTERFACE      # Load new settings from profile

IPv4
nmcli con mod INTERFACE ipv4.addresses IPADDRESS/XX,IPADDRESS/XX        # Primary (or seconday IP address)
nmcli con mod INTERFACE ipv4.gateway IPADDRESS      # Sets gateway
nmcli con mod INTERFACE ipv4.dns IPADDRESS,IPADDRESS        # Sets DNS servers
nmcli con mod INTERFACE ipv4.dns-search DOMAIN      # Sets search domain aka. DNS suffix
nmcli con mod INTERFACE ipv4.method manual      # Set either static (manual) IP address or a address from DHCP (auto).

IPv6
nmcli con mod INTERFACE ipv6.addresses IPADDRESS/XX,IPADDRESS/XX        # Primary (or seconday IP address)
nmcli con mod INTERFACE ipv6.dns IPADDRESS,IPADDRESS        # Sets DNS servers
nmcli con mod INTERFACE ipv6.dns-search DOMAIN     # Sets search domain aka. DNS suffix
nmcli con mod INTERFACE ipv6.method manual      # Set either static (manual) IP address, a address from DHCP (auto) or disabled.

OR

Use nmtui 😉
```
### WIFI
**Scan and connect to WIFI**
```sh
nmcli device wifi list
nmcli --ask device wifi connect "<SSID>"a
```



## Advanced networking
### IP forwarding
```
sysctl -w net.ipv4.ip_forward=1 OR net.ipv6.conf.all.forwarding = 1
```
Permanent save
1. Edit /etc/sysctl.conf
2. Add net.ipv4.ip_forward = 1
3. sysctl -p /etc/sysctl.conf 

## Firewalls
**Overview**
tables
    filter
    mangle
    nat
    raw

chain
    INPUT = incomming traffic
    FORWARD = going to a router, from one device to another
    OUTPUT = outgoing traffic

target
    ACCEPT - accept connection
    REJECT - send reject response
    DROP - drop connection without sending any response

**iptables
#**List iptables rules
iptables -L

#**Remove all iptables rules
iptabels -F
### Configure firewall (Red Hat)
```
firewall-cmd --list-all                             # List all firewall rules
firewall-cmd --get-zones                            # List firewall zones

firewall-cmd --get-active-zone                      # Check current firewall zone
firewall-cmd --zone=public --list-all               # List all firewall rules for the public zone

firewall-cmd --get-services                         # List all firewall appliable services rules
firewall-cmd --add-service=http --permanent         # Add service to the firewall
firewall-cmd --remove-service=http --permanent      # Remove service to the firewall
firewall-cmd --add-port=80/tcp --permanent          # Add port to the firewall
firewall-cmd --remove-port=80/tcp --permanent       # Remove port to the firewall
firewall-cmd --add-icmp-block-inversion             # Block ICMP (ping)
firewall-cmd --remove-icmp-block-inversion          # Allow ICMP (ping)

firewall-cmd --reload                               # Reload firewall rules
firewall-cmd --complete-reload                      # Reload the firewall service, which also terminate active connections
firewall-cmd --runtime-to-permanent                 # Make current configuration permanent
```

### Advanced firewall (Red Hat)
**Add custom service to firewalld**
1. Copy any XML file under /usr/lib/firewalld/services/ and modify it.
2. Restart the firewall:
> systemctl restart firewalld
3. List all services - you should find you newly added service:
> firewall-cmd --get-services
4. Add the service as a rule to the firewall and save it permanently:
> firewall-cmd --add-service=XX --permanent

## Logging

### System logs
**System logs (RedHat)**
```sh
/var/log/boot # Boot events
/var/log/chronyd # NTP events
/var/log/messages # All events
/var/log/secure # Security events
/var/log/cron # Cron events
/var/log/maillog # SMTP events
```

Successful and non-successful login attempts:
```
/var/log/auth.log       # Debian/Ubuntu
         secure.log     # Red Hat/CentOS
```

**Log a specific message to system log files**
```
$ logger -s "Message"     
```

**Log a specific message to Kernel log buffer (useful for dmesg debugging)**
```
# echo "Message" >> /dev/kmsg
```

### Journal
**See journal from last boot**
```sh
journalctl -b -1
```

**Activate persistent storage**
```sh
vi /etc/systemd/journald.conf
Go to [Journal] and add line Storage=persistent
mkdir /var/log/journal
systemctl restart systemd-journald
journalctl --flush
```

## Scheduling
### Cron

**Allow or disallow access to crontab**
> Based on existence of /etc/cron.allow and /etc/cron.deny, user is allowed or denied to edit the crontab in below sequence.
> 
>     If cron.allow exists - only users listed into it can use crontab
>     If cron.allow does not exist - all users except the users listed into cron.deny can use crontab
>     If neither of the file exists - only the root can use crontab
>     If a user is listed in both cron.allow and cron.deny - that user can use crontab.
> 

source: https://www.thegeeksearch.com/how-cron-allow-and-cron-deny-can-be-user-to-limit-access-to-crontab-for-a-particular-user/

```sh
echo USER >>/etc/cron.allow      # Allow specific user(s) to use crontab
echo ALL >>/etc/cron.deny       # Deny all users from using crontab except those in cron.allow
```

**List available options**
```sh
man 5 crontab
```

**Edit crontab for current user**
```sh
crontab -e
```

**Edit crontab for another user**
```sh
crontab -e -u <user>
```

**List crontab for current user**
```sh
crontab -l
```


### At
**Schedule command to run at specific time**
```sh
<command> | at <time>
<command> | at <time> <date>
<command> | at now +1 hours            # Start specific command about 1 hour
<command> | at <time> -M               # suppress email notification
```

**Enable or disable the atd service**
```sh
systemctl enable/disable atd
```

**Allow or disallow access to at**
```
echo USER >>/etc/at.allow      # Allow specific user(s) to use at
echo ALL >>/etc/at.deny       # Deny all users from using at except those in at.allow
```

## Performance tuning
### Tuned
**List tuned profiles**
```tuned-adm list```

**View current tuned profile active**
```tuned-adm active```

**Active tuned profile**
```tuned-adm profile powersave```

**Disable tuned profile**
```tuned-adm off```

**Get tuned profile recommendation**
```tuned-adm recommend```

## Storage
### Generic
```
lsblk                # List block devices and mount points
df -h                # List volumes with sizes and mount points
blkid                # List UUID for block devices
fdisk -l <device>    # list partitions of a device
```

**Quick format, create a single partition and format it to EXT4**
```sh
lsblk                       # 1. List disk
fdisk <path to device>      # 2. Open disk in fdisk
g OR s                      # 3. Choose partition table. g for GPT and s for DOS (MBR)
n -> ENTER -> ENTER -> ENTER                 # 4. Create a new partition
t -> L -> xx -> ENTER       # 5. Set the partition type
w                           # Write changes

q                           # Quit without making changes

mkfs.ext4 <path to device x>        # Quick format the disk
mkdir /data
mount <path to device x> /data
```

**Label volume**
```sh
e2label <device>
```

**Mount a device with specific label**
```sh
mount -L <label> <mount point>
```

**Resize partition and filesystem**
```sh
resize2fs <path to device x>
```

**Format partition to XFS**
```sh
mkfs.xfs <path to device x>
```

**Mount disk upon boot**
```
Edit /etc/fstab

UUID=<uuid> <mount point>   <file system>   <defaults> <0> <0>              

# ext4, vfat (for FAT and FAT32),swap, udf (DVD), iso9660 (CD) and xfs. For more information, see https://wiki.archlinux.org/title/Fstab.
```

**Check integrity on disk**
```sh
e2fsck -f <device>
```

**Interactive partitioning tool**
```sh
cfdisk <block device path>
```

### SWAP
```
swapoff -a                                      # Disable all SWAP devices
```

### Mounting
```
mount -o loop /PATH/TO/ISO /MOUNTPATH           # Mount a ISO image on desired path
mount -a                                        # Remount all entries in /etc/fstab
mount -o remount,rw /                           # Remount /
```

### LVM
Physical volume (PV) = One or more block devices that makes up a volume group (VG)
Volume group (VG) = A volume group (VG) contains one or more logical volumes (LV)
Logical volume (LV) = Logical partition (LV) that can be formatted with a file system such as e.g. EXT4.

**Create LVM, physical volume, volume group and logical volume, format volume and resize**
```sh
pvcreate <path to block device>
vgcreate <name of volume group> <path to block device> <path to block device>                                    # Create new VG on block device - a PV is automatically created 
lvcreate -L <sizeXX> -l <100%FREE> -n <name of logical volume> <name of volume group>             # Create new LV with 50 GB size
lvresize -L <sizeXX> /dev/mapper/<name of volume group>-<name of logical volume>       # Resize LV to new size - add -r to resize underlaying file system
mkfs.ext4 /dev/mapper/<name of volume group>-<name of logical volume>                     # Create a ext4 file system on the new LV
resize2fs /dev/mapper/<name of volume group>-<name of logical volume>                     # Resize underlaying file system
```

**Remove LV**
```sh
lvremove <volume group>/<logcial volume>
```

**Remove VG**
```sh
vgremove <volume group>
```

**Remove PV**
```sh
pvremove <path to block device>
```

**Resize LV**
```sh
lvresize <path to mapped device> -l +100%FREE -L +10GB
```

1. create physical partition
fdisk
n
t 8e

2. create physical volume
pvcreate /dev/sdb1 
pvcreate /dev/sdc1

3. 
vgcreate LOGICALNAME /dev/sdb1
OR
vgcreate LOGICALNAME /dev/sdb1 /dev/sdc1


lvcreate --name LOGICALNAME -l +100%FREE LOGICALNAME
mkfs.ext4


vgextend LOGICALNAME /dev/sdc1
lvextend -l +100%FREE LOGICALNAME
resize2fs                                           # xfs_growfs <path to device> when XFS


lvcreate -n data1_lv -l +100%FREE data1_vg
#########################################################3

### LVMVDO
> The topic is written for Red Hat 9 in mind - some commands won't work!
> You can use both /dev/device OR /dev/vg*/lv*/
**Create **
```sh
vgs
vgcreate <volume group name> <block device path>
lvcreate --type vdo -n <logical volume name> -L <physical size> -V <logical size> <volume group> # Use exact size e.g. 20 GB
lvcreate --type vdo -n <logical volume name> -l <physical size> -V <logical size> <volume group> # Use extent instead e.g. +100%FREE
mkfs.ext4 -E nodiscard <path to volume>
mkfs.xfs -K <path to volume>
```

**Show VDO stats**
```sh
lvs -olv_name,vdo_compression,vdo_deduplication
vdostats --human-readable
```

**Change setting on vdo**
```sh
lvchange --compression n <path to volume>
lvchange --deduplication y <path to volume>
```

### Stratis

> Stratis volumes will always show 1 TB when eg. running df
> Uses thin provision as default

**Install Stratis**
```sh
dnf install stratis-cli stratisd
systemctl enable stratisd --now
```

**Create Stratis pool**
```sh
stratis pool create <pool name> <block device>
```

**List Stratis pools**
```sh
stratis pool list
```

**List Stratis filesystem**
```sh
stratis filesystem list
```

**Create Stratis filesystem on pool**
```sh
stratis filesystem create <pool name> <filesystem name>
```

**Enable or disable overprovisioning**
```sh
stratis pool overprovision <yes OR no>
```

**Add more block devices to pool**
```sh
stratis pool add-data <pool name> <block device>
```

**Create snapshot of Stratis filesystem**
```sh
stratis filesystem snapshot <pool name> <filesystem name> <snapshot name>
```

**Remove snapshot of Stratis filesystem**
```sh
stratis filesystem destroy <pool name> <snapshot name>
```

**Mount**
```sh
mkdir -p <mount point>
mount /dev/stratis/<pool name>/<filesystem name> <mount point>
```

**Mount (persistent)**
```sh
vi /etc/fstab
UUID="<UUID>" <mount point> xfs defaults,x-systemd.requires=stratisd.service 0 0
```



## Boot
### Boot options
```
systemctl get-default                       # Get current setting
systemctl set-default graphical.target      # Set to GUI
systemctl set-default multi-user.target     # Set to CLI
```

### Grub2
**Access Grub during boot**
```sh
F8

OR

Pressing ESC during boot

OR

Holding SHIFT during boot
```

**Boot into a specific target**
> Do not attempt to boot in to emergency or rescue mode without root password - you will be stuck otherwise.

```sh
Go into Grub menu during boot
Press e to edit
at the end of the linux line, type: systemd.unit=emergency.target
Then CTRL+X to start
```

```sh
cat /proc/cmdline

vi /etc/default/grub
grub2-mkconfig -o /boot/grub2/grub.cfg                # BIOS
grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg       # UEFI
```

**Output available kernels**
```sh
grubby --info ALL
```

**Comment**
```sh
grubby --set-default <path to kernel>
```

**Last selected kernel becomes default**
```sh
vi /etc/default/grub
add 
    GRUB_DEFAULT=saved
    GRUB_SAVEDEFAULT=true
```

## SSH
**Generate new key pair**
```
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -C "<comment>"
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -C "<comment>"
```

**Copy you keys to a remote computer**
```
ssh-copy-id USER@192.168.1.120 -i .ssh/id_rsa.pub
```

## NFS and autofs
**Create a folder and share it using NFS**
```sh
(dnf install nfs-utils)         # Or whatever package manager you're using.
mkdir -p /exports/<folder>
vi /etc/exports -> /export/<folder>  <IP range/address to allow or just * to allow all>(rw,sync,no_root_squash)
(systemctl enable --now nfs-server)
(systemctl status nfs-server)
showmount -e
firewall-cmd --add-service nfs --permanent
firewall-cmd --reload
```

**Mount a NFS share**
```sh
(dnf install nfs-utils)         # Or whatever package manager you're using.
vi /etc/fstab -> <IP address or hostname of NFS server>:/exports/<folder>  /mnt/<folder>   nfs rw 0 0
mount -a
```

**Set up autofs (direct) on client machine**
```sh
dnf install autofs
vi /etc/auto.master --> add /- /etc/auto.direct
vi auto.direct --> add /mnt/<server> -rw,soft <server>:/<share>
systemctl enable --now autofs
```

**Set up autofs (indirect, in this case users home directories) on client machine**
> Permissions are matched using the user's id. Create the folder on the source machine and change the ownership to a ID that matches the user accessing the share.
```sh
dnf install autofs
vi /etc/auto.master --> add /mnt/home /etc/auto.home
vi auto.home --> add * -rw,soft,timeo=5 <server>:/home/&
systemctl enable --now autofs
```

## Misc

### Popd/Pushd
**Add folder to stack**
```sh
pushd <PATH>
```

**Show current stacks (verbose)**
```sh
dirs -v
```

**Go to specific stack**
```sh
pushd +<POSITION OF STACK>
```

**Remove folder from stack**
```sh
popd -<POSITION OF STACK>
```

**Exit current stack**
```sh
popd
```

### Snippets
**Find process group id and set priority to minimum**
```sh
read processname; for x in $(pgrep $processname); do echo $x; renice -n 19 -p $x; done;
```

**List only certain directories**
```sh
ls /etc/kubernetes/{pki,manifests}
```

**Read a file in a script - line by line**
```sh
while IFS= read -r x;
do
  echo $x
done < <file>
```

**Case in script**
```sh
#!/bin/sh
case "${1}" in
    square)     echo "You specified 'square'." ;;
    circle)     echo "You specified 'circle'." ;;
    triangle)   echo "You specified 'triangle'." ;;
    *)          echo "Unknown shape '${1}'."; exit 1 ;;
esac
```

**Trim content with tr**
```sh
$x | tr "x,x,x,x" " "           # Replace characters with whitespace char.
$x | tr -d "x,x,x,x"            # Delete characters
```

**Check if directory exists**
```sh
if [ -d "$DIRECTORY" ]; then
  echo "$DIRECTORY exist."
fi
```

**Check if file exists**
```sh
**Check if specific file exists**
if [ -f "$FILE" ]; then
  echo "$FILE exist."
fi
```

**Check if variable is empty**
```sh
if [ -z "$VARIABLE" ]; then
  echo "$VARIABLE is empty."
fi
```

**Search for test in files and retur with matches (line by line)**
```sh
grep <search pattern> <file>
grep -n <search pattern> <file>         # Show which rows
```

**Search for all files with are 3M big and copy it to certain folder**
```sh
find / -type -f -size 3M -exec cp -r {} <path> \;
```

**Search for all files in the specified folder (recursive)**
```sh
find <path> -iname <search pattern>       
find . -iname <search pattern>          # Current folder
```

**Search for all files owned by a specific user in the specified folder (recursive)**
```sh
find <path> -iname <search pattern> --user <username or user ID>   
```

**Search for all files in the specified folder (not recursive)**
```sh
find <path> -maxdepth 1 -iname <search pattern>          # Only search within the specifed
```

**DNS lookup with dig**
```sh
dig <domain name> <type> +noall +answer         # Only output the DNS records and their respective values
```

**Generate password**
```sh
cat /dev/urandom | tr -dc 'a-zA-Z0-9' | head -c 20              # Generates as password that contains a-zA-Z0-9 and with a length of 20 characters.
```

### OpenSSL

**Generate CSR**
```sh
openssl req -new -newkey rsa:4096 -nodes -keyout <domain>.key -out <domain>.csr
```

**Generate self-signed certificate (valid for one year)**
```sh
openssl req -x509 -newkey rsa:4096 -keyout <domain>.key -out <domain>.crt -sha256 -days 365
```

**Check certificate chain for server**
```sh
openssl s_client -connect <host>:443
```

### Git

**Configure Git (locally)**
```sh
Create a local config
git config --local user.name "<nickname or fullname>"
git config --local user.email "<email address>"
git config --local core.sshCommand 'ssh -i ~/.ssh/<private key file>'
```

**Revert last commit without removing any changes**
```sh
git reset --soft HEAD~1

OR

git reset --soft <hash of commit>
```

**Revert last commit and changes made since last commit (POTENTIALLY DANGEROUS!)**
```sh
git reset --hard HEAD~1

OR

git reset --hard <hash of commit>
```

**Remove unstaged files**
```sh
git reset @
```

**Add all changes to staged**
```sh
git add *
```

**Add all changed to staged including deleted files**
```sh
git add --all .
```

**Force push**
```sh
git push --force
```

**Change commit author**
```sh
git commit --amend --reset-author
```

**Commit with comment**
```sh
git commit -m "Message"
```

**Show commits**
```sh
git log --name-only
```

### Vim
**List newline**
```
:set list
```

## Container

**Search for a container image**
```sh
podman search <container name>
```

**Edit list of container registries**
```sh
vi /etc/containers/registries.conf                  # By changing order of registries, you can prioritize which one that should come first respective come last.
```

**Pull image**
```sh
podman pull <address to the image>
```

**Login to container registry**
```sh
podman login <url to registry>

OR

skopeo login <url to registry>
```

**Inspect container image**
```sh
podman pull <address to the image>
podman inspect docker://<url to image> | less

OR

skopeo inspect docker://<url to image> | less


podman inspect docker://<url to image> | grep -A2 Cmd           # List container entrypoint command
```


**List containers**
```sh
podman ps               # List running containers
podman ps -a            # List all container
```

**List container images**
```sh
podman images
```

**Run a container**
```sh
podman run --name <container name> <image id or url>
podman run --name <container name> -d -p <host port:container port> <image id or url> -e <environment variable>="<value>"
podman run --name <container name> <image id or url>             # Run the container in background (detached)
podman run -it --name <container name> <image id or url> <cmd>      # Run container interactively 
```

**Remove all containers**
```sh
podman rm -a
podman rm -a -f          # Remove all container regardless of state.
```

**Start, stop or kill container**
```sh
podman start <container name or id>
podman stop <container name or id>
podman kill <container name or id>
```

**Execute commands interactively inside container**
```sh
podman exec -it <container name or id> <cmd>
```

**Expose container ports**
```sh
podman run
```

**Run a container with persistent storage**
```sh
mkdir <container persistent storage>
podman run --name  -d -p 4080:8080 -v <path to dir:container path>
podman run --name  -d -p 4080:8080 -v <path to dir:container path:z>
podman run --name  -d -p 4080:8080 -v <path to dir:container path:Z>
```

**Create a container file/Docker file**
```sh
mkdir <container name>
cd <folder name>
vi container-file

    FROM registry.redhat.io/ubi9/ubi-minimal:9.1.0

    RUN microdnf install -y nginx

    RUN rm -r /usr/share/nginx/html/*

    COPY index.html /usr/share/nginx/html/

    COPY startup.sh /

    EXPOSE 80

    CMD /startup.sh


vi index.html

    <h1>Hello!</h1>

vi startup.sh

    #!/bin/bash
    
    exec /usr/sbin/nginx -g "daemon off;"

chmod +x startup.sh

podman login

podman build . -t <container image:release name>

podman images

podman run
```

**Enable docker container to start without user have been logged on**
```sh
loginctl show-user <user>
sudo loginctl enable-linger <user>
mkdir -p ~/.config/systemd/user
cd ~/.config/systemd/user
podman generate systemd --name <container name> --files --new 
systemctl daemon-reload --user
systemctl list-unit-files --no-pager --user | grep container-<container name>
systemctl --user status container-<container name>
systemctl --user start container-<container name>
systemctl --user enable container-<container name> --now
```




docker compose logs -f <container name>

**Access something through SSH host**
1. Connect and setup port forward to remote machine:
```sh
ssh -L 9443:<host>:443 <user>@<remote machine>
```
2. Leave the terminal open.
3. Access the host on `https://localhost:9443`.
4. Close the terminal to the remote machine to remove the port forward.

> Pro tip! Edit /etc/hosts and add e.g. `127.0.0.1     host`. When you now access the website with https://\<hostname\>:9443, it should be trusted (if required CAs are installed).

**Setup up local SOCKS proxy to remote machine via SSH**
> Dont forget to remove all other proxy settings before you proceed.
1. Connect and setup SOCKS proxy to remote machine:
```sh
ssh -D 10800 <user>@<remote host>`
```
2. Leave the terminal open.
3. Set SOCKS proxy to `127.0.0.1:10800` in browser, app or export environment variable with it like this:
```sh
export http_proxy="socks5://127.0.0.1:10800"
export https_proxy="socks5://127.0.0.1:10800"
```
4. Access the desired host as normal
5. Close the terminal to the remote machine to remove the SOCKS proxy.




