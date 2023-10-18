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

3. Create mount points on /mnt directory for the logical volumes as follow:

```
sudo mkdir /mnt/apps
sudo mkdir /mnt/logs
sudo mkdir /mnt/opt
```
Mount lv-apps on /mnt/apps – To be used by webservers
Mount lv-logs on /mnt/logs – To be used by webserver logs
Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8

4. Install NFS server, configure it to start on reboot and make sure it is up and running

```
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```

5. Export the mounts for webservers’ subnet cidr to connect as clients. For simplicity, you will install your all three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security.
To check your subnet cidr – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:

![6001](https://user-images.githubusercontent.com/85270361/210139096-83606401-3872-4c41-819e-9bc8efd36aff.PNG)

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

![6002](https://user-images.githubusercontent.com/85270361/210139194-8b530ac6-c9c3-496a-b4c2-7502fcb25186.PNG)

>Important note: In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049

![6003](https://user-images.githubusercontent.com/85270361/210139251-c4cc5219-a207-40ba-9dec-f38a9b0d2424.PNG)

