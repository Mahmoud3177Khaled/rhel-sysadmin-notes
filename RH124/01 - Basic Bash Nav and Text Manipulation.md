**Basic Bash Navigation and Text Manipulation:**
-------------------------------------------------------------------------------------------------------------------------------------------------------------
**command; command; command; command** ---> Run commands sequentially without conditions.

**command && command && command && command** ---> Run commands sequentially with condition of NO FAILS.

**command1 || command2** ---> Runs command2 only if command1 FAILS.

**whatis \<command\>** ---> Know what a command does.

***true/false ---> return a success/error (0/1) while doing nothing***

**file ~/file** ---> See file type and format.
**wc ~/file** ---> Display the size of the file.

**cat file.txt** ---> Print whole file.
**tac file.txt** ---> Print the file in reverse.

**head -\<n\> file** ---> Print first n lines from file.
**tail -\<n\> file** ---> Print last n lines from file.

**history** ---> See commands history.
**!12** ---> Select to rerun command number 12 in the history list.
**!"string"** ---> Run the last command in history that has matching "string" in it.

**Ctrl+R** ---> Search in history.
**Ctrl+R again while searching** ---> Cycle through the history.

**Alt+.** ---> Cycle through arguments history and add them to the current command.

**!!** ---> Run last command from history.

Say the last command in history is: **nano ~/.config/gtk-4.0/gtk.css**

**^old^new** ---> Runs the last command in history, but substitutes "old" with "new" in it. (as ^4^3).

**sudo -i** ---> Use the root superuser.

**sudo \<any other command\>** ---> Run any command with elevated privileges.

**man \<command\>** ---> See the manual on any command.
*Inside man, press "/"+"enter":* Search inside man pages.
*Inside man, press "n":* Cycle forward through search matches.
*Inside man, press "Shift"+"n":* Cycle backwards through search matches.

**man -k \<command\>** ---> Search the man pages for this command.
**sudo mandb** ---> Update and reindex the man pages db if you can't find a certain document.

**tee (-a) outputfile** ---> Print its input and save that as output in outputfile. Example: **cat file | tee -a file**

**ls -l | tee /tmp/saved-output | less** ---> Take a copy from the piped from ls to less, and save it as "/tmp/saved-output".

**tree -f \<dir\>** ---> Show the contents of a directory.
**ls -lRa \<dir\>** ---> Equivalent to tree + permissions.

**rename \<old\> \<new\> \<files\>** ---> Change a substring in the names of multiple files at once.

**mkdir -pv isos/{redhat,centos,fedora}/{1..10}** ---> Create multiple directories at once.

```
$ tree isos/
isos/
├── centos
│   └── x86
├── fedora
│   └── x86
└── redhat
└── x86
```

**cat <<EOF > cattest** ---> Takes input from keyboard stdin until I enter "EOF", then saves it in cattest.

    File descriptors: STDIN -> 0 | STDOUT -> 1 | STDERR -> 2

vimtutor ---> very helpful guide to vim
    **:set nu** ---> Enable line numbering in vim

**cp \<SourcePath\> \<DestPath\>** ---> Copy files (add **-r** to copy directories).
**scp SourcePath user@ip:DestPath** ---> Copy files remotely from one machine to another via a network.

*scp is deprecated and always overrides all destination files*

**rsync SourcePath user@ip:DestPath** ---> Copies folders and files, and skips unchanged items.

***How files point to data on the disk:***
    Files point to inodes, inodes carry metadata, one of which is a pointer to the data on the disk.
    
    Index nodes are always 256B, carrying file type, permissions, owners, timestamp, data pointer, and link count.

**ln \<file\> \<hardlink\>** ---> Create a hard link, 2 files pointing to the same inode, thus data. If one file is deleted, all are deleted. (Must be on same FS)

**ln -s \<file\> \<symlinkname\>** ---> Create a symlink, pointing to the same data but through a new inode. If the original is deleted, the symlink is deleted.

**ls -li \<file\>** ---> See the inodes your files point to.
