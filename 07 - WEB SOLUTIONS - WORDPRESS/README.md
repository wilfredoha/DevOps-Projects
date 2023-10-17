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

```
df -h
```

![df-h](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/df%20-h.png)

5. Use gdisk utility to create a single partition on each of the 3 disks

```
sudo gdisk /dev/xvdf
```

PROMPT: 

![gdisk](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/gdisk.png)

Now,  your changes has been configured succesfuly, exit out of the gdisk console and do the same for the remaining disks.


5. Use lsblk utility to view the newly configured partition on each of the 3 disks.

![lsblk.checkgdisk](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/lsblk.checkgdisk.png)


6. Install lvm2 package using sudo yum install lvm2. Run sudo lvmdiskscan command to check for available partitions.

>Note: Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.

```
sudo yum install lvm2 -y
```

7. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```

8. Verify that your Physical volume has been created successfully by running sudo pvs

```
sudo pvs
```

![5026](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/sudo_pvs.png)


9. Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

```
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
```

10. Verify that your VG has been created successfully by running 

```
sudo vgs
```

![5027](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/vgs_check.png)


11. Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. 

>NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.
 
```
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```

12. Verify that your Logical Volume has been created successfully by running 

```
sudo lvs
```

![5029](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/lvs_check.png)


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

18. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)

```
sudo rsync -av /var/log/. /home/recovery/logs/
```


19. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very important)

```
sudo mount /dev/webdata-vg/logs-lv /var/log
```

20. Restore log files back into /var/log directory

```
sudo rsync -av /home/recovery/logs/. /var/log
```

21. Update /etc/fstab file so that the mount configuration will persist after restart of the server.The UUID of the device will be used to update the /etc/fstab file;

```
sudo blkid
```

![5030](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/blkid.png)

```
sudo nano /etc/fstab
```

Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.

![5031](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/fstabconfig.png)


22. Test the configuration and reload the daemon

```
sudo mount -a
sudo systemctl daemon-reload
```

23. Verify your setup by running df -h, output must look like this:

![5032](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/df-h-setup-running.png)


# Prepare the Database Server
Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

# Install WordPress on your Web Server EC2

1. Update the repository

```
sudo yum -y update
```

2. Install wget, Apache and it’s dependencies

```
sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
```

3. Start Apache

```
sudo systemctl enable httpd
sudo systemctl start httpd
```

4. To install PHP and it’s depemdencies

```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```

5. Restart Apache

```
sudo systemctl restart httpd
```

6. Download wordpress and copy wordpress to var/www/html

```
  sudo mkdir wordpress
  cd   wordpress
  sudo wget http://wordpress.org/latest.tar.gz
  sudo tar xzvf latest.tar.gz
  sudo rm -rf latest.tar.gz
  sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
  sudo cp -R wordpress /var/www/html/
```


7. Configure SELinux Policies

```
sudo chown -R apache:apache /var/www/html/wordpress
sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
sudo setsebool -P httpd_can_network_connect=1
```

# Install MySQL on your DB Server EC2

```
sudo yum update
sudo yum install mysql-server
```

Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and enable it so it will be running even after reboot:

```
sudo systemctl restart mysqld
sudo systemctl enable mysqld
```

- Configure DB to work with WordPress

```
sudo mysql
CREATE DATABASE wordpress;
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```

```
sudo nano /etc/my.cnf
```

![bind](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/access_bind.png)

- Configure WordPress to connect to remote database.
>Hint: Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address, so in the Inbound Rule configuration specify source as /32

![5034](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/security_g_3306.png)


1. Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client

```
sudo yum install mysql
sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
```

2. Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.

![test_mysql_connection](https://github.com/wilfredoha/DevOps-Projects/blob/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS/images/test_mysql_connection.png)

3. Change permissions and configuration so Apache could use WordPress:

```
sudo cp wp-config-sample.php wp-config.php
```

```
sudo nano wp-config.php
```

4. Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation’s IP)

5. Try to access from your browser the link to your WordPress 

http://<Web-Server-Public-IP-Address>/wordpress/
  
![5040](https://user-images.githubusercontent.com/85270361/210138663-27322aaf-f020-4261-a695-a98dc5f2387c.PNG)

  
Fill out your DB credentials:
  
![5050](https://user-images.githubusercontent.com/85270361/210138669-88078473-e459-4864-856f-4e6cb3d457d6.PNG)


If you see this message – it means your WordPress has successfully connected to your remote MySQL database
  

![5060](https://user-images.githubusercontent.com/85270361/210138718-d9c15bc9-f5b0-45f8-b613-11882374bf63.PNG)

  
Important: Do not forget to STOP your EC2 instances after completion of the project to avoid extra costs.

CONGRATULATIONS!
You have learned how to configure Linux storage susbystem and have also deployed a full-scale Web Solution using WordPress CMS and
MySQL RDBMS!
  

![5070](https://user-images.githubusercontent.com/85270361/210138753-775e4c79-c634-473a-9330-254b209347d1.PNG)
