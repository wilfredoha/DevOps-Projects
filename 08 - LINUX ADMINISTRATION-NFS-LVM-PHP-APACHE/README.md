# DEVOPS TOOLING WEBSITE SOLUTION

In previous [07 - WEB SOLUTIONS - WORDPRESS](https://github.com/wilfredoha/DevOps-Projects/tree/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS) you implemented a WordPress based solution that is ready to be filled with content and can be used as a full fledged website or blog. Moving further we will add some more value to our solutions that your DevOps team could utilize.
We want to introduce a set of DevOps tools that will help our team in day to day activities in managing, developing, testing, deploying and monitoring different projects.

The tools we want our team to be able to use are well known and widely used by multiple DevOps teams, so we will introduce a single DevOps Tooling Solution that will consist of:

1. **Jenkins** – free and open source automation server used to build CI/CD pipelines.
2. **Kubernetes** – an open-source container-orchestration system for automating computer application deployment, scaling, and management.
3. **Jfrog Artifactory** – Universal Repository Manager supporting all major packaging formats, build tools and CI servers. Artifactory.
4. **Rancher** – an open source software platform that enables organizations to run and manage Docker and Kubernetes in production.
5. **Grafana** – a multi-platform open source analytics and interactive visualization web application.
6. **Prometheus** – An open-source monitoring system with a dimensional data model, flexible query language, efficient time series database and modern alerting approach.
8. **Kibana** – Kibana is a free and open user interface that lets you visualize your Elasticsearch data and navigate the Elastic Stack.

>Note: Do not feel overwhelmed by all the tools and technologies listed above, we will gradually get ourselves familiar with them in upcoming projects!

## Side Self Study
- Read about Network-attached storage (NAS), Storage Area Network (SAN) and related protocols like NFS, (s)FTP, SMB, iSCSI. Explore what Block-level storage is and how it is used by Cloud Service providers, know the difference from Object storage.

- On the example of AWS services understand the difference between Block Storage, Object Storage and Network File System.

## Setup and technologies used
As a member of a DevOps team, you will implement a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible.

In this project you will implement a solution that consists of following components:

1. Infrastructure: AWS
2. Webserver Linux: Red Hat Enterprise Linux 8
3. Database Server: Ubuntu 20.04 + MySQL
4. Storage Server: Red Hat Enterprise Linux 8 + NFS Server
5. Programming Language: PHP
6. Code Repository: GitHub

On the diagram below you can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware – for Web Servers it look like a local file system from where they can serve the same files.

![6000](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/three_tier.png)

>It is important to know what storage solution is suitable for what use cases, for this – you need to answer following questions: what data will be stored, in what format, how this data will be accessed, by whom, from where, how frequently, etc. Base on this you will be able to choose the right storage system for your solution.

# STEP 1 – PREPARE NFS SERVER

1. Spin up a new EC2 instance with RHEL Linux 8 Operating System.
2. Based on your LVM experience from [07 - WEB SOLUTIONS - WORDPRESS](https://github.com/wilfredoha/DevOps-Projects/tree/main/07%20-%20WEB%20SOLUTIONS%20-%20WORDPRESS), Configure LVM on the Server.

- Instead of formating the disks as ext4 you will have to format them as xfs
- Ensure there are 3 Logical Volumes. lv-opt lv-apps, and lv-logs

Connect to your NFS Server

```
lsblk
```

![lsblk](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/lsblk.png)

Create 3 Volumes ans attach them to de NFS server

![volume_creation](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/volume_creation.png)

```
lsblk
```

![lsblk_volumnes_created](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/lsblk_volumnes_created.png)

```
sudo gdisk /dev/xvdf
sudo gdisk /dev/xvdg
sudo gdisk /dev/xvdh
```

![gdisk](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/gdisk.png)

![lsblk_after_gdisk](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/lsblk_after_gdisk.png)

Isntall lvm package

```
sudo yum install lvm2 -y
```

```
sudo lvmdiskscan
```

```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```

![pvs](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/pvs.png)

Create a Volume Group

```
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
```

![vgs](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/vgs.png)

Create Logical Volumes. lv-opt lv-apps, and lv-logs

```
sudo lvcreate -n lv-apps -L 9G webdata-vg
sudo lvcreate -n lv-logs -L 9G webdata-vg
sudo lvcreate -n lv-opt -L 9G webdata-vg
```

![lvcreate](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/lvcreate.png)

Format disks as xfs

```
sudo mkfs -t xfs /dev/webdata-vg/lv-apps
sudo mkfs -t xfs /dev/webdata-vg/lv-logs
sudo mkfs -t xfs /dev/webdata-vg/lv-opt
```

3. Create mount points on /mnt directory for the logical volumes as follow:

```
sudo mkdir /mnt/apps
sudo mkdir /mnt/logs
sudo mkdir /mnt/opt
```
Mount lv-apps on /mnt/apps – To be used by webservers
Mount lv-logs on /mnt/logs – To be used by webserver logs
Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8

```
sudo mount /dev/webdata-vg/lv-apps /mnt/apps
sudo mount /dev/webdata-vg/lv-logs /mnt/logs
sudo mount /dev/webdata-vg/lv-opt /mnt/opt
```

4. Install NFS server, configure it to start on reboot and make sure it is up and running

```
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```

![nsf_server_enabled](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/nsf_server_enabled.png)

5. Export the mounts for webservers’ subnet cidr to connect as clients. For simplicity, you will install your all three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security.
To check your subnet cidr – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:

![subnet_cidr](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/subnet_cidr.png)

Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:

```
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt

sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt

sudo systemctl restart nfs-server.service
```

Configure access to NFS for clients within the same subnet (example of Subnet CIDR – 172.31.32.0/20 ):

```
sudo vi /etc/exports

/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)

Esc + :wq!

sudo exportfs -arv
```

6. Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)

```
rpcinfo -p | grep nfs
```

![rpcinfo](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/rpcinfo.png)

>Important note: In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049

![nfs-sg](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/nfs-sg.png)

# STEP 2 — CONFIGURE THE DATABASE SERVER
By now you should know how to install and configure a MySQL DBMS to work with remote Web Server

1. Install MySQL server
```
sudo apt update
sudo apt upgrade -y
sudo apt install mysql-server -y
sudo systemctl start mysql.service
sudo systemctl status mysql.service
```
2. Create a database and name it tooling
```
sudo mysql
```

```
mysql> CREATE DATABASE tooling;
```

3. Create a database user and name it webaccess

```
mysql> CREATE USER 'webaccess'@'172.31.0.0/16' IDENTIFIED BY 'password'
```

4. Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr

```
mysql> GRANT ALL PRIVILEGES ON tooling.*  TO 'webaccess'@'172.31.0.0/16';
```

```
mysql> FLUSH PRIVILEGES;
```

## Step 3 — Prepare the Web Servers
We need to make sure that our Web Servers can serve the same content from shared storage solutions, in our case – NFS Server and MySQL database.

You already know that one DB can be accessed for reads and writes by multiple clients. For storing shared files that our Web Servers will use – we will utilize NFS and mount previously created Logical Volume lv-apps to the folder where Apache stores files to be served to the users (/var/www).

This approach will make our Web Servers stateless, which means we will be able to add new ones or remove them whenever we need, and the integrity of the data (in the database and on NFS) will be preserved.

## During the next steps we will do following:

- Configure NFS client (this step must be done on all three servers)
- Deploy a Tooling application to our Web Servers into a shared NFS folder
- Configure the Web Servers to work with a single MySQL database


1. Launch a new EC2 instance with RHEL 8 Operating System

2. Install NFS client

```
sudo yum update -y
```

```
sudo yum install nfs-utils nfs4-acl-tools -y
```

3. Mount /var/www/ and target the NFS server’s export for apps

```
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
```

4. Verify that NFS was mounted successfully by running df -h. Make sure that the changes will persist on Web Server after reboot:

```
sudo vi /etc/fstab
```

add following line

```
<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
```

![nfs-server-from-web](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/nfs-server-from-web.png)

Create a file in the Web Sever inside /var/www/

![nfs-test-md-01](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/nfs-test-md-01.png)

The file will be copied to /mnt/apps/ inside the NFS Server

![nfs-test-md-02](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/nfs-test-md-02.png)

5. Install [Remi’s repository](http://www.servermom.org/how-to-enable-remi-repo-on-centos-7-6-and-5/2790/), Apache and PHP


```
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo dnf module reset php
sudo dnf module enable php:remi-7.4
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
sudo setsebool -P httpd_execmem 1
```

## Repeat steps 1-5 for another 2 Web Servers.

6. Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. 
>If you see the same files – it means NFS is mounted correctly. You can try to create a new file touch test.txt from one server and check if the same file is accessible from other Web Servers.

7. Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs. 

```
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/logs /var/log/httpd
```

Repeat step №4 to make sure the mount point will persist after reboot.

```
sudo vi /etc/fstab
```

add following line

```
<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
```

8. Clone the next [repository](https://github.com/wilfredoha/tooling) to your Github account.

9. Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

>Note 1: Do not forget to open TCP port 80 on the Web Server.

>Note 2: If you encounter 403 Error – check permissions to your /var/www/html folder and also disable SELinux **sudo setenforce 0** To make this change permanent – open following config file **sudo vi /etc/sysconfig/selinux** and set **SELINUX=disabled** then restart httpd.

After this you can go to a web browser an enter the public IP

![test_web-server](https://github.com/wilfredoha/DevOps-Projects/blob/main/08%20-%20LINUX%20ADMINISTRATION-NFS-LVM-PHP-APACHE/images/test_web-server.png)

10. Update the website’s configuration to connect to the database (in /var/www/html/functions.php file). Apply tooling-db.sql script to your database using this command mysql -h <databse-private-ip> -u <db-username> -p <db-pasword> < tooling-db.sql

11. Create in MySQL a new admin user with username: myuser and password: password:

INSERT INTO ‘users’ (‘id’, ‘username’, ‘password’, ’email’, ‘user_type’, ‘status’) VALUES
-> (1, ‘myuser’, ‘5f4dcc3b5aa765d61d8327deb882cf99’, ‘user@mail.com’, ‘admin’, ‘1’);

12. Open the website in your browser http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/index.php and make sure you can login 
into the websute with myuser user.
  
  
![6004](https://user-images.githubusercontent.com/85270361/210139699-6c023692-c7cb-404a-80c6-848434967fe9.PNG)

  
Congratulations!
You have just implemented a web solution for a DevOps team using LAMP stack with remote Database and NFS servers
  

![6005](https://user-images.githubusercontent.com/85270361/210139730-61025cd1-fa95-4f0f-817e-0f9e1037022c.PNG)
