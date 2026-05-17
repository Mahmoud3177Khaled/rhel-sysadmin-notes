### Disk Management:
---------------------------------------------------------------------------------------------------------------------------------
Every storage device in Linux lives under **/dev** as a **block device** file.  
Storage is **accessed by mounting** it onto an **empty directory (mount point)** so  everything is **one unified tree**, no drive letters like Windows.  

**lsblk -fp** ---> List all mounted disks.  

Device naming:  

```
  SATA/USB:        /dev/sda, /dev/sdb         partitions: /dev/sda1, /dev/sda2  
  NVMe SSD:        /dev/nvme0n1               partitions: /dev/nvme0n1p1, /dev/nvme0n1p2  
  VMs:             /dev/vda, /dev/vdb         partitions: /dev/vda1, /dev/vda2  
  SD cards:        /dev/mmcblk0               partitions: /dev/mmcblk0p1  

```
LVM (Logical Volume Manager) → instead of raw partitions, aggregates physical disks into a volume group, then carves out flexible logical volumes from it.  
Accessible at: /dev/myvg/mylv or /dev/mapper/...  

Default filesystem on RHEL: XFS. Also supports ext4. exFAT for removable media (RHEL10+).  
***tmpfs and devtmpfs are RAM-based, everything in them is lost on reboot.***  

Commands:  
**df -h** ---> Show all mounted filesystems, total/used/free space. Human readable.  
**du -h <dir>** ---> Show disk usage breakdown inside a directory recursively.  
**du -sh <dir>** ---> Show only the total size summary of a directory.  
**du -sh /etc /boot /var** ---> Summarize multiple dirs at once.  



***Mounting & Unmounting:***  
**lsblk -fp** ---> Shows block device files with UUID, filesystem type, and usage %.  
**mount /dev/sdx /emptyDir** ---> Mount a filesystem manually.  
		- **mount /dev/sda3 /mnt/data** ---> Mount by device name.  
		- **mount UUID="xxxx-..." /mnt/data** ---> Mount by UUID (safer as device names can change 				 																		   					   between reboots, UUID doesn't).
**umount /mnt/data** ---> Unmount a filesystem. It must be idle.  
**lsof <mountpoint>** ---> Show which processes are blocking unmount.  


***Manual mounts don't survive reboots, but persistent mounting via /etc/fstab.***  


Gotchas:

Mount point must exist before mounting, and ideally be empty, if not, existing files in it get hidden (not deleted) while something is mounted there.  

umount fails if the filesystem is busy → something has an open file or a shell is cd'd into it.  
Then either wait for them, cd out, or kill them — then retry umount.  

Removable media auto-mounts at /run/media/USERNAME/LABEL.  

Thank you :)

