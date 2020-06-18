# Linux Command Cheat Sheet  
## All the credits goes to [Linux Journey](https://linuxjourney.com/)

### Install VMware Tools
	
	sudo mount /dev/sr0 /mnt/cdrom  
	
	cd /mnt/cdrom/  
	
	cp VMwareTools-10.3.10-13959562.tar.gz /tmp/  
	
	cd /tmp  
	
	tar -zxvf VMwareTools-10.3.10-13959562.tar.gz  
	
	cd vmware-tools-distrib/  
	
	./vmware-install.pl  
	
	sudo ./vmware-install.pl  
	
	reboot now
	

### Update Operative System  

#### CentOS

	yum update -y

#### Ubuntu

	apt update
	
	apt upgrade
	
	apt autoremove (cleanup after updates installation)

**You can run first and second commands at once using:**  
	
	apt update && sudo apt upgrade -y
	
### Reboot: Restart a Linux Box

	reboot now

### Shutdown: Turn off a Linux Box

	shutdown -P now

**WARNING: Once you shutdown a Linux Box you won't be able to connect to it again until someone powers it on**

### Configure Linux HOSTNAME

	vi /etc/hostname
	
	vi /etc/hosts


### See Server Hostname

	hostname
	
### Get current user

	whoami
	
### USer extended information

	id
	
Response: uid=1001(matthew) gid=1001(matthew) groups=1001(matthew)
	
### Get current Time and date

	date
	
And the result you will get is: time currentTimeZone year
	
### Configure TimeZone

Diplay current time / timezone settings:
	
	timedatectl
	
List Available Time-Zones:
	
	timedatectl list-timezones
	
	timedatectl set-timezone America/New_York
	

### Start Service

The new way:

	systemctl start httpd
	
### Stop Service

	systemctl stop httpd

### Service Status

	systemctl status httpd

### Restart network service

	systemctl restart networking
	
### Configure service to start automaticallky on every boot

	systemctl enable httpd
	
### To disable the service at bootup

	systemctl disable httpd

### To configure your App as a Service:

Go to the sytemd path

	cd /etc/systemd/system
	
create a file named:

	my_app.service
	
**The name used here is the name that you later will use with the systemctl command**

my_app.service command should have at least the following:
	
	[Unit]
	Description=My python web application
	
	[Service]
	ExecStart=/usr/bin/python3 /opt/code/my_app.py
	ExecStartPre=/opt/code/configure_db.sh
	ExecStartPost=/opt/code/email_status.sh
	Restart=always
	
	
	[Install]
	WantedBy=multi-user.target
	
**The [Install] section of the file makes possible for this service to startup automatically at boot time**

**The ExecStartPre and ExecStartPost are tasks that will run before or after you service has started**

Let systemd know that there is a new service configured, using:

	systemctl reload

And then finally, start/stop/status/enable your new service

	systemctl start my_app

### Networking Configurations  

vi /etc/network/interaces

	auto eth1
	iface eth1 inet static
	address 192.168.72.8
	netmask 255.255.255.0
	gateway 192.168.72.1
	dns-nameservers 8.8.8.8 4.4.2.2

Add a Temporary IP Address (This configuration will be LOST after the server reboots):

	ip addr add 10.102.66.200/24 dev enp0s25
	
Add a Temporary Default Gateway (This configuration will be LOST after the server reboots):

	ip route add default via 10.102.66.1
	
Or you can use

	ip route add 0.0.0.0 via 10.102.66.1
	
Bring UP or DOWN an Interface (where the interface name is enp0s25):

	ip link set dev enp0s25 up
	ip link set dev enp0s25 down

To See IP address configured on interfaces:

	ip a
	ip addr show
	
To See the default route:

	ip route show

To configure a temporary DNS Server (It will modify /etc/resolv.conf file. It is NOT A PERSISTENT configuration)

	nameserver 8.8.8.8
	nameserver 4.4.4.4
	
