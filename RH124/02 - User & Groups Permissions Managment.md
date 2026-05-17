**User & Permissions Management:**
----------------------------------------------------------------------------------------------------------------
**id \<user\>** ---> Show all details of a user (UID, GID, secondary groups).
**passwd \<user\>** ---> Change user password.

**ls /etc/passwd** ---> See all users (no passwords here).  
**ls /etc/shadow** ---> See all users passwords (hashed).  
**ls /etc/group** ---> See all user groups.  

**useradd \<username\>** ---> Add a new user.

**ls /etc/passwd** --->
```
***rocky:x:1000:1000::/home/rocky:/bin/bash***
name:pass:uid:gid:fullName:homeDir:allowed user shell
```
**useradd \<user\> --> add a auser named "user"**  
**userdel \<user\> --> delete the user named "user"**  
**users**  

**cat /etc/login.defs** ---> Default setting for a newly created user.  
**/etc/skel** ---> Contents is copied to the home dir of all new users.  

**chage** ---> Change password lifetime, warning, etc.  
**chage -d 0 \<user\>** ---> Set the user password to expire immediately on login, so he changes it right then.  

**usermod [options] \<user\>**  
**usermod -c "nickname" \<user\>** ---> Add a nickname for a user.  
**usermod -g \<group\> \<user\>** ---> Set the primary group of \<user\> to \<group\>.  
**usermod -G \<group\> \<user\>** ---> Set the secondary group of \<user\> to \<group\>.  
**usermod -aG \<group\> \<user\>** ---> Append \<group\> to secondary groups of \<user\>.  
**usermod -L \<user\>** ---> Locks the password of the \<user\> user account.  
**usermod -U \<user\>** ---> Unlocks the password of the \<user\> user account.  
**usermod -l \<user\>** ---> Locks the user account, still there but unusable.  

**groupadd** ---> Add a new group.  

**usermod -c "ubuntu" -s /sbin/nologin** ---> Lock user from logging in and acquiring a bash.  

*Every user has his own and private primary group id, containing only him*  

**ls /etc/group** --->  
```
lpadmin:x:114:mahmoud-khaled
groupName:groupPassW:GID:groupMembers
```  
***Groups have passwords so users can add themselves to them if they know the password***  

**ls /etc/shadow** --->

```
rocky:!!:20570:0:99999:7:::
user:hashedPass:epochAge:resetWait:PassLife:warnBuffer:expiredLogin:AccExpire:Futureuse
```

**chsh \<user\> /bin/bash** ---> Change the login shell of a user (like /sbin/nologin or /bin/bash).  

**su - \<user\>** ---> Raise your privileges to a user you know his password.  
**visudo** ---> Edit /etc/sudoer, but with syntax checks for safety.  

*Inside visudo:*  
WHO   WHERE=(AS_WHOM)   WHAT  

*mahmoud can run any command as any user on any host:*  
**mahmoud ALL=(ALL:ALL) ALL**  

*ahmed can only restart nginx, as root, on any host, but has to enter his password, trying sudo with any other command would **fail***:  
**ahmed ALL=(root) /usr/bin/systemctl restart nginx**  

*sara can run apt commands without entering any password:*  
**sara ALL=(root) NOPASSWD: /usr/bin/apt update**  

*we can also use grant groups to have a certain user privileges:*  
**%admins ALL=(root) NOPASSWD:/usr/bin/dnf**  

*We can also have drop-in files that mimic adding entries to sudoers, but being separately named:*  
**#includedir /etc/sudoers.d**  

*inside that, we can have all the above in files named: **xx-entry.conf**, like systemd dropins.*  

*Best practice is not to delete users, because it creates accidental data leakage in the deleted user home dir if another user got created with the id of the deleted user. **usermod -l** is safer or delete his home permanently*  

---

**Permission Management:**  
-------------------------------------------------------------------------------  
Every file/dir has an **owner (user)**, an **owning group**, and **others (everyone else).**  
Permissions are checked in order: **user → group → other** most specific wins.  

Three permission types:  
**r (read) 4** → read file contents / list directory contents  
**w (write) 2** → modify file / create or delete files inside a dir  
**x (execute) 1** → run file as command / cd into a directory (needs r too)  

    7 = rwx
    6 = rw-
    5 = r-x
    4 = r--
    0 = ---

