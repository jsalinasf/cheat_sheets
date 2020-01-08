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
	sudo yum update -y
#### Ubuntu
	sudo apt update
	sudo apt upgrade
	sudo apt autoremove (cleanup after updates installation)

* You can run first and second commands at once using:  
	$ sudo apt update && sudo apt upgrade -y
	
### Reboot: Restart a Linux Box
	reboot now

### Shutdown: Turn off a Linux Box
	sudo shutdown -P now
*WARNING: Once you shutdown a Linux Box you won't be able to connect to it again until someone powers it on

### CD: Change Directory
#### Traverse directly to the root of the FileSystem 
	cd /
#### Traverse directly to the User´s Home Folder
	cd ~
#### Traverse directly to the last folder direcyory you were in
	cd -

### PWD: Print Working Directory
	pwd
### LS: List Hidden files ~ all
	ls -la
#### List Folders and subfiles RECURSIVELY
	ls -R
#### List Files and Folders in alphabetically reverse order
	ls -lr
#### List Files and Folders in reverse order by LAST MODIFICATION Time
	ls -lt

### Create an empty file
	touch example.txt

### MKDIR: Create Directory
	mkdir name_of_folder
#### Create subdirectories at once using the '-p' flag
	mkdir -p Books/Sheets

### Using NANO, a very basic Text Editor in Linux
	nano example.txt
* COPY: ALT + 6  
* PASTE: CRTL + U
* CRTL + X: To exit  

### CLEAR: Clear Screen  
	clear
### FILE: Get file content/type explanation
	file myCommands.txt  
	myCommands.txt: UTF-8 Unicode text

	file index.html  
	HTML document, ASCII text

	file pic02.jpg  
	pic02.jpg: JPEG image data, JFIF standard 1.01, resolution (DPI), density 72x72, segment length 16, Exif Standard: [TIFF image data, big-endian, direntries=11, manufacturer=Apple, model=iPhone 6s, orientation=upper-left, xresolution=162, yresolution=170, resolutionunit=2, software=10.1.1, datetime=2016:12:02 11:10:20, GPS-Data], baseline, precision 8, 3218x4291, frames 3

### DELETE: Delete File or Folder
	rm -f example02.txt  (To delete a File)
	rm -rd Presentations  (To delete a Folder)
*WARNING: the -r flag will delete all of the files contained within the Folder

### CAT: For Reading SHORT Content
	cat example.txt
#### For concatenating files
	cat example01.txt exmaple02.txt

### LESS: For viewing LONG Files as pages
	less veryLongFile.txt  
*Press Space to keep reading and 'q' to exit

### HISTORY: To see all the commands that habe been input into the system
	history
#### To run the exact last command without typing it againg or using the up arrow, use:
	!!
#### Send your history to a file using stdout
	history > myCommands.txt

### COPY
#### From current directory to /tmp folder
	cp file01.txt /tmp/file01.txt
#### From specified source directory to /tmp folder
	cp ~/Presentations/file01.txt /tmp/file01.txt
#### Using recursive copy (To include files and folders within the source
	cp -r Documents/ Downloads/
#### Copy all files to destination - * is a wildcard
	cp -r *.* ~/Downloads/
#### Use the -i flag to make an INTERACTIVE COPY - It will ask you to confirm before overwritting any existing file or folder
	cp -ir * ~/Downloads/

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
	find /home largeText.txt
	find /home -name largeText.txt
	find /home -type d -name Downloads
	* Use -type d to search for Folders
	* Replace '/home' with the directory where you need to find

### ECHO: Send a Mesagesage
#### To standard OUTPUT (Screen)
	echo 'Hello World!'
#### To a file (REPLACE Content)
	echo 'Hello World!' > myFile.txt
*It will overwrite file content with echo message
#### To a file (APPEND Content)
	echo 'Hello World!' >> myFile.txt
*It will append the message to the existing content of the file. The message will be appended to the end of the file


### HELP: Get Help for commands
	sudo help echo > echo_manual (Send it to a file)
#### Get the manual for a specific command
	man ls
	man echo

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


### Run commands as SUPERUSER  
This specific my_command will be run as root  

	sudo my_command
	
It will substitute user. If no user is specified, a root shell will be open  

	su

Make sure you check /etc/sudoers to see which users can sudo


### Get Users list  

	cat /etc/passwd

In this file you will see something like this for each user (humans or system users): root : x : 0 :0:root:/root:/bin/bash

1. username
1. password reference (/etc/shadows/)
1. User ID
1. Group ID
1. Comments such as user's real name
1. User's home directory
1. User's shell