### Packet Forwarding

Add a Temporary Default Route (This configuration will be LOST after the server reboots):

	ip route add 192.168.10.0/24 via 10.102.66.2
	
To Temporary Allow a Linux Box to route packages between two different networks:

	Go to: /proc/sys/net/ipv4 and change the value to "1"
	
To Permanently Allow a Linux Box to route packages between two different networks:

	Go to: /etc/sysctl.conf and modify the parameter "net.ipv4.ip_forward = 1"
	
### DNS basics

Add local DNS registers

	cat >> /etc/hosts or vi /etc/hosts

	192.168.1.11	db
	
	
Add NameServers (DNS Server)

	cat >> /etc/resolv.conf or vi /etc/resolv.conf	
	
	nameserver 192.168.1.100
	
Add Search Domains

	search mycompany.com othercompany.com

	
### To ssh into another box

	ssh user01@192.169.10.27
	
### Enable SSH Service

#### Ubuntu

	apt update
	apt install openssh-server
	
	systemctl status ssh (Check if service is running)
	
	ufw allow ssh (Firewall utility to allow ssh into the box)
	
### MAN

Prints the manual of a command

	man lvcreate
	
Press G (uppercase) to reach the end of a document

Press g (lowercase) to reach the beginning of the document

Write "/text_to_search" for searching text, press "n" for next ocurrence


When you don't know the command but you have an idea of what to do, you can search for the manual like this:

In the example Im want to create a user (So I should probably be root, that why Im looking for section 8)
Then in order to filter even further my results, I apply a second filter | grep create 

	man -k user | grep 8 | grep create
	
To update the man database run:

	mandb


### CD: Change Directory

#### Traverse directly to the root of the FileSystem 

	cd /

#### Traverse directly to the User´s Home Folder

	cd ~

#### Traverse directly to the last folder direcyory you were in

	cd -

### PWD: Present Working Directory

	pwd

### LS: List Hidden files ~ all

	ls -la

#### List Folders and subfiles RECURSIVELY

	ls -R

#### List Files and Folders in alphabetically reverse order
	
	ls -lr

#### List Files and Folders in reverse order by LAST MODIFICATION Time
	
	ls -lt
	
#### Copy files from upper level directory to current

	cp ../file* .
	
Where the dot "." means to current folder


### Run multiple commands

To run multiple commands in one line seprate them using a semicolog ;

	cd Documents; mkdir newFolder; pwd

### Create an empty file
	
	touch example.txt
	
**This command may be also used to update the timestamp of the file**

### See last users who have logged in into the system

	last


### MKDIR: Create Directory

	mkdir name_of_folder
	
	
#### Create subdirectories at once using the '-p' flag

	mkdir -p Books/Sheets

### Using NANO, a very basic Text Editor in Linux

	nano example.txt

* COPY: ALT + 6  
* PASTE: CRTL + U
* CRTL + X: To exit

### LOCATE a file

	locate adduser
	
If new files have been recently added and not yet indexed, you can use the following command to force the index to update

	updatedb


### CLEAR: Clear Screen  

	clear
	
You can also use

	CTRL + L

	
### FILE: Get file content/type explanation

	file myCommands.txt  
	myCommands.txt: UTF-8 Unicode text

	file index.html  
	HTML document, ASCII text

	file pic02.jpg  
	pic02.jpg: JPEG image data, JFIF standard 1.01, resolution (DPI), density 72x72, segment length 16, Exif Standard: [TIFF image data, big-endian, direntries=11, manufacturer=Apple, model=iPhone 6s, orientation=upper-left, xresolution=162, yresolution=170, resolutionunit=2, software=10.1.1, datetime=2016:12:02 11:10:20, GPS-Data], baseline, precision 8, 3218x4291, frames 3
	

### Download files

	wget http://www.some-site.com/report.pdf
	
### TYPE

