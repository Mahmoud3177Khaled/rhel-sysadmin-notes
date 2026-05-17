**Process Management:**  
-----------------------------------------------------------------------------------------------------------------------------------------  
**w** ---> See logged in users with system info  

***TTYs in RHEL***: A tty (teletypewriter) is a virtual console, a text-based terminal session that runs independently of any graphical interface.  

RHEL provides 6 virtual consoles (tty1–tty6) accessible via **Ctrl+Alt+F1** through **Ctrl+Alt+F6.**  

***tty1*** ---> Default graphical login (GNOME/GUI) if a desktop environment is installed  
***tty2*** ---> Used by GNOME for a second graphical session (Wayland/X11 session)  
***tty3–6*** ---> Pure text-mode virtual consoles, available for additional login sessions.  

***RDP*** ---> Runs an *xrdp* daemon on the remote server that captures a *virtual graphical desktop (no physical monitor needed)* and streams it over the network on TCP 3389.  
Your local machine runs an *RDP client* that sends your keyboard & mouse input back to the server.  
Giving you *full GUI access* to a headless machine, without SSH.  

---

***Listing processes:***  

**ps** ---> Show processes in your current terminal only.  
**pstree -p** ---> View the processes dependency tree with PIDs.  
**pstree -p \<user\>** ---> View the processes tree PIDs used by \<user\>.  

**ps auww** ---> See all processes running in terminal sessions.  
**ps aux** ---> List all currently running processes.  
**ps aux | less** ---> List them in less pager.  

**pgrep -f firefox** ---> Return directly the PID of the process named "firefox".  
**pgrep -lau \<user\>** ---> Show all PIDs used by \<user\>.  
**pgrep -lat pts/x** ---> See processes running in a certain pts session.  
**ps aux | grep "firefox"** ---> Search for the PID of a certain process by its name (to **kill** later for example).  
**ps -ef** ---> UNIX style, all processes, [xxxx] are kernel processes.  

---

***Process States:*** tracked by the kernel, STAT column:  

**R** ---> Running (using CPU cycles) or runnable (waiting for CPU time).  
**S** ---> Sleeping interruptible (waiting for an event).  
**D** ---> Sleeping uninterruptible.  
**K** ---> Killable sleep — like D but accepts kill signals.  
**I** ---> Idle.  
**T** ---> Stopped/suspended (by signal, like Ctrl+Z).  
**Z** ---> Zombie (process finished, waiting for parent to reap it (clean up)).  
**X** ---> Dead (fully cleaned up, never visible in ps output).  

---

***Job Control:***  

**gnome-calculator &** ---> Run a process in background.  
**Ctrl+Z** ---> Suspend the currently running process (R -> T).  
**jobs** ---> List active jobs with their state and number.  
**fg \<jobNum\>** ---> Bring back a suspended process in current terminal.  
**bg \<jobNum\>** ---> Bring back a suspended process to run in the background.  

---

***Manipulating processes:***  

**pkill \<process name\>** ---> Uses pattern recognition to kill all processes matching.  
    **-t** ---> Specify tty session to close (ex.: pts/2 or tty2) (know from **w**).  

**killall \<string\>** ---> Kill ALL running processes of the name "string".  

**kill -l** ---> List all process signals.  
**kill -n \<PID\>** ---> Kill a process or session.  
    **-9 = [SIGKILL]** ---> Terminate NOW.  
    **-15 = [SIGTERM]** ---> Default, kill gracefully.  
    **-19 = [SIGSTOP]** ---> Keep process in memory but ban from CPU cycles.  
    **-18 = [SIGCONT]** ---> Resume the stopped process (by -19).  
    **-u \<user\>** ---> Kill all sessions of this \<user\>.  

---

***systemd units:***  

**systemd** is PID 1, forks all other processes.  

    **- .service:** web or db services (or exporters that listen on ports)  
    **- .socket:** also listen to ports, but initialize services of the same name once a request is received (service starts once it's actually needed)  
    **- .path:** point to directories, and activate a service once one is accessed  
    **- .target:** used to group services that need to run together  

**systemctl** ---> Control systemd units.  

**systemctl list-units [ -t \<unit\> ]** ---> List all systemd units on the system.  
**systemctl list-unit-files --type=service** ---> List the enabled or disabled states of all service units.  

**systemctl status sshd.service** ---> Check a service state.  

    - **Loaded** ---> Which init config file the service is running off of  
        - *enabled*: will this service run on boot or not  
        - *vendor preset*: is the default to enabled or disabled  
    - **Active** ---> Is the service currently up or not  
    - **Docs** ---> Useful man pages for this service  
    - **Drop-in** ---> Path to /etc/systemd/system/sshd.service.d/00-edits.conf where some overrides are used  
    - **+ some logs the service prints**  

**After adding drop-in configs, the service will need to be reloaded or restarted to fetch the new configs and load them to memory in place of the old configs, BY: systemctl daemon-reload**  

Using: **systemctl reload sshd.service** or **systemctl restart sshd.service**  

***reload VS restart***  

    reload ---> Keeps services up while updating configs + keeps same PID after reloading.  
    restart ---> Stops the service entirely first (downtime) and cold starts it again after with the new configs now, getting a new PID afterwards.  

**Not all processes support a reload transaction, -1 |"SIGHUP" kill, to redial the contents from the fs to memory while the process is running.**  

**systemctl enable --now sshd.service** ---> Enables the service, and starts it now.  

    Q: *How does a service start on boot after it's been enabled?*  
    A: *A symlink to that service gets created under multi-user.target.wants, which is a target of services the system starts automatically on boot.*  

**systemctl list-dependencies multi-user.target** ---> Lists all the units that belong to this target.  
**systemctl list-dependencies --reverse httpd** ---> Show the targets httpd belongs to.  

**systemctl get-default** ---> Prints the default target that systemd initializes on boot.  
**systemctl set-default** ---> Change that .target.  

**systemctl mask httpd** ---> Makes the service unstartable. (creates a symlink of the service to /dev/null)  
**systemctl unmask httpd** ---> Deletes that symlink and returns the service to startable.  

[root@rocky1 ~]# **systemctl list-dependencies multi-user.target**  

```
multi-user.target
● ├─auditd.service
● ├─chronyd.service
● ├─crond.service
● ├─firewalld.service
● ├─httpd.service
○ ├─irqbalance.service
... output omitted ...
● ├─rsyslog.service
● ├─sshd.service
● ├─basic.target
● │ ├─-.mount
○ │ ├─microcode.service
● │ ├─paths.target
● │ ├─slices.target
● │ │ ├─-.slice
● │ │ └─system.slice
● │ ├─sockets.target
● │ │ ├─dbus.socket
... output omitted ...
```

**vim /etc/httpd/conf/httpd.conf** ---> Change httpd.service port.  

Thank you :)
