# LFCS Simulator

## Kernel and System Info

``/etc/sysctl.conf`` to permanantly edit kernel values  
``sysctl -p`` to load default configuration file  
Get kernel release: ``uname -r``  
Get ``ip_forward`` Kernel parameter: ``cat /proc/sys/net/ipv4/ip_forward``  
Get system timezone: ``date +%Z``  

## Cronjobs

Explanation of a cronjob:
```
minute hour day of the month month day of the week user command  
*       *          *           *          *        root    ls  
```
``crontab -l`` to list them  
``crontab -e`` to edit them  
``/etc/crontab`` is the config file for system-wide cronjobs

## Time synchronisation Configuration (NTP)

### ``man timesyncd.conf`` for help 

``/etc/systemd/timesyncd.conf`` is the config file for local time and timezone  
``sudo service systemd-timesyncd restart`` to restart the NTP (Network Time Synchronization) service  
``sudo grep systemd-timesyncd /var/log/syslog`` to check NTP logs about NTP modification

## Environment Variables

``VARIABLE="value"`` to create a variable only available in the script  
``export VARIABLE2="${VARIABLE}-extended"`` to create a variable available in the script and all child processes of the shell

## Archives and Compression

### ``man tar``, ``man bzip2`` and ``man gzip`` for help

``bunzip2 -k import001.tar.bz2 `` allow to extract a `bzip2` archive and received an uncompressed `tar` archive  
``gzip --best import001.tar`` create a `gzip` archive

To compare both archives and to be sure they contain the same files and structure, we run the following commands  
```bash
tar tf import001.tar.bz2 | sort > import001.tar.bz2_list
tar tf import001.tar.gz | sort > import001.tar.gz_list
```  

To compare both archives hashes
```
cat import001.tar.bz2_list | sha512sum
cat import001.tar.gz_list | sha512sum
```  

## User, Groups and Sudoers

``usermod -d [HOMEPATH] [USER]`` to change user's home directory  
``usermod -g [GROUP] [USER]`` to change user's group  
``useradd -s [SHELLPATH] -m -d [HOMEPATH] -G [GROUPS1,GROUPS2,...] [USER]`` to create a new user with a defined login shell, a new created home directory and added to groups  
``visudo`` to edit `/etc/sudoers` file  
``[USER] ALL=(root) NOPASSWD: [SHELL] [BASHFILE]`` to allow a nonroot user to execute a root file without asking for a password

## Network Packet Filtering

### ``man iptables`` is helpful  


``curl [SERVERNAME]:[PORT]`` to test ports availability  
``iptables -L`` to view existing iptables rules and interfaces  
``iptables -L -t nat`` to view existing iptables rules and interfaces of `nat` table  
``ip a`` to show all addresses  
``iptables -A INPUT -i eth0 -p tcp --dport [PORT] -j DROP`` to close a port  
``iptables -A PREROUTING -i eth0 -t nat -p tcp --dport [PORT] -j REDIRECT --to-port [PORT]`` to perform some NAT for connections on a specific port (with a redirection)  
``iptables -A INPUT -i eth0 -p tcp --dport [PORT] -s [ADDRESS] -j ACCEPT`` to open a port from a specific source address  
``iptables -A OUTPUT -d [ADDRESS] -p tcp -j DROP`` to drop outgoing packages from a specific destination address

## Disk Management
``sudo fdisk -l`` to list existing disks  
``lsblk -f`` to list existing disk with a format  
``df -h`` is an alternative useful to easily check disks spaces
  
