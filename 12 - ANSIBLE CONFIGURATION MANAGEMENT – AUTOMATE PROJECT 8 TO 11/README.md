# ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATE PROJECT 8 TO 11

You have been implementing some interesting projects up untill now, and that is awesome.

In Projects 8 to 11 you had to perform a lot of manual operations to seet up virtual servers, install and configure required software, deploy your web application.

This Project will make you appreciate DevOps tools even more by making most of the routine tasks automated with Ansible Configuration Management, at the same time you will become confident at writing code using declarative language such as YAML.

**Ansible Client as a Jump Server (Bastion Host)**: A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server – it provide better security and reduces attack surface.

On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

![bastion](https://github.com/wilfredoha/DevOps-Projects/blob/main/12%20-%20ANSIBLE%20CONFIGURATION%20MANAGEMENT%20%E2%80%93%20AUTOMATE%20PROJECT%208%20TO%2011/images/bastion.png)

In a later project we will see a Bastion host in proper action. But for now, we will develop Ansible scripts to simulate the use of a Jump box/Bastion host to access our Web Servers.

## Task
1. Install and configure Ansible client to act as a Jump Server/Bastion Host
2. Create a simple Ansible playbook to automate servers configuration