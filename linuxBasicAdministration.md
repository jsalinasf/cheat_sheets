# Linux Command Cheat Sheet
## By Jorge
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

### Restart
$ sudo reboot now

### Shutdown Linux Box
$ sudo shutdown -P now

### Traverse directly to the root of the FileSystem
$ cd /

### Traverse directly to the User´s Home Folder
$ cd ~

### Print Working Directory
$ pwd

### List Hidden files ~ all
$ ls -la

### List Folders and subfiles RECURSIVELY
$ ls -R

### List Files and Folders in alphabetically reverse order
$ ls -lr

### List Files and Folders in reverse order by LAST MODIFICATION Time
$ ls -lt

### Create an empty file
$ touch example.txt

### Create Directory
$ mkdir name_of_folder

### Using NANO, a very basic Text Editor in Linux
$ nano example.txt

### COPY & PASTE in NANO
To copy a line in Nano: ALT + 6  
To paste a line in Nano: CRTL + U

### Clear Screen
$ clear

### Get file content/type explanation
$ file myCommands.txt 
$ myCommands.txt: UTF-8 Unicode text

$ file index.html
$ HTML document, ASCII text

$ file pic02.jpg
$ pic02.jpg: JPEG image data, JFIF standard 1.01, resolution (DPI), density 72x72, segment length 16, Exif Standard: [TIFF image data, big-endian, direntries=11, manufacturer=Apple, model=iPhone 6s, orientation=upper-left, xresolution=162, yresolution=170, resolutionunit=2, software=10.1.1, datetime=2016:12:02 11:10:20, GPS-Data], baseline, precision 8, 3218x4291, frames 3

### Delete File or Folder. If there files within the Folder, thos file will also be deleted
$ rm -rf example02.txt 
$ rm -rd Presentations

### CAT
#### For reading SHORT Content
$ cat example.txt

#### For concatenating files
$ cat example01.txt exmaple02.txt