``sudo mkfs -t ext4 /dev/[DEVICENAME]`` to create an `ext4` filesystem  
To mount the filesystem to a required location
```bash
sudo mkdir /mnt/[MOUNTNAME]
sudo mount /dev/[DEVICENAME] /mnt/[MOUNTNAME]
```
``sudo touch /mnt/[MOUNTNAME]/[FILENAME]`` to create a file inside the mounted filesystem  
``sudo rm -rf /mnt/[MOUNTNAME]/.trash/*`` to clear up disk space on a specific mounted filesystem  
  
``top -b | grep [PROCESSNAME]`` or ``ps aux | grep [PROCESSNAME]`` to compare processes with a specific name  
``df -h | grep [MOUNTPATH]`` to see the disk and mount point  
``sudo umount [MOUNTPATH]``  to unmount a disk  
If the `target is busy`, ``sudo lsof | grep [MOUNTPATH]`` to see open files in a specific disk  
``sudo kill [PID]`` to end a process

## Find files with properties and perform actions

### ``man find`` to help  

``find -exec echo {} \;`` to find all files and runs `echo FILE` for each  
``find -exec echo {} +`` to find all files and runs `echo FILE1 ... FILEX`  
``find ! -newermt "YYYY-MM-DD HH:MM:SS" -type f -exec rm {} \;`` to find all files created before a specific date and delete them  
``find -maxdepth 1 -size -3k -type f -exec mv {} ./[SUBFOLDER] \;`` to find all files having a size less than 3Kib (3,072 Kb) and move them into a subfolder  
``find -maxdepth 1 -size +10k -type f -exec mv {} ./[SUBFOLDER] \;`` to find all files having a size more than 10Kib and move them into a subfolder  
``find -maxdepth 1 -perm 777 -type f -exec mv {} ./[SUBFOLDER] \;``
to find all files with too open permissions and move them into a subfolder

## SSHFS and NFS

### ``man sshfs`` and ``man exportfs`` are helpful  

``sudo apt install nfs-kernel-server`` to install NFS  

To create the SSHFS mount
```bash
sudo mkdir -p [LOCALPATH]
sudo sshfs -o allow_other,rw [SERVERNAME]:/[MOUNTDIRECTORY] [LOCALPATH]
```
``service --status-all | grep nfs`` to find nfs service  
``service nfs-kernel-server status`` to check nfs service status  
``sudo vim /etc/exports`` to edit exported filesystems to NFS clients  

Example of content in `/etc/exports`
```bash
/nfs/share [IP_ADDRESS]/24(ro|rw,async|sync,no_subtree_check,no_root_squash)
```

``sudo exportfs -ra`` to run after adding exports in `/etc/exports`  
``showmount -e`` to see if the mount was done  


## Docker
``sudo docker ps`` to list all Docker containers  
``sudo docker stop [CONTAINERNAME]`` to stop a Docker container  
``sudo docker inspect [CONTAINERNAME] | vim -`` open the inspected JSON Format container configuration  
``sudo docker run -d --name [CONTAINERNAME] --memory [SIZE] -p [LOCALPORT]:[CONTAINERPORT] [IMAGENAME]:[IMAGEVERSION]`` to build and run a detached docker image with a specific name, specific memory size, specific local and container ports from a specific image and its version

## Git Workflow
``git clone [SOURCE] [DEST]`` to clone a GIT repository to a specific location  

## Runtime Security of processes
``ps aux | grep [PROCESSNAME]`` list processes having a specific name  
``strace -p [PID]`` to investigate the kernel syscall of a specific process

## Output redirection
``>`` to redirect standard output  
``2>`` to redirect error output  
``>>`` to append standard output  
``2>>`` to append error output  
``$?`` to get the exit code  

## Build and install from source (To work)
- Check the helper of the executable file  
- Check if there is a Makefile to execute make or make install command
- Check if it is installed with the `whereis` command

## LoadBalancer
To create a load balancer, you need to copy an existing application file located in `/etc/nginx/sites-available` and edit the copied file by adding the following content
```
server {
    listen [WANTEDPORT] default_server;
    listen [::]:[WANTEDPORT] default_server;

    server_name _;

    location / {
        proxy_pass http://[ADDRESS]:[PORT]/[ROUTE];
    }
}
```

Create a symlink from ``/etc/nginx/sites-available/[FILENAME]`` to ``/etc/nginx/sites-enabled/`` by running the following command ``ln -s /etc/nginx/sites-available/[FILENAME] /etc/nginx/sites-enabled/`` and edit again the LoadBalancer by adding the second part
```
upstream backend {
    server [APP1-ADDRESS]:[APP1-PORT];
    server [APP2-ADDRESS]:[APP2-PORT];
}

server {
    listen [WANTEDPORT] default_server;
    listen [::]:[WANTEDPORT] default_server;

    server_name _;

    location / {
        proxy_pass http://backend;
    }
}
```
``sudo service nginx restart`` to restart nginx

## OpenSSH Configuration
``vim /etc/ssh/sshd_config`` to edit SSH config file  
``service ssh restart`` to restart SSH service
```bash
Match User|Group [USERNAME]|[GROUPNAME]
    ...
    Banner /etc/ssh/sshd-banner
```

## LVM Storage
```
PV = Physical Volume
VG = Volume Group
LV = Logical Volume
```
``sudo pvs`` to look at all PVs  
``sudo vgs`` to look at all VGs  
``sudo lvs`` to look at all LVs  
``sudo lvmdiskscan`` to get an overview over all system disks and their LVM usage  
``sudo vgreduce [VGNAME] /dev/[DEVICENAME]`` to remove a device/disk from a specific volume group  
``sudo vgcreate [VGNAME] /dev/[DEVICENAME]`` to create a volume group to a specific device/disk  
``sudo lvcreate --size [SIZE] --name [LVNAME] [VGNAME]`` to create a specific logical volume from a specific volume group  

## Regex, filter out log lines

### ``man grep`` and ``man sed`` are helpful

``cat [LOGFILE] | grep -E [REGEXPATH]`` to find a pattern with a specific regex  
``sed 's/^container.web.*Running.*24h$/SENSITIVE LINE REMOVED/g' [FILE]`` to find a pattern with a spec

## User and Group limits

### ``man ulimit`` is helpful

``ulimit -a`` to check out user's limits  
``ulimit -u`` to list the max user processes limit  
``ulimit -S -u 1100`` to change the max user processes limit  

``vim /etc/security/limits.conf`` to open limits configuration  
