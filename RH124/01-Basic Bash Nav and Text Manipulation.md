**Basic Bash Nav and Text Manipulation:**
-------------------------------------------------------------------------------------------------------------------------------------------------------------
**command; command; command; command**   ---> run commands sequentially without conditions

**command && command && command && command** ---> run commands sequentially with condition of NO FAILS happen.

**command1 || command2** ---> runs command 2 only if command1 FAILS

**whatis <command>** ---> know what a command does

***true/false ---> return a success/error (0/1) while doing nothing***

**file ~/file** ---> See file type and format.
**wc ~/file** ---> Display the size of the file.

**cat file.txt**---> Print whole file.
**tac file.txt** ---> Print the file in reverse.

**head -<n> file** ---> print first n lines from file
**tail -<n> file** ---> print last n lines from file

**history**---> see commands history
**!12** ---> select to rerun command number 12 in the history list
**!"string"**---> run the last command in history that has matching "string" in it

**cntr+r** ---> search in history
**cntr+r again while searching** ---> to cycle through the history

**alt+.** ---> cycle through arguments history and add them to the currunt command

**!!** ---> run last command from history

Say the last command in history is: **nano ~/.config/gtk-4.0/gtk.css**

**^old^new** ---> Runs the last command in history, but substitutes "3" with "4" in it. (as ^4^3).

**sudo -i**  --> use the root superuser

**sudo <any other command>**  ---> run any command with elevated privillages.

**man <command>** ---> see the manual on any command
*inside man, press "/"+"enter":*   Search inside man pages
*inside man, press "n":*   					To cycle forward through search matches
*inside man, press "shift"+"n":*    To cycle backwards through search matches

**man -k <command>** ---> search the man pages for this command.
**sudo mandb** ---> Update and reindex the man pages db if you cant find a certain document.


**tee (-a) outputfile** ---> print its input and save that as output in outputfile, Example: **cat file | tee -a file**

**ls -l | tee /tmp/saved-output | less**  ---> take a copy from the piped from ls to less, and save it as "/tmp/ saved-output"



**tree -f <dir>** ---> Show the contents of a directory.
**ls -lRa <dir>** ---> equivelent to tree + permissions.

**rename <old> <new> <files>** ---> Change a substring in the names of multiple files at once.

**mkdir -pv isos/{redhat,centos,fedora}/{1..10}** ---> Create multiple directories at once.
```
**$ tree isos/**
	isos/
	├── centos
	│   └── x86
	├── fedora
	│   └── x86
	└── redhat
    	└── x86
```
**cat <<EOF > cattest** ---> takes input from keyboard stdin until i enter "EOF", then saves it in cattest


File descriptors: STDIN -> 0 | STDOUT -> 1 | STDERR -> 2

vimtutor!!!!!!!!!!!!!!!!!!
**:set nu**---> Enable line numbering in vim


**cp <Sourcepath> <DestPath>** ---> to copy files (add **-r** to copy directories)
**scp SourcePath user@ip:DestPath** --> To copy files remotly from one machine to another via a network

*scp is depricated and always overrides all destination files*

**rsync SourcePath user@ip:DestPath** ---> Copies folders and files, and skips unchanged items

***How files point to data on the disk:***
	  Files point to inodes, inodes carries metadata, one of which is a pointer to the data on the disk.
	  
index nodes are always 256B, carrying ffile type, permissions, owners, timestamp, data pointer, and link count.
		
**ln <file> <hardlink>**---> To create a hard link, 2 files pointing to the same inode, thus data. If one file is deleted, all are deleted. (Must be on same FS)

**ln -s <file> <symlinkname>**---> To create a symlink, pointing to the same data but through a new inode.
If the original is deleted, the symlink is deleted.

**ls -li <file>** ---> See the inodes yourfiles points to.


Thank you :)
