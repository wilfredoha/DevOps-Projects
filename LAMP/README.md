# Preparing prerequisites
In order to complete this project you will need an AWS account and a virtual server with Ubuntu Server OS.

[AWS](https://aws.amazon.com/) is the biggest Cloud Service Provider and it offers a free tier account that we are going to leverage for our projects.

Right now, all we need to know is that AWS can provide us with a free virtual server called EC2 (Elastic Compute Cloud) for our needs.

Spinning up a new EC2 instance (an instance of a virtual server) is only a matter of a few clicks.

1. AWS account setup and Provisioning an Ubuntu Server
2. Connecting to your EC2 Instance

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/1.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/2.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/3.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/4.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/5.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/6.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/7.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/8.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/9.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/10.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/11.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/12.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/13.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/14.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/15.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/16.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/17.png)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/48af1af6a84476647e212604261bed8c37839d79/LAMP/images/18.png)

IMPORTANT – save your private key (.pem file) securely and do not share it with anyone! If you lose it, you will not be able to
connect to your server ever again!

For Windows users, you will need a tool called putty to connect to your EC2 Instance. Download Putty Here.
For Mac users, you can simply open up Terminal and use the ssh command to get into the server.

IMPORTANT NOTICE
Both Putty and ssh use the SSH protocol to establish connectivity between computers. It is the most secure protocol because it uses
crypto algorithms to encrypt the data that is transmitted – it uses TCP port 22 which is open for all newly created EC2 intances in
AWS by default. Most of these terminologies will make more sense to you as you proceed. So for now, if nothing makes sense, just 
ignore. But be assured that the information is already registered in your sub-conscious mind. So it will become useful to you soon.

The process to connect to the virtual server is different between Windows and Mac. So lets take a quick tour.

# Connecting to EC2 terminal

## Using the terminal on MAC/Linux

- The terminal is already installed by default. You just need to open it up.
- You do not need to convert to a .ppk file. Just use the same key as downloaded from AWS.
- Change directory into the loacation where your PEM file is. Most likely will be in the Downloads folder

```
cd ~/Downloads
```


IMPORTANT - Anywhere you see these anchor tags < > , going forward, it means you will need to replace the content in there with values 
specific to your situation. For example, if we need you to replace the name you have saved the private key on your machine, we will 
write something like < private-key-name >.

If the private key you downloaded was named my-private-key.pem simply remove the anchor tags and insert my-private-key.pem in the
command you are required to execute. Lets try this and follow the instructions below to get some work done.

- Change premissions for the private key file (.pem), otherwise you can get an error "Bad permissions"

```
sudo chmod 0400 <private-key-name>.pem
```


- Connect to the instance by running

```
ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>
``` 
 
# Using Windows Terminal

Remember the private key your downloaded from AWS while provisioning the server? It is a PEM file format. You can open it up to see
the content and have a glimpse of what a PEM file looks like.

Now, we are going to use that PEM key to connect to our EC2 Instnace via ssh.

On, windows the windows terminal tool is not installed by default, you can install it from here

cd Downloads

IMPORTANT - Anywhere you see these anchor tags < > , going forward, it means you will need to replace the content in there with values
specific to your situation. For example, if we need you to replace the name you have saved the private key on your machine, we will 
write something like < private-key-name >.

If the private key you downloaded was named my-private-key.pem simply remove the anchor tags and insert my-private-key.pem in the 
command you are required to execute. Lets try this and follow the instructions below to get some work done.

Connect to the instance by 

```
ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>
``` 
 
 
 Congratulations! You have just created your very first Linux Server in the Cloud and our set up looks like this now:
  (You are the client)



![projject1-step0](https://user-images.githubusercontent.com/85270361/210112007-5cd14a18-8aaa-4c7a-857e-b18400535bdd.PNG)


Please read information about AWS free tier limits and make sure that you STOP your EC2 instance when you are not using it.

Stop EC2

All we need to know right now is that we can use 750 hours (31.25 days) of t2.micro server per month for the first 12 months FOR FREE.

You can launch and stop new instances when you need to, but by default there is a soft-limit of maximum 5 running instances at the same time. In our first projects we will be using only 1 running instance at a time. When you stop an instance – it stops consuming available hours.

Note that every time you stop and start your EC2 instance – you will have a new IP address, it is normal behavior, so do not forget to update your SSH credentials when you try to connect to your EC2 server.

Let us move on and configure our EC2 machine to serve a Web server!

# STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL

What exactly is Apache?

Apache HTTP Server is the most widely used web server software. Developed and maintained by Apache Software Foundation, Apache is an
open source software available for free. It runs on 67% of all webservers in the world. It is fast, reliable, and secure. It can 
be highly customized to meet the needs of many different environments by using extensions and modules. 

Most WordPress hosting providers use Apache as their web server software. However, websites and other applications can run on other
web server software as well. Such as Nginx, Microsoft’s IIS, etc.

The Apache web server is among the most popular web servers in the world. It’s well documented, has an active community of users, and
has been in wide use for much of the history of the web, which makes it a great default choice for hosting a website.

Install Apache using Ubuntu’s package manager ‘apt’:

```
#update a list of packages in package manager
sudo apt update

#run apache2 package installation
sudo apt install apache2
```

## To verify that apache2 is running as a Service in our OS, use following command

```
sudo systemctl status apache2
```


If it is green and running, then you did everything correctly – you have just launched your first Web Server in the Clouds!

Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is the default port that web browsers use
to access web pages on the Internet

As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration
to open inbound connection through port 80:
Open inbound port 80


Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).

First, let us try to check how we can access it locally in our Ubuntu shell, run:

```
curl http://localhost:80
or
curl http://127.0.0.1:80
```


These 2 commands above actually do pretty much the same – they use ‘curl’ command to request our Apache HTTP Server on port 80
(actually you can even try to not specify any port – it will work anyway). The difference is that: in the first case we try to
access our server via DNS name and in the second one – by IP address (in this case IP address 127.0.0.1 corresponds to DNS name
‘localhost’ and the process of converting a DNS name to IP address is called "resolution"). We will touch DNS in further lectures 
and projects.

As an output you can see some strangely formatted test, do not worry, we just made sure that our Apache web service responds
to ‘curl’ command with some payload.

Now it is time for us to test how our Apache HTTP server can respond to requests from the Internet.
Open a web browser of your choice and try to access following url


```
http://<Public-IP-Address>:80
```

Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use following command:

```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```


The URL in browser shall also work if you do not specify port number since all web browsers use port 80 by default.

If you see following page, then your web server is now correctly installed and accessible through your firewall.

Apache Ubuntu Default Page

In fact, it is the same content that you previously got by ‘curl’ command, but represented in nice HTML formatting by your web browser.