**ls -l output** --->   
```
-rw-rw-r--. 1 student student 0 Mar 8 17:36 test
^---^^^---^^^
|   |   |   └─ other: r--
|   |   └───── group: rw-
|   └───────── user:  rw-
└───────────── file type (- file, d dir, l symlink)
```

**ls -ld \<dir\>** ---> Show permissions of the directory itself, not its contents.  

***Key gotchas for beginners:***  

- *Permissions don't inherit* ---> a subdir doesn't auto-get its parent's permissions.  
- *Allowing +w on a directory* ---> can delete any file inside it, even if you don't own the file.  
- *Sticky bit* ---> overrides the above: only the file owner can delete it, even in a writable dir.  

---

***chmod octal method:***  

**chmod** ---> Change permissions on a file/dir.  
**chmod 640 \<file\>** ---> *owner=rw(6), group=r(4), other=none(0)*  
**chmod -R 755 \<dir\>** ---> Everything inside: *owner=rwx(7), group+other=r-x(5)*  
**chown** ---> Change ownership of a file/dir.  
**chown \<user\> \<file\>** ---> Change the owning user.  
**chown :\<group\> \<file\>** ---> Change the owning group.  
**chown \<user\>:\<group\> \<file\>** ---> Change both at once. 
**chown -R \<user\>:\<group\> \<dir\>** ---> Recursively change ownership.  

---

***chmod symbolic method: instead of numbers, use letters:***  

**chmod Who+/-/=Which \<file\>**  

    Who: *u=user*, *g=group*, *o=other*, *a=all*
    + --> add
    - --> remove
    = --> set exactly; Which: *r, w, x*

**chmod go-rw \<file\>** ---> Remove r and w from group and other.  
**chmod a+x \<file\>** ---> Add execute for everyone.  
**chmod g=rwx \<file\>** ---> Set group to exactly rwx, overwriting whatever was there.  
**chmod -R g+rwX \<dir\>** ---> Recursively add rw to group, and x only where x is already set (dirs + already-executable files). Safer than lowercase x recursively.  
  
---


***Quick reminder:***

For Dirs:  
**x** --> Able to enter a dir (cd to it)  
**rx** ---> Enter it and list contents  
**wx** ---> Create/delete files inside - REGARDLESS of being an owning user/group or not.  

For files:  
**r** ---> Read.  
**w** ---> Edit.  
**rx** --> Execute.  

---

***Special Permissions (4th permission type):***  

1)
**suid** ---> File temporarily runs as **its owner**, not as the user who launched it.  
    Example: passwd runs **as root by users** so normal users can change their own password.  
    Shown as s in **owner's x position**: -rw**s**r-xr-x  
    Indeed **ls -l /bin/passwd** returns -rwsr-xr-x  

**chmod u+s \<file\>** or **chmod 4xxx \<file\>** where xxx is normal octal code.  

2)
**sgid** ---> Files created inside the dir **inherit the parent dir's group** automatically.  
    Perfect for shared team folders *(no manual chown needed).*  
    Shown as **s in group's x position**: drwxr-**s**r-x  

**chmod g+s \<dir\>** or **chmod 2xxx \<dir\>** where xxx is normal octal code.  

3)
**sticky bit** → users can **only delete their own files** inside a writable dir for them.  
    Example: /tmp is writable by all, but nobody can delete each other's files.  
    Shown as **t in other's x position**: drwxrwxrw**t**  

**chmod o+t \<dir\>** or **chmod 1xxx \<dir\>** where xxx is normal octal code.  

*Uppercase S or T = special bit is set but execute beneath it is NOT → usually a mistake.*  

---

**umask — change permissions for new files/dirs:**  

Files by default start at 666, dirs start at 777, and umask value gets subtracted.  
  
**umask** ---> Show current umask.  

**umask 022** ---> files→ 644 (rw-r--r--) dirs→ 755 (rwxr-xr-x)  
**umask 027** ---> files→ 640 (rw-r-----) dirs→ 750 (rwxr-x---)  
**umask 002** ---> files→ 664 (rw-rw-r--) dirs→ 775 (rwxrwxr-x)  

*Effect vanishes once session is terminated, **add your umask to ~/.bashrc** to make the effect permanent.*  

*For security: Files never start with +x (666 not 777), so execute must always be added manually with chmod.*  
