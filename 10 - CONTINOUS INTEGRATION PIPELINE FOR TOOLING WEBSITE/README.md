# TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS

In previous project [09 - LOAD BALANCER SOLUCTION WITH APACHE](https://github.com/wilfredoha/DevOps-Projects/tree/main/09%20-%20LOAD%20BALANCER%20SOLUCTION%20WITH%20APACHE) we introduced horizontal scalability concept, which allow us to add new Web Servers to our Tooling Website and 
you have successfully deployed a set up with 3 Web Servers and also a Load Balancer to distribute traffic between them. If it is just two or three servers – it is not a big deal to configure them manually. Imagine that you would need to repeat the same task over and over again adding dozens or even hundreds of servers.

DevOps is about Agility, and speedy release of software and web solutions. One of the ways to guarantee fast and repeatable deployments is Automation of routine tasks.

In this project we are going to start automating part of our routine tasks with a free and open source automation server – Jenkins. 
It is one of the most popular CI/CD tools, it was created by a former Sun Microsystems developer Kohsuke Kawaguchi and the project originally had a named "Hudson".

Acording to Circle CI, Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub https://github.com/**yourname**/tooling will be automatically updated to the Tooling Website.

## Side Self Study
Read about Continuous Integration, Continuous Delivery and Continuous Deployment.

## Task
Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

Here is how your updated architecture will look like upon competion of this project:
  
![6011](https://user-images.githubusercontent.com/85270361/210151603-8c6fedbc-4835-456a-97a9-9f282a7bfbf6.PNG)

# INSTALL AND CONFIGURE JENKINS SERVER

Step 1 – Install Jenkins server

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

2. Install JDK (since Jenkins is a Java-based application)

```
sudo apt update
sudo apt install openjdk-17-jdk
```

3. Install Jenkins

```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update

sudo apt-get install jenkins
```

Make sure Jenkins is up and running

```
sudo systemctl status jenkins
```

4. By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

![sg_jenkins](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/sg_jenkins.png)


5. Perform initial Jenkins setup.
From your browser access http://**Jenkins-Server-Public-IP-Address-or-Public-DNS-Name**:8080

You will be prompted to provide a default admin password
  
![unlock_jenkins](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/unlock_jenkins.png)

  
Retrieve it from your server:
  
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
  
Then you will be asked which plugings to install – choose suggested plugins.
 
![sugested_plugins_jenkins](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/sugested_plugins_jenkins.png)

Once plugins installation is done – create an admin user and you will get your Jenkins server address.

The installation is completed!
  
![installed_jenkins](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/installed_jenkins.png)

Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

1. Enable webhooks in your GitHub repository settings

![github_webhook](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/github_webhook.png)
  
2. Go to Jenkins web console, click "New Item" and create a "Freestyle project"

![jenkins_new_item](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/jenkins_new_item.png)

To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself
  
![repo_url](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/repo_url.png)

  
In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![add_repo_to_jenkins](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/add_repo_to_jenkins.png)
  
Save the configuration and let us try to run the build. For now we can only do it manually.
Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it under #1  
  
![build_project_jenkins](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/build_project_jenkins.png)

You can open the build and check in "Console Output" if it has run successfully.

If so – congratulations! You have just made your very first Jenkins build!

But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

3. Click "Configure" your job/project and add these two configurations
Configure triggering the job from GitHub webhook:

![config_01](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/config_01.png)

Click on **Configure**

![config_02](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/config_0002.png)

![config_03](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/config_03.png)

Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".

![config_04](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/config_04.png)

Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the main branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.

![second_build](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/second_build.png)

  
You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

```
 ls -l /var/lib/jenkins/jobs/tooling\ github/builds/2/archive/
```
![archived](https://github.com/wilfredoha/DevOps-Projects/blob/main/10%20-%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/images/archived.png)
  
# CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH

##  Step 3 – Configure Jenkins to copy files to NFS server via SSH
Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "Publish Over SSH".

1. Install "Publish Over SSH" plugin.
On main dashboard select "Manage Jenkins" and choose "Plugins" menu item.

On "Available plugins" tab search for "Publish Over SSH" plugin and install it

![6025](https://user-images.githubusercontent.com/85270361/210152439-c65af162-b6da-43a6-bc3b-a0b43ef30693.PNG)

2. Configure the job/project to copy artifacts over to NFS server.
On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

1. Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
2. Arbitrary name
3. Hostname – can be private IP address of your NFS server
4. Username – ec2-user (since NFS server is based on EC2 with RHEL 8)
5. Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server

Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive
SSH connections.


![6026](https://user-images.githubusercontent.com/85270361/210152524-d8811bcf-1bac-4693-9f55-216a0c394973.PNG)

Save the configuration, open your Jenkins job/project configuration page and add another one "Post-build Action"


![6027](https://user-images.githubusercontent.com/85270361/210152559-83e84c9b-5740-4e40-91b3-9b75fb319ccd.PNG)


Configure it to send all files probuced by the build into our previouslys define remote directory. In our case we want to copy all
files and directories – so we use **.

If you want to apply some particular pattern to define which files to send – use this syntax.


![6028](https://user-images.githubusercontent.com/85270361/210152615-2334a0ce-e78a-413d-b2a9-b93dffed2c17.PNG)


Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.

Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:

```
SSH: Transferred 25 file(s)
Finished: SUCCESS
```

To make sure that the files in /mnt/apps have been udated – connect via SSH/Putty to your NFS server and check README.MD file

```
cat /mnt/apps/README.md
```

If you see the changes you had previously made in your GitHub – the job works as expected.

Congratulations!
You have just implemented your first Continous Integration solution using Jenkins CI. Watch out for advanced CI configurations in 
upcoming projects.

![6029](https://user-images.githubusercontent.com/85270361/210152690-24e9c16c-704b-4589-a725-e8299e75d6a3.PNG)