It will tell you how a word will be interpreted by bash and its location

	type wget
	
	wget is hashed (/usr/bin/wget)

### DELETE: Delete File or Folder

	rm -f example02.txt  (To delete a File)
	
	rm -rd Presentations  (To delete a Folder and any folders included inside of it)

**WARNING: the -r flag will delete all of the files contained within the Folder**


### CAT: For Reading SHORT Content
	
	cat example.txt
	
	
#### For concatenating files

	cat example01.txt exmaple02.txt
	
#### For Adding contents to file

	cat > new_file.txt
	
	CTRL + D  to exit outta the prompt

### LESS: For viewing LONG Files as pages

	less veryLongFile.txt  

*Press Space to keep reading and 'q' to exit

### HISTORY: To see all the commands that habe been input into the system

	history

#### To run the exact last command without typing it againg or using the up arrow, use:

	!!
	
#### To run a specific command from history

	history
	
Find line number of the command you want to use, and then run:

	!146

#### Send your history to a file using stdout

	history > myCommands.txt
	
#### Reverse Search History in commands

	CTRL + R
	
And enter a search term

### TREE

To have a nicer view of folder and files structure

	sudo yum install -y tree
	
To display, run the following command:
	
	tree

### COPY

#### From current directory to /tmp folder

	cp file01.txt /tmp/file01.txt

#### From specified source directory to /tmp folder

	cp ~/Presentations/file01.txt /tmp/file01.txt

#### Using recursive copy (To include files and folders within the source

	cp -r Documents/ Downloads/

#### Copy all files to destination - * is a wildcard

	cp -r * ~/Downloads/

#### Use the -i flag to make an INTERACTIVE COPY - It will ask you to confirm before overwritting any existing file or folder

	cp -ir * ~/Downloads/
	
#### Copy files that start with a,b or c to /opt/files folder

	cp /etc/[a-c]* /tmp/files

### MOVE

#### Move one or more files from source directory to destination folder

	mv commands.txt large.txt ~/Downloads/

#### Change the name of a file

	mv large.txt largefile.txt

#### Change the name of a folder

	mv Dir01 Directory01

#### Use the -i flag to make an INTERACTIVE MOVE - It will ask you to confirm before overwritting any existing file or folder

	mv -i largefile.txt ~/Downloads/

### FIND: Seach for files or folders

Find a **FILE** named 'largeText.txt' inside the /home directory

	find /home -name largeText.txt

Find a **FOLDER** named 'myDirectory' inside the /home directory

	find /home -type d -name myDirectory

*Use -type d to search for Folders*

*Replace '/home' with the directory where you need to find*
	
To find files craeted by user 'amy':

	find / -user amy

To backup amy's files

	mkdir /root/amy; find / -user amy -exec cp {} /root/amy \;
	
To find files bigger than 100MB

	find / -size +100M

To filter errors outta of find results use:

	find / -size +100M 2>/dev/null
	
To find **only files** that contain the string '127.0.0.1' inside the folder /etc

	find /etc -name '*' -type f | xargs grep '127.0.0.1'

### ECHO: Send a Mesagesage

#### To standard OUTPUT (Screen)

	echo 'Hello World!'

#### To a file (REPLACE Content)

	echo 'Hello World!' > myFile.txt

* It will overwrite file content with echo message

#### To a file (APPEND Content)

	echo 'Hello World!' >> myFile.txt

* It will append the message to the existing content of the file. The message will be appended to the end of the file


### HELP: Get Help for commands
	
	sudo help echo > echo_manual (Send it to a file)
	
	wget --help | less
	
	
#### Get the manual for a specific command
	man ls
	man echo
	
#### Info

Info will provide help for Linux tools.

You can find examples of how to use it.

If bot installed, please do so by using your package manager.

	info wget examples simple
	
### WHATIS: Brief description of command line program

	whatis find  
	whatis cat  
	whatis less

### ALIAS

	alias a1='ls -la'  
	alias please='sudo'
	please reboot now (Example of using the newly created alias)
	
