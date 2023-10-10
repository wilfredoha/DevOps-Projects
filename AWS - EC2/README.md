# Preparing prerequisites
In order to complete this project you will need an AWS account and a virtual server with Ubuntu Server OS.

[AWS](https://aws.amazon.com/) is the biggest Cloud Service Provider and it offers a free tier account that we are going to leverage for our projects.

Right now, all we need to know is that AWS can provide us with a free virtual server called EC2 (Elastic Compute Cloud) for our needs.

Spinning up a new EC2 instance (an instance of a virtual server) is only a matter of a few clicks.

## Provisioning an Ubuntu Server and connecting to your EC2 Instance

**Sign in to your AWS account and access the ec2 option**
![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/1.png)

**Creating a Key Pair** - Under *Network & Security* in the left side panel, select Key Pairs

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/2.png)

**Click Create Key Pair**

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/3.png)

**Enter a Name and click Create Key Pair**

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/4.png)

**IMPORTANT** – Save your private key (.pem file) securely and do not share it with anyone! If you lose it, you will not be able to connect to your server ever again!

**Creating a Security Group** - Under *Network & Security* in the left side panel, select Security Groups

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/5.png)

**Click Create Security Group**

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/6.png)

**Enter a Security group name**

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/7.png)

**Add Inbound rules** - You need to SSH into your instance and open port 80(http)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/8.png)

**Click Create security group**

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/9.png)

**In the left side panel click EC2 Dashboard and then click Launch instance**

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/10.png)

**Select Ubuntu an then look for the Ubuntu Server 20.04** - Make sure to be on the Free tier

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/11.png)

**Instance type and Key pair** - Use t2.micro (Free tier). Look for the Key Pair created before

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/12.png)

**Select security group and Lauch instance** - look for the security group created before. Click Launch instance

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/13.png)

**Make suer your new ec2 instances is Running**

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/14.png)

**Select the ec2 instance an click Connect**

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/15.png)

**Click SSH Client and copy the command** - We use this command to connect to the ec2 instance, as you can see the key pair name is placed in the command and the DNS of the instance.

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/16.png)

# Connecting to EC2 terminal

# Using Windows Terminal

**SSH into the instance** - The command copied before must be run at the locations where the Key Pair is located. Is this case I'm using Git Bash, but you can use another console(Putty, MobaXterm)

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/17.png)

**Successful connection**

![Image](https://github.com/wilfredoha/DevOps-Projects/blob/main/AWS%20-%20EC2/images/18.png)

## Using the terminal on MAC/Linux

- The terminal is already installed by default. You just need to open it up.
- Change directory into the loacation where your PEM file is. Most likely will be in the Downloads folder

```
cd ~/Downloads
```

**IMPORTANT** - Anywhere you see these anchor tags < > , going forward, it means you will need to replace the content in there with values specific to your situation. For example, if we need you to replace the name you have saved the private key on your machine, we will write something like < private-key-name >.

- Change premissions for the private key file (.pem), otherwise you can get an error "Bad permissions"

```
sudo chmod 0400 <private-key-name>.pem
```

- Connect to the instance by running

```
ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>
``` 
 
*Congratulations! You have just created your very first Linux Server in the Cloud and our set up looks like this now: (You are the client)*


Please read information about AWS free tier limits and make sure that you STOP your EC2 instance when you are not using it. 

Note that every time you stop and start your EC2 instance – you will have a new IP address, it is normal behavior, so do not forget to update your SSH credentials when you try to connect to your EC2 server.