### LESS
#### For viewing LONG Files as pages
$ less veryLongFile.txt
(Press Space to keep reading and 'q' to exit

### HISTORY
#### It allows you to see all the commands that habe been input into the system
$ history
#### To run the exact last command without typing it againg or using the up arrow, use:
$ !!
#### Send your history to a file using stout
$ history > myCommands.txt

### COPY
#### From current directory to /tmp folder
$ cp file01.txt /tmp/file01.txt
#### Using recursive copy (To include files and folders within the source
$ cp -r Documents/ Downloads/
#### Copy all files to destination - * is a wildcard
$ cp -r *.* ~/Downloads/
#### Use the -i to make an INTERACTIVE COPY - It avoids existing folders or files to be overwritten by CP default behavior
$ cp -ir * ~/Downloads/

  179  mv commands.txt large.txt ~/Downloads/
  180  ls
  181  cd-
  182  cd -
  183  ls
  184  cp -r * ~/Documents/
  185  rm -rf *
  186  cd -
  187  ls
  188  mv commands.txt large.txt ~/Downloads/
  189  cd -
  190  ls
  191  mv large.txt largefile.txt
  192  ls
  193  mv * ~/Documents/
  194  ls
  195  cd -
  196  ls
  197  mv Dir01/ Directory01
  198  ls
  199  mv Directory01/ myFilesç
  200  mv myFilesç myFiles
  201  ls
  202  ls -l
  203  clear
  204  ls
  205  cp largefile.txt example.txt ~/Downloads/
  206  mv -i largefile.txt ~/Downloads/
  207  cd -
  208  mv -b largefile.txt ~/Downloads/
  209  ls
  210  cd -
  211  mv -b largefile.txt ~/Downloads/
  212  ls
  213  cp ~/Downloads/largefile.txt 
  214  cp ~/Downloads/largefile.txt /
  215  cp ~/Downloads/largefile.txt ~/Documents/
  216  ls
  217  mv -b largefile.txt ~/Downloads/
  218  ls -l
  219  cd -
  220  ls
  221  ls -l
  222  clear
  223  ls
  224  rm -rf *
  225  cd -
  226  ls
  227  mv myFiles/ Files
  228  ls
  229  mkdir Books Sheets
  230  ls
  231  mkdir -p Presentations/MKT
  232  ls
  233  cd Presentations/
  234  ls
  235  cd ..
  236  rm -rf Files Presentations/ Sheets/
  237  ls
  238  rm -rf Books/
  239  ls
  240  rm example.txt 
  241  ls
  242  clear
  243  ls
  244  rm -i otherfile.txt 
  245  ls -l
  246  cd -
  247  cd ~/Downloads/
  248  ls
  249  file SampleJPGImage_15mbmb.jpg 
  250  mv SampleJPGImage_15mbmb.jpg pic15.jpg
  251  ls
  252  mv SampleTextFile_1000kb.txt largeText.txt
  253  ls
  254  mv SampleJPGImage_2mbmb.jpg pic02.jpg
  255  ls
  256  mv -i * ~/Documents/
  257  ls
  258  cd -
  259  ls
  260  ls -l
  261  clear
  262  ls
  263  cp pic15.jpg otherpic.jpg
  264  cp pic15.jpg otherpic2.jpg
  265  cp pic15.jpg otherpic3.jpg
  266  ls
  267  rm -i otherpic.jpg 
  268  ls
  269  mkdir Fotos
  270  mv otherpic2.jpg otherpic3.jpg ~/Documents/Fotos/
  271  ls
  272  rm -r Fotos/
  273  ls
  274  cd /
  275  find /home largeText.txt
  276  find /home -name largeText.txt
  277  find /home -type d Downloads
  278  find /home -type d /Downloads
  279  find /home -type d -name Downloads
  280  clear
  281  help echo > echo_manual
  282  sudo help echo > echo_manual
  283  help echo
  284  echo --help
  285  mkdir --help
  286  echo --help
  287  mkdir --help
  288  clear
  289  help
  290  man ls
  291  man echo
  292  clear
  293  whatis find
  294  whatis cat
  295  whatis less
  296  alias a1='ls -la'
  297  cd ~
  298  cd Documents/
  299  a1
  300  alias please='sudo?
  301  alias please='sudo'
  302  please reboot now
  303  history
  304  please reboot
  305  ls -la
  306  nano .bashrc 
  307  nano .bash_aliases
  308  exit
  309  please reboot now
  310  pwd
  311  ls -la
  312  nano .bash_aliases 
  313  alias all='ls -la'
  314  all
  315  unalias all
  316  all
  317  find /home -name pic*
  318  ls
  319  cd Documents/
  320  ls
  321  mkdir Presentations
  322  cd /
  323  find /home -type d -name Presen*
  324  ls
  325  cd ~
  326  ls
  327  cd Documents/
  328  ls
  329  less largeText.txt 
  330  whatis yum
  331  whatis apt-get
  332  man apt-get
  333  apt-get --hrlp
  334  apt-get --help
  335  logout
  336  exit
  337  please shutdown -P now
  338  shutdown -P now
  339  pwd
  340  ls
  341  cd Documents/
  342  ls
  343  echo 'Hello World!' > peanuts.txt
  344  more peanuts.txt 
  345  car peanuts.txt 
  346  cat peanuts.txt 
  347  less peanuts.txt 
  348  echo 'Hello World 2!' > peanuts.txt
  349  less peanuts.txt 
  350  more peanuts.txt 
  351  echo 'Hello World 3!' >> peanuts.txt
  352  more peanuts.txt 
  353  clear
  354  ls -l /var/log/ > myOutput.txt
  355  more myOutput.txt 
  356  echo Hello World > rm
  357  ls
  358  more rm
  359  nano rm
  360  > someFile.txt
  361  ls
  362  more someFile.txt 
  363  nano someFile.txt 
  364  clear
  365  history
  366  history > myCommands.txt