#### User created ALIAS are stored in .bash_aliases files in the HOME user folder

	nano .bashrc
* You can edit this file to remove/edit existing alias

#### To remove an ALIAS using command

	alias all='ls -la'  
	all (it works)  
	unalias all  
	all (it doesn´t work anymore)

### PIPES: Pass the stdout of a command to the stdin of another command

	ls -la /etc | less
	ls | tee peanuts.txt (It writes the output of the command to 2 streams: file and screen)

### ENVIRONMENT: stores global variables such as: paths, home directory, etc

	echo $HOME
	echo $USER
	echo PATH
	env (This outputs information about the environment variables you currently have set)

### CUT: cuts character from all of the lines of a file

	cut -c 5 sample.txt (Cuts the 5th character of every line)
	cut -c 5-10 sample.txt (Cuts a string: 5th character included, 10th character excluded)
	cut -c 5- sample.txt (Cuts a string: From the 5th character included till the end of the line)
	cut -c -5 sample.txt (Cuts a string: From the first character of the line till 5th character included)
	cut -f 1 -d ";" sample.txt (Cuts since the beginning of the line till the specified character ';')
	cut -f 2 -d ";" sample.txt (Cuts since the specified character ';' forwards)
	cut -f 1  sample.txt (Cuts since the beginning of the line till the first TAB)
	cut -f 2  sample.txt (Cuts since the first found TAB till the next TAB or end of the line)
	
	cut -d: -f3 /etc/group | sort -n
	
	cut -d: -f3 /etc/group | sort -rn

### PASTE: Paste lines from a file. It can also paste different files

	cat sample2.txt (This is a file with multiple lines)

The
quick
brown
fox

	paste -s sample2.txt 

The		quick	brown	fox (In here the words were pasted using the default delimeter TAB. All the words go into one line because of the '-s' flag)

	paste -d " " -s sample2.txt

The quick brown fox (In here the words were pasted together using an empty space within words defined by the '-d' flag. All the words go into one line because of the '-s' flag)

### HEAD: Lets you see the first lines of a file

	head /var/log/syslog (by default the head command will show you the first 10 lines in a file)
	head -n 15 /var/log/syslog (show you the first 15 lines in a file)

### TAIL: lets you see the last lines of a file

	tail /var/log/syslog
	tail -n 10 /var/log/syslog (lets you see the last 10 files)
	tail -f /var/log/syslog (tail -f you can see everything that is getting added to that file)

