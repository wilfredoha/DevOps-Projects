In this project you will be tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress. WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

# This project consists of two parts:

1. Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.

2. Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying  Web and DB tiers of Web solution.

As a DevOps engineer, your deep understanding of core components of web solutions and ability to troubleshoot them will play essential role in your further progress and development.

# Three-tier Architecture
Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture.

Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.


![three_tier_application](https://user-images.githubusercontent.com/85270361/210136746-989083fe-d67f-4454-97d5-5f7cc8f921f3.PNG)


1. Presentation Layer (PL): This is the user interface such as the client server or browser on your laptop.
2. Business Layer (BL): This is the backend program that implements business logic. Application or Webserver
3. Data Access or Management Layer (DAL): This is the layer for computer data storage and data access. [Database](https://www.computerhope.com/jargon/d/database-server.htm) Server or File System Server such as [FTP](https://dinahosting.com/ayuda/que-es-ftp-y-para-que-sirve/) server, or [NFS](https://www.techtarget.com/searchenterprisedesktop/definition/Network-File-System) Server

In this project, you will have the hands-on experience that showcases Three-tier Architecture while also ensuring that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively.

You will be working with several storage and disk management concepts, to have a better understanding, watch following video:
[Disk management in Linux](https://youtu.be/dHbeFhb7mmY)

Note: We are gradually introducing new AWS elements into our solutions, but do not be worried if you do not fully understand AWS Cloud Services yet, there are Cloud focused projects ahead where we will get into deep details of various Cloud concepts and technologies – not only AWS, but other Cloud Service Providers as well.

# Your 3-Tier Setup
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server

Use RedHat OS for this project

By now you should know how to spin up an EC2 instanse on AWS, but if you forgot – refer to [01 - AWS - EC2](https://github.com/wilfredoha/DevOps-Projects/tree/main/01%20-%20AWS%20-%20EC2)
In previous projects we used ‘Ubuntu’, but it is better to be well-versed with various Linux distributions, thus, for this projects we will use very popular distribution called ‘RedHat’ (it also has a fully compatible derivative – CentOS)

Note: for Ubuntu server, when connecting to it via SSH/Putty or any other tool, we used ubuntu user, but for RedHat you will need to use ec2-user user. Connection string will look like ec2-user@<Public-IP>

# LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”.

Step 1 — Prepare a Web Server

1. Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.
Learn How to Add EBS Volume to an EC2 instance [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-attaching-volume.html)

![5022](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/creating_3_volumes.png)


1. Attach all three volumes one by one to your Web Server EC2 instance

![attach01](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/attach_01.png)

![attach02](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/attach_02.png)

2. Open up the Linux terminal to begin configuration

3. Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their 
names will likely be xvdf, xvdh, xvdg.

```
lsblk
```

![lsblk](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/lsblk.png)

4. Use df -h command to see all mounts and free space on your server

5. Use gdisk utility to create a single partition on each of the 3 disks

```
sudo gdisk /dev/xvdf
```

PROMPT: 

```
 GPT fdisk (gdisk) version 1.0.3

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries.

Command (? for help branch segun-edits: p
Disk /dev/xvdf: 20971520 sectors, 10.0 GiB
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): D936A35E-CE80-41A1-B87E-54D2044D160B
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 20971486
Partitions will be aligned on 2048-sector boundaries
Total free space is 2014 sectors (1007.0 KiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048        20971486   10.0 GiB    8E00  Linux LVM

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): yes
OK; writing new GUID partition table (GPT) to /dev/xvdf.
The operation has completed successfully.
Now,  your changes has been configured succesfuly, exit out of the gdisk console and do the same for the remaining disks.
```


5. Use lsblk utility to view the newly configured partition on each of the 3 disks.

![5025](https://user-images.githubusercontent.com/85270361/210137616-558c1857-6fca-40e4-a833-f4605fcd46ca.PNG)


6. Install lvm2 package using sudo yum install lvm2. Run sudo lvmdiskscan command to check for available partitions.

Note: Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we 
shall use yum command instead.

7. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```

8. Verify that your Physical volume has been created successfully by running sudo pvs

![5026](https://user-images.githubusercontent.com/85270361/210137702-7d0c0151-0cf8-4d61-a1af-a146afc9e26a.PNG)


9. Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

```
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
```

10. Verify that your VG has been created successfully by running 

```
sudo vgs
```

![5027](https://user-images.githubusercontent.com/85270361/210137794-23e31c97-1f34-469b-b7da-3456fb4e4dcd.PNG)


11. Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of
 the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.
 
```
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```

12. Verify that your Logical Volume has been created successfully by running 

```
sudo lvs
```

![5029](https://user-images.githubusercontent.com/85270361/210137927-8aef5842-f176-45a4-bf5c-817fc1eeb1fb.PNG)


13. Verify the entire setup

```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk 
```

14. Use mkfs.ext4 to format the logical volumes with ext4 filesystem

```
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

15. Create /var/www/html directory to store website files

```
sudo mkdir -p /var/www/html
```

16. Create /home/recovery/logs to store backup of log data

```
sudo mkdir -p /home/recovery/logs
```

17. Mount /var/www/html on apps-lv logical volume

```
sudo mount /dev/webdata-vg/apps-lv /var/www/html/
```

18. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before 
mounting the file system)

```
sudo rsync -av /var/log/. /home/recovery/logs/
```


19. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above 
is very important)

```
sudo mount /dev/webdata-vg/logs-lv /var/log
```

20. Restore log files back into /var/log directory

```
sudo rsync -av /home/recovery/logs/. /var/log
```

21. Update /etc/fstab file so that the mount configuration will persist after restart of the server.
Click on the next Step To update the /etc/fstab file
