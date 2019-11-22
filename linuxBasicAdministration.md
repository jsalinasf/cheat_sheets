# Linux Command Cheat Sheet
## All the credits goes to [Linux Journey](https://linuxjourney.com/)

## Please assume dolar sign '$' as the prompt of your Linux´s Terminal Box

### Install VMware Tools
$ sudo mount /dev/sr0 /mnt/cdrom  
$ cd /mnt/cdrom/  
$ cp VMwareTools-10.3.10-13959562.tar.gz /tmp/  
$ cd /tmp  
$ tar -zxvf VMwareTools-10.3.10-13959562.tar.gz  
$ cd vmware-tools-distrib/  
$ ./vmware-install.pl  
$ sudo ./vmware-install.pl  
$ reboot now

### Reboot: Restart a Linux Box
$ sudo reboot now

### Shutdown: Turn off a Linux Box
$ sudo shutdown -P now
*WARNING: Once you shutdown a Linux Box you won't be able to connect to it again until someone powers it on

### CD: Change Directory
#### Traverse directly to the root of the FileSystem
$ cd /
#### Traverse directly to the User´s Home Folder
$ cd ~
#### Traverse directly to the last folder direcyory you were in
$ cd -

### PWD: Print Working Directory
$ pwd

### LS: List Files or Folder
#### List Hidden files ~ all
$ ls -la
#### List Folders and subfiles RECURSIVELY
$ ls -R
#### List Files and Folders in alphabetically reverse order
$ ls -lr
#### List Files and Folders in reverse order by LAST MODIFICATION Time
$ ls -lt

### Create an empty file
$ touch example.txt

### MKDIR: Create Directory
$ mkdir name_of_folder
#### Create subdirectories at once using the '-p' flag
$ mkdir -p Books/Sheets

### Using NANO, a very basic Text Editor in Linux
$ nano example.txt

* COPY: ALT + 6  
* PASTE: CRTL + U
* CRTL + X: To exit  

### CLEAR: Clear Screen
$ clear

### FILE: Get file content/type explanation
$ file myCommands.txt  
$ myCommands.txt: UTF-8 Unicode text

$ file index.html  
$ HTML document, ASCII text

$ file pic02.jpg  
$ pic02.jpg: JPEG image data, JFIF standard 1.01, resolution (DPI), density 72x72, segment length 16, Exif Standard: [TIFF image data, big-endian, direntries=11, manufacturer=Apple, model=iPhone 6s, orientation=upper-left, xresolution=162, yresolution=170, resolutionunit=2, software=10.1.1, datetime=2016:12:02 11:10:20, GPS-Data], baseline, precision 8, 3218x4291, frames 3

### DELETE: Delete File or Folder
To delete a File: $ rm -f example02.txt  
To delete a Folder: $ rm -rd Presentations  
*WARNING: the -r flag will delete all of the files contained within the Folder

### CAT: For Reading SHORT Content
$ cat example.txt
#### For concatenating files
$ cat example01.txt exmaple02.txt

### LESS: For viewing LONG Files as pages
$ less veryLongFile.txt  
*Press Space to keep reading and 'q' to exit

### HISTORY: To see all the commands that habe been input into the system
$ history
#### To run the exact last command without typing it againg or using the up arrow, use:
$ !!
#### Send your history to a file using stdout
$ history > myCommands.txt

### COPY
#### From current directory to /tmp folder
$ cp file01.txt /tmp/file01.txt
#### From specified source directory to /tmp folder
$ cp ~/Presentations/file01.txt /tmp/file01.txt
#### Using recursive copy (To include files and folders within the source
$ cp -r Documents/ Downloads/
#### Copy all files to destination - * is a wildcard
$ cp -r *.* ~/Downloads/
#### Use the -i flag to make an INTERACTIVE COPY - It will ask you to confirm before overwritting any existing file or folder
$ cp -ir * ~/Downloads/

### MOVE
#### Move one or more files from source directory to destination folder
$ mv commands.txt large.txt ~/Downloads/
#### Change the name of a file
$ mv large.txt largefile.txt
#### Change the name of a folder
$ mv Dir01/ Directory01
#### Use the -i flag to make an INTERACTIVE MOVE - It will ask you to confirm before overwritting any existing file or folder
$ mv -i largefile.txt ~/Downloads/

### FIND: Seach for files or folders
$ find /home largeText.txt
$ find /home -name largeText.txt
$ find /home -type d -name Downloads
* Use -type d to search for Folders
* Replace '/home' with the directory where you need to find

### ECHO: Send a Mesagesage
#### To standard OUTPUT (Screen)
$ echo 'Hello World!'
#### To a file (REPLACE Content)
$ echo 'Hello World!' > myFile.txt
*It will overwrite file content with echo message
#### To a file (APPEND Content)
$ echo 'Hello World!' >> myFile.txt
*It will append the message to the existing content of the file. The message will be appended to the end of the file


### HELP: Get Help for commands
#### Send it to a file
$ sudo help echo > echo_manual
#### Get the manual for a specific command
$ man ls
$ man echo

### WHATIS: Brief description of command line program
$ whatis find  
$ whatis cat  
$ whatis less

### ALIAS
$ alias a1='ls -la'  
$ alias please='sudo'
* Example of using the newly created alias: $ please reboot now
#### User created ALIAS are stored in .bash_aliases files in the HOME user folder
$ nano .bashrc
* You can edit this file to remove/edit existing alias
#### To remove an ALIAS using command
$ alias all='ls -la'  
$ all (it works)  
$ unalias all  
$ all (it doesn´t work anymore)


### PIPES
$ ls -la /etc | less
$ ls | tee peanuts.txt (It writes the output of the command to 2 streams: file and screen)

### ENVIRONMENT
$ echo $HOME
$ echo $USER
$ echo PATH
$ env (This outputs information about the environment variables you currently have set)

### CUT
cut -c 5 sample.txt (Cuts the 5th character of every line)
cut -c 5-10 sample.txt (Cuts a string: 5th character included, 10th character excluded)
cut -c 5- sample.txt (Cuts a string: From the 5th character included till the end of the line)
cut -c -5 sample.txt (Cuts a string: From the first character of the line till 5th character included)
cut -f 1 -d ";" sample.txt (Cuts since the beginning of the line till the specified character ';')
cut -f 2 -d ";" sample.txt (Cuts since the specified character ';' forwards)
cut -f 1  sample.txt (Cuts since the beginning of the line till the first TAB)
cut -f 2  sample.txt (Cuts since the first found TAB till the next TAB or end of the line)














