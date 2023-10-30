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

# INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

1. Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.
2. In your GitHub account create a new repository and name it ansible-config-mgt.
3. Instal Ansible

```
sudo apt update

sudo apt install ansible -y
```

Check your Ansible version by running ansible --version

![ansible_version](https://github.com/wilfredoha/DevOps-Projects/blob/main/12%20-%20ANSIBLE%20CONFIGURATION%20MANAGEMENT%20%E2%80%93%20AUTOMATE%20PROJECT%208%20TO%2011/images/ansible_version.png)

4. Configure Jenkins build job to save your repository content every time you change it.

- Create a new Freestyle project ansible in Jenkins and point it to your 'ansible-config-mgt' repository.
- Configure Webhook in GitHub and set webhook to trigger ansible build.
- Configure a Post-build job to save all (**) files.

5. Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder

```
ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
```

![readme](https://github.com/wilfredoha/DevOps-Projects/blob/main/12%20-%20ANSIBLE%20CONFIGURATION%20MANAGEMENT%20%E2%80%93%20AUTOMATE%20PROJECT%208%20TO%2011/images/readme.png)

>Note: Trigger Jenkins project execution only for /main (master) branch.

Now your setup will look like this:

![6035](https://user-images.githubusercontent.com/85270361/210153832-75e74f67-0654-4fc1-bcdd-08c3d5a8fa76.PNG)


>Tip Every time you stop/start your Jenkins-Ansible server – you have to reconfigure GitHub webhook to a new IP address, in order to avoid it, it makes sense to allocate an Elastic IP to your Jenkins-Ansible server. Note that Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.

Step 2 – Prepare your development environment using Visual Studio Code

1. First part of 'DevOps' is 'Dev', which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose
whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs.

2. After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

3. Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance

```
git clone <ansible-config-mgt repo link>
```

# BEGIN ANSIBLE DEVELOPMENT

1.In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.

Tip: Give your branches descriptive and comprehensive names, for example, if you use Jira or Trello as a project management tool – include ticket number (e.g. PRJ-145) in the name of your branch and add a topic and a brief description what this branch is about – a bugfix, hotfix, feature, release (e.g. feature/prj-145-lvm)

2. Checkout the newly created feature branch to your local machine and start building your code and directory structure
3. Create a directory and name it playbooks – it will be used to store all your playbook files.
4. Create a directory and name it inventory – it will be used to keep your hosts organised.
5. Within the playbooks folder, create your first playbook, and name it common.yml
6. Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.

Step 4 – Set up an Ansible Inventory
An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:

To learn how to setup SSH agent and connect VS Code to your Jenkins-Ansible instance, please see this video:

- For Windows users – ssh-agent on windows 
- For Linux users – ssh-agent on linux 

Windows Installation:
Part 1: https://youtu.be/R-qcpehB5HY
part 2: https://youtu.be/jsNIlK5s6pI

OpenSSH Installation:
- https://docs.microsoft.com/en-us/wind...

- https://docs.microsoft.com/en-us/wind...


Read about ssh-agent:
- https://www.ssh.com/academy/ssh/agent
- https://smallstep.com/blog/ssh-agent-....

For Linux users:
https://youtu.be/RRRQLgAfcJw


```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

Confirm the key has been added with the command below, you should see the name of your key

```
ssh-add -l
```

Now, ssh into your Jenkins-Ansible server using ssh-agent

```
ssh -A ubuntu@public-ip
```

Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.

Update your inventory/dev.yml file with this snippet of code:

```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
```
# CREATE A COMMON PLAYBOOK

Step 5 – Create a Common Playbook
It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in inventory/dev.

In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your playbooks/common.yml file with following code:

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```

Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

Feel free to update this playbook with following tasks:

- Create a directory and a file inside it
- Change timezone on all servers
- Run some shell script

For a better understanding of Ansible playbooks – watch this video from RedHat ( https://youtu.be/ZAdJ7CdN7DY ) and read this article ( https://www.redhat.com/en/topics/automation/what-is-an-ansible-playbook ).

Step 6 – Update GIT with the latest code
Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle".

Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.

Commit your code into GitHub:

1. use git commands to add, commit and push your branch to GitHub.

```
git status

git add <selected files>

git commit -m "commit message"
```

2. Create a Pull request (PR)

3. Wear a hat of another developer for a second, and act as a reviewer.

4. If the reviewer is happy with your new feature development, merge the code to the master branch.

5. Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.

# RUN FIRST ANSIBLE TEST

Step 7 – Run first Ansible test

Now, it is time to execute ansible-playbook command and verify if your playbook actually works:

```
cd ansible-config-mgt
```

```
ansible-playbook -i inventory/dev.yml playbooks/common.yml
```

You can go to each of the servers and check if wireshark has been installed by running which wireshark or wireshark --version


![6037](https://user-images.githubusercontent.com/85270361/210154560-3fa50ba8-67b4-45c8-9da1-8d9bdd45e418.PNG)


Your updated with Ansible architecture now looks like this:

![6038](https://user-images.githubusercontent.com/85270361/210154593-092a4ee2-ab8b-4212-a260-8845c3f8693a.PNG)


Optional step – Repeat once again
Update your ansible playbook with some new Ansible tasks and go through the full checkout -> change codes -> commit -> PR -> merge -> build -> ansible-playbook cycle again to see how easily you can manage a servers fleet of any size with just one command!