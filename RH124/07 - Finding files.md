### Finding files:
-----------------------------------------------------------------------------------------------------------------------------  
Two main tools:  

**locate** → fast, searches a pre-built database (not real-time, updates daily)  
**find** → slow, searches the filesystem in real-time (accurate, tons of filtering options)  

***locate:***  
**locate <keyword>** ---> Search for files by name/path from the database.  
**locate -i <keyword>** ---> Case-insensitive search.  
**locate -n 5 <keyword>** ---> Limit results to first 5 matches.  
**updatedb** ---> Manually update the locate database (root only).  

*locate is fast but won't find files created since the last updatedb run.*  
*Users only see results for **files they have permission** to access.*  


***find:***  
**find <startDir> -name 'pattern' ---> Search by exact filename (wildcards need quotes).  
**find / -name '*.txt'** ---> All .txt files from root.  
**find /etc -name '*pass*'** ---> Files with "pass" anywhere in the name.  
**find / -iname '*messages*'** ---> Case-insensitive name search.  

(Combine with the above arguments for startDir and pattern to search for.)  

***By ownership:***    
**find -user <username>** ---> Files owned by this user.  
**find -group <groupname>** ---> Files owned by this group.  
**find -uid <uid>** ---> Files owned by this UID.  
**find / -user root -group mail** ---> Owned by root user AND mail group.  

**By permissions:**  
**find /home -perm 764** ---> Exact permissions match (rwxrw-r--).  
**find /home -perm -324** ---> At least these permissions (user=wx, group=w, other=r).  
**find /home -perm /442** ---> Any of these permissions (user=r OR group=r OR other=w).  
**find -perm -o=w** ---> Others have write permission.   

***By size:***  
**find -size 10M** ---> **Exactly** 10MB.  
**find -size +10G** ---> **Larger than** 10GB.  
**find -size -10k** ---> **Smaller than** 10KB.  
Units: k (kilo), M (mega), G (giga) --- case matters.  

***By modification time:***  
**find / -mmin 120** ---> Modified **exactly** 120 minutes ago.  
**find / -mmin +200** ---> Modified **more than** 200 minutes ago.  
**find / -mmin -150** ---> Modified **less than** 150 minutes ago.  

***By file type:***  
**find /etc -type d** ---> Directories only.  
**find / -type f** ---> Regular files only.  
**find / -type l** ---> Symbolic links only.  
**find /dev -type b** ---> Block devices only.  

***By hard link count:***  
**find / -type f -links +1** ---> Regular files with **more than** 1 hard link.  

***Useful combo:***  
**find /home -perm 764 -ls** ---> Show detailed info (perms, owner, size) for matched files.  

*If you omit the directory, find starts from the current directory.*  
*Always quote wildcards in find: '.txt' not *.txt  


Thank you :)