### LINUX VERSION

	cat /etc/*release*
	
### See which SHELL you are using

	echo $SHELL

### SORT: The sort command is useful for sorting lines.

	sort file1.txt
	sort -r file1.txt (sort reverse alphabetically order)
	sort -n file1.txt (sort by numerical value)

### Unique: remove duplicates

	uniq file2.txt (removes duplicates form file2.txt)
	uniq -c ile2.txt (count of how many occurrences of a line)
	uniq -u file2.txt (Get unique values when lines are ADJACENT)
	sort file2.txt | uniq -u (If lines are not adjacent, I could use sort and pipe the result to uniq)
	
### COUNT the number of lines, words and bytes of a file

	wc someFile.txt (it returns the number of lines, words and bytes)
	wc -w someFile.txt (it only returns the number of words)
	
### GREP search files for characters that match a certain pattern

	grep fox sample.txt (it finds fox on sample.txt file)
	
	grep -i the sample.txt (it finds The, the, or any other. -i flags means it is case insentitive)
	
	grep someDir/ | grep '.txt$' (It will return any *.txt inside the folder someDir)
	
	grep amy -B3 /etc/passwd (it shows 3 lines BEFORE what greop found, very good for configuration files)
	
	grep amy -A3 /etc/passwd (it shows 3 lines AFTER what greop found, very good for configuration files)

### REGULAR Expressions 101

Let´s suppose we have the following file named sally.txt  

sally sells seashells  

by the seashore  

	grep ^by sally.txt (It returns the line that starts with "by" - by the seashore)
	
	grep seashells$ sally.txt (It returns the line that ends with "seashore" - sally sells seashells)
	
	grep e. sally.txt (It returns all of the words that will have the character "e": sells, seashells, the, seashore)
	
	grep ll[ys] sally.txt(it returns the "ll" string followed by any "y" or "s" character - sally, sells, seashells)
	
	grep d[^i]g (would match: dog and dug but not dig)
	
	grep d[a-c]g (would match dag, dbg or dcg
	
	grep d[A-C]g (would match dAg, dBg or dCg but not dag, dbg or dcg)
	
	grep -l returns only files
	
	grep '\<alex\>' myFiles.txt (searches for the word alex in every line)


### Run commands as SUPERUSER  

sudo: stands for super user do it

To run a command as another user, use SUDO:

	sudo my_command
	
su: stands for switch user

To switch from current user to another, use SU:

	su other_username	
	
The following command will switch from curent user to root. The whole bash will run under root account. The minus sign will activate the whole shell for root.

	su -


Ubuntu root does NOT have a password set fot the root account due to security reasons so you cant use the root account on Ubuntu

When you install Ubuntu you create a default user. This user by default has administrative privileges

In Ubuntu, if you happen to be using the default user created during installation and in case you need to switch your terminal bash to start working with administrative privileges run the command:

	sudo -i
	
**For Ubuntu only**

Make sure you check /etc/sudoers to see which users can sudo


### Get Users list  

	cat /etc/passwd

In this file you will see something like this for each user (humans or system users): root : x : 0 :0:root:/root:/bin/bash

1. username
1. password reference (/etc/shadow/)
1. User ID
1. Group ID
1. Comments such as user's real name
1. User's home directory
1. User's shell



### User's password store

User's encrypted passwords can be found with the following command:

	cat /etc/shadow/


### Get Groups

	cat /etc/group
	
In this file you will see something like this for each group:

1. Group Name
1. Group Password (Not used)
1. Group ID
1. Members of the Group


### User Management Tools

To create a new user  

	useradd user_name
	
To remove a User

	userdel user_name
	
To update user's password

	passwd user_name
	
To add a user to the wheel group in CentOS

	usermod -aG wheel user01 (User may need to logout and login again in order for new privileges to take effect)
	

### Modifying Permissions

Give the user the execute permission

	chmod u+x myFile
	
Give the user and group the read permission

	chmod ug+r myFile
	
Remove the execute permission from user

	chmod u-x myFile

Use numerical notation:  

4: read permission  
2: write permission  
1: execute permission

The user receives read, write and execute permission
The group receives read and write permission
Others receive read and write permission

	chmod 755 myFile
	
### Modifying Ownership

Modify user ownership

	chown user02 myFile
	
Modify group ownership

	chgrp group02 myFile
	
Modify user and group ownership at once:

	chown user02:group02 myFile


### Special Permissions

#### Setuid  

It is repressented by a "s" in the user permission set  
bit: 4  
It gives normal users elevated access

	sudo chmod 4555	myFile (or) chmod u+s myFile

	ls -l /usr/bin/passwd
	
	-rwsr-xr-x 1 root root 47032 Dec 11:45 /usr/bin/passwd


#### Setgid  

It is repressented by a "s" in the group permission set  
bit: 2  
It gives normal group elevated access

	sudo chmod 2555	myFile (or) chmod g+s myFile

	ls -l /usr/bin/wall
	
	-rwxr-sr-x 1 root tty 19024 Dec 11:45 /usr/bin/wall


#### Sticky Bit

It is repressented by a "t" in the group permission set, at the end  
bit: 1  
For Shared Directories!
Only the owner can delete files, the rest can add, write modify files in that directory Ej: /tmp
	
	sudo chmod 1755 myDir (or) sudo chmod +t myDir
	
## Proccesses

### PS

Keep in mind that the following commands are using BSD style

Run the following command to see running processes for the current user

	ps
	
Extended information about all of the running proccesses including those from other users  
* a displays all runnging process from all users  
* u displays extended information about the processes
* x displays list processes that dont have a TTY associated (These are system daemons) and will shoy a "?" under the TTY column

	ps aux
	
To see processes as files

	ls /proc
	

### TOP

Very useful command to get real time information about the processes that are running in the system  
It refreshes every 10 seconds  
Extremly useful to see what process are taking up a lot of your resources 

	top
	

### TTY

#### Regular Terminal Device

These are the terminals that you can get into using Ctrl-Alt-F1, Ctrl-Alt-F2, etc  
There are denoted by TTY1, TTY2

To exit a Regular Terminal Device, use Ctrl-Alt-F7

#### PseudoTerminal

These are the ones you launch inside a windows emulating a terminal   
There are denoted by PTS

### CLEAR DNS Cache

	sudo systemd-resolve --flush-caches
	
	sudo systemd-resolve --statistics

### CHeck Status of Firewall in Ubuntu
	
	sudo ufw status

### Check Open Ports on Ubuntu Box (netstat)

	sudo ss -tunlp
	
### View Memory and Swap consumption

	free -h
	
### List Block Devices

	lsblk

### Create Partitions

Use "parted"
	
	select /dev/sdb
	mklabel gpt
	mkpart name_partition_01 2048s 10GiB (Ej: Crear una particion desde el sector inicial hasta 10GB)
	mkpart name_partition_02 10GiB 20GiB (Ej: Crear una particion desde 10GB hasta 20GB)
	mkpart name_partition_03 10GiB 100% (Ej: Crear una particion desde 20GB hasta todo el tamano del disco)
	
Update fstab (To make the partition available after every time the system boots)

	vi /etc/fstab
	
### List BLOCK IDs

	sudo blkid
	
* Then you need to update the /etc/fstab file to include the new volumes
* Make sure the mount points exists

### Mount New Volumes

	mount -a
	
### Kill a Process

	kill PID
	
SIGKILL

	kill -9 PID
	
	
### Sending a Job to the Background

	sleep 1000 &
	
* The ampersand (&) at the ned will tell the command to run in the background

To view the jobs running on the background

	jobs
	
	
### Repositories List

	/etc/apt/sources.list
	
### TAR and GZIP

Gzip is used to compress single files

	gzip myLargeFile.gz
	
To decompress the file:

	gunzip myLargeFile.gz
	
To compress multiple Files we are going to need TAR

* c - create

* v - tell the program to be verbose and let us see what it's doing

* f - the filename of the tar file has to come after this option, if you are creating a tar file you'll have to come up with a name  

	tar -cvf myTarFile.tar file01 file02 file3
	
To add compression, use -z(gzip) or -j (bzip)
	
To extract files

* x - extract

* v - tell the program to be verbose and let us see what it's doing  

* f - the file you want to extract  

	tar xvf myTarFile.tar
	
* -C to swith the output path (If not specified, it extracts it on the current directory)

	tar -xvf myTarFile.tar -C /tmp
	
If a file has been compressed using TAR and GZIP, you can extract it withthe following command:

	tar zxvf file.tar or
	
	tar jxvf file.tar
	
To see the content of a TAR file

	tar -tvf myTarFile.tar
	
To compress a folder using Gzip

	tar -zcvf myCompressedFile.gz /home/user
	
To extract a folder to current path using Gzip

	tar -zxvf myCompressedFile.gz

To extract a folder to a different path using Gzip

	tar -zxvf myCompressedFile.gz -C /tmp/extract_here/

To compress a folder using BZIP2

	tar -jcvf myCompressedFile.bz2 /home/user
	
To extract a folder using BZIP2

	tar -jxvf myCompressedFile.bz2

To extract a folder to a different path using Bzip

	tar -jxvf myCompressedFile.bz2 -C /tmp/extract_here/

	
### Duplicate Data (Clone/Backup/Restore Hard Drives, Wipe/Delete Content, etc)

Clone a Hard Disk

	dd if=/dev/sda of=/dev/sdb
	
Backup a Partition

	dd if=/dev/sdb2 of=~/myDiskImage.img
	
Backup and Compress the Partition

	dd if=/dev/sdb2 | bzip myDiskImage.img.bz2
	
Restoring the image file in other machine's partition

	dd if=myDiskImage.img of=/dev/sdb3

Wipe/delete content of a disk by writting zeroes to it

	dd if=/dev/zero of=/dev/sdb
	
Wipe/delete content of a disk by writting random data to it

	dd if=/dev/random of=/dev/sdb
	
Repeat the previous command to make recover procedures less successful

	for y in {1..10}; do dd if=/dev/random of=/dev/sdb; done
	
Create ISO file from a CD-ROM

	dd if=/dev/dvd of=/opt/my_linux_image.iso
	
	dd if=/dev/sr0 of=/home/$user/my_linux_image.iso bs=2048 conv=sync
	
Create a dummy file of 1GB

	dd if=/dev/zero of=bigfile bs=1M count=1024	


### Compress Files

	gzip bigfile1
	
	bzip2 bigfile 2
	
	zip bigfile3.zip bigfile2

### Measure how long it takes to run a task

	time gzip bigfile1
	
	time find / -name host -type f	
	
### List installed Packages (Debian)

	dpkg -l 
	
### Install packages using Packgae Management System (APT)

To install:

	apt install package_name
	
To remove:

	apt remove package_name
	
Update and Upgrade:

	apt update
	apt upgrade
	
Get information about installed package_name

	apt show package_name
	
## Get rid of CentOS Screen Saver

Go to System Tools -> Settings

Go to Power

Set Blank Time to NEVER
	
## VI Editor

Command Mode:

	ESC

Inser Mode:
		
	i

### While on command mode

Delete a character:

	x
	
Delete a line:

	dd
	
Copy:

	yy
	
Paste:

	p
	
Undo:

	u
	
Scroll Up/Down:

	CTRL + u
	CTRL + d
	
To have access to write commands on Command Mode, press:

	:
	
To save a file

	:w
	
	:w filename
	
Quit and Discard changes:

	:q
	
Quit and Save:

	:wq
	
Search:

	/search_string # Press Enter and Press n to jump to the next ocurrence

Replace:

	ESC :
	
Go to command mode and type

	%s/search_string/new_string/g # g for global, to replace all ocurrences.
	
Go to the beginning of the file:

	:gg
	
Remeber vim is vi improved. It has color markup and automatic indentation. It may be beneficial for some type of work you do.

You could try vimtutor to learn some additional commands

	vimtutor
	

### RedHat Family Package manager

#### CentOS

This family use the RPM packages

RPM stands for RedHat Package Manager

To install a RPM package, use:

	rpm -i telnet.rpm
	
**This command won't take care of dependencies**
	
To uninstall a RPM package:

	rpm -e telnet.rpm
	
To query the database for an installed package use:

	rpm -q telnet.rpm

**YUM is a HIGH LEVEL Package Manager that uses RPM underneath**

To install a command and all of its dependencies, run:

	yum install ansible
	
To install a specific version of a package, run:

	yum install ansible-2.4.2.0
	
Pay attention since YUM will search packages and dependencies from internet repos

The list of Repos is locally stored on:

	/etc/yum.repos.d/
	
You can watch the repos files using:

	ls /etc/yum.repos.d/
	
To get the list of configured Repos:

	yum repolist
	
To get information (name, version, repository this package was installed from) about a certain package, run:

	yum list ansible
	
To see all of the installed versions for a specific package, run:

	yum --showduplicates list ansible

To remove a package, use:

	yum remove ansible
	

## CentOS LAMP Example configuration (Linux, Apache, MariaDB and PHP)

Here we are using MariaDB instead of MySQL. 

MariaDB is a fork of MySQL, so basically they are pretty similar.

### Firewalld on CentOS

#### Install Firewalld

Install firewalld

	sudo yum install firewalld
	
Start firewalld

	sudo systemctl start firewalld
	
Enable firewalld

	sudo systemctl enable firewalld
	
View firewall configured rules

	sudo firewall-cmd --list-all
	
Reload firewalld configuracion

	sudo firewall-cmd --reload
	
Add a permanent rule

	sudo firewall-cmd --permanent --zone=public --add-port=3306/tcp
	

#### Install MariaDB

Install MariaDB
	
	sudo yum install mariadb-server
	
Check MariaDB configuration

	sudo vi /etc/my.cnf
	
Start MariaDB Service

	sudo systemctl start mariadb
	
Enable MariaDB Service

	sudo systemctl enable mariadb
	
Configure a Database

	mysql
	
	MariaDB > CREATE DATABASE ecomdb;
	
	MariaDB > CREATE USER 'ecomuser'@'localhost' IDENTIFIED BY 'ecompassword';
	
	MariaDB > GRANT ALL PRIVILEGES ON *.* TO 'ecomuser'@'localhost';
	
	MariaDB > FLUSH PRIVILEGES;

Load a Script to database

	mysql < db-load-script.sql
	

#### Install Apache and PHP

Install Apache and PHP

	sudo yum install -y httpd php php-mysql
	
Configure Firewall rule

	sudo firewall-cmd --permanent --zone=public --add-port=80/tcp
	
Reload Firewall configuration

	sudo firewall-cmd --reload
	
Configure httpd

	sudo vi/httpd/conf/httpd.conf
	
Start and Enable httpd

	sudo systemctl start httpd
	
	sudo systemctl enable httpd
	
Clone code Repos to an specific path

	Git Clone https://github.com/kodekloudhub/learning-app-ecommerce.git /var/www/html
	
Update code index.php to connect to the right database

	
## Links: Hard and Symbolic

Hard Links point to the same iNode

Hard Links can NOT be used for cross-device files. Hard Links must reside on the SAME partition as the original file

Hard Links can NOT be used to point to Directories

Hard Links are at the same herarchical level

To create a Hard link, use the following command (It works pretty similar to the 'cp' commands):

	ln /etc/hosts myHostsFile
	
To list Links, use the command:

	ls -li /etc/hosts myHostsFile

To create a symbolic link, use the following command:

	ln -s myHostsFile mySymbolicLink

To view symbolic links:

	ls -l
	
And watch for:

	lrwxrwxrwx. 1 root root 9 Nov 5 15:20 mySymbolicLink -> myHostsFile
	
To create symbolic links always use ABSOLUTE path names



## Login using SSH Keys

On Server01, using account user01:

Generate user private and public keys. Using a passpharse is recommended

	ssh-keygen
	
Copy your public key to destination server Server02

	ssh-copy-id Server02
	
Now ssh into Server02 from Server01

## Copy files between servers SCP

On Server01
	
	scp /etc/hosts Server02:/tmp/hosts
	
It also works the other way around

	scp Server02:/tmp/hosts .
	
## REDIRECTION

It is used to manipulate the inpunt and output of commands

### Standard output (0): <

	sort < /etc/services # Im sending the content of /etc/services to the command sort
	
### Standard output (1): > 

Usually the standard output of a command is the console, but we can redirect it to other places

The following command will send the content of the directory to a file. It will ALWAYS create a new file. If a file with the same exists on the target path, it will be deleted and a new file will be created

	ls > ~/myfile
	
The following command will send the content of the directory to a file. It will create a new file if the file does not exist. If the file exists, it will APPEND the content to the file
	
	who >> ~ /myfile #


	
	

