### Software Management:
-----------------------------------------------------------------------------------------

An RPM is an ***archive file*** that bundles together:
 
- ***actual files***   to be installed.  
- ***Metadata***       (name, version, dependencies, license, etc.)  
- ***Scripts***        that run automatically during install/update/remove.  

RPM tracks everything it installs in a local database → so the system always knows what's installed, what files belong to what package, and what's needed before installing.  

Packages are **digitally signed with a GPG key** → if the signature doesn't match, RPM refuses to install it.  

RPM itself is a low-level tool, it won't resolve dependencies for you.
***That's what DNF is for.***  


**RPM package filename structure** ---> 
      
      name-version-release.architecture.rpm
      coreutils-9.5-6.el10.x86_64.rpm

***Querying installed packages:***  
**rpm -qa** ---> List all installed packages.  
**rpm -qf <filepath>** ---> Which **package** owns this file?  
**rpm -q <pkg>** ---> Is this package installed? What version?  
**rpm -qi <pkg>** ---> Detailed info about the package.  
**rpm -ql <pkg>** ---> List **all files** the package installed.  
**rpm -qc <pkg>** ---> List **only config files** the package installed.  
**rpm -qd <pkg>** ---> List **only documentation** files.  
**rpm -q --scripts <pkg>** ---> Show scripts that ran during install/remove.  
**rpm -q --changelog <pkg>** ---> Show the package changelog.  

Add **-p** to any of the above to inspect a **local .rpm** file before installing it:  
**rpm -qlp <file.rpm>** ---> List files inside an uninstalled local .rpm.  

**rpm -ivh <file.rpm>** ---> Installing a local rpm file ***(no dependency resolution)***  

***Use DNF instead whenever possible***,  it handles *dependencies automatically*.  

Extracting files from an RPM without installing:  
rpm2cpio <file.rpm> | cpio -idv ---> Extract all files into current dir.  
rpm2cpio <file.rpm> | cpio -tv ---> Just list files inside without extracting.  




***DNF Package Manager (replaces YUM in RHEL9+):*** (yum still works but is just a symlink to dnf.)  

DNF = RPM + dependency resolution + repository management.  


***Finding packages from the dnf repos:***  
**dnf list 'http*'** ---> List installed/available packages by name (wildcards work).  
**dnf search <keyword>** ---> Search in name and summary fields.  
**dnf search all '<keyword>'** ---> Search in name, summary, AND description.  
**dnf info <pkg>** ---> Show detailed info + install size.  
**dnf provides <filepath>** ---> Which package owns/provides this path?  


***Installing & Removing packages on the system:***  
**dnf install <pkg>** ---> Install package + all dependencies automatically.  
**dnf upgrade** ---> Upgrade all packages.  
**dnf upgrade <pkg>** ---> Upgrade a specific package.  
**dnf remove <pkg>** ---> Remove package + anything that depends on it.  

***Handling Groups of packages:***  
**dnf group list** ---> List available/installed groups.  
**dnf group list hidden** ---> Include hidden groups.  
**dnf group info '<group>'** ---> Show what's inside a group.  
**dnf group install '<group>'** ---> Install a whole group of packages.  

***History:***  
**dnf history** ---> Show all past install/remove transactions.  
**dnf history undo <number>** ---> Undo what was done by dnf at some point.  



***DNF Repositories:***  
**dnf repolist all** ---> List all repos and their status (enabled/disabled).  
**dnf config-manager --enable <repoid>** ---> Enable a repo.  
** dnf config-manager --disable <repoid> ---> Disable a repo.  
** dnf config-manager --add-repo="<url>"**  ---> Add a new repo (creates a .repo file automatically).  


***Repo files live in: /etc/yum.repos.d/ as .repo files.**  

.repo files contain:  
*baseurl* → where the packages are  
*enabled* → on or off / 1 or 0   
*gpgcheck* → verify package signatures 1 or 0  
*gpgkey* → path/url to the GPG key  


**rpm --import <gpgkey-url>** ---> Import the key first.  
GPG key, import before installing from a new repo:  
Then install normally with dnf. Never use --nogpgcheck unless you fully trust and understand the source.  


***Easiest way to add a third-party repo (if they provide one):***  

**dnf install <repo-release-package-url>** ---> installs the .repo file + GPG key in one shot.  

***Temporarily enable/disable a repo for one command:***  
**dnf install <pkg> --enablerepo=<repoid>**  
**dnf install <pkg> --disablerepo=<repoid>**  


Thank you :)
