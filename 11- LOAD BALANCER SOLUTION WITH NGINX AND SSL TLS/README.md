# LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS

By now you have learned what Load Balancing is used for and have configured an LB solution using Apache, but a DevOps engineer must be a versatile professional and know different alternative solutions for the same problem. That is why, in this project we will configure an Nginx Load Balancer solution.

It is also extremely important to ensure that connections to your Web solutions are secure and information is encrypted in transit – we will also cover connection over secured HTTP (HTTPS protocol), its purpose and what is required to implement it.

When data is moving between a client (browser) and a Web Server over the Internet – it passes through multiple network devices and, if the data is not encrypted, it can be relatively easy intercepted by someone who has access to the intermediate equipment. This kind of information security threat is called Man-In-The-Middle (MITM) attack.

This threat is real – users that share sensitive information (bank details, social media access credentials, etc.) via non-secured channels, risk their data to be compromised and used by cybercriminals.

SSL and its newer version, TSL – is a security technology that protects connection from MITM attacks by creating an encrypted session between browser and Web server. Here we will refer this family of cryptographic protocols as SSL/TLS – even though SSL was replaced by TLS, the term is still being widely used.

SSL/TLS uses digital certificates to identify and validate a Website. A browser reads the certificate issued by a Certificate Authority (CA) to make sure that the website is registered in the CA so it can be trusted to establish a secured connection.

In this project you will register your website with LetsEnrcypt Certificate Authority, to automate certificate issuance you will use a shell client recommended by LetsEncrypt – cetrbot.

## Task
This project consists of two parts:

1. Register a new domain name and configure secured connection using SSL/TLS certificates
2. Configure Nginx as a Load Balancer

Your target architecture will look like this:

![architecture](https://github.com/wilfredoha/DevOps-Projects/blob/main/11-%20LOAD%20BALANCER%20SOLUTION%20WITH%20NGINX%20AND%20SSL%20TLS/images/architecture.png)

# REGISTER A NEW DOMAIN NAME
Let us make necessary configurations to make connections to our Tooling Web Solution secured!

In order to get a valid SSL certificate – you need to register a new domain name, you can do it using any Domain name registrar – a company that manages reservation of domain names. The most popular ones are: Godaddy.com, Domain.com, Bluehost.com.

1. Register a new domain name with any registrar of your choice in any domain zone (e.g. .com, .net, .org, .edu, .info, .xyz or any other). In my case I already have a domain with [Godaddy](https://www.godaddy.com/en-uk).

![my_domain](https://github.com/wilfredoha/DevOps-Projects/blob/main/11-%20LOAD%20BALANCER%20SOLUTION%20WITH%20NGINX%20AND%20SSL%20TLS/images/my_domain.png)

2. Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP

You might have noticed, that every time you restart or stop/start your EC2 instance – you get a new public IP address. When you want to associate your domain name – it is better to have a static IP address that does not change after reboot. Elastic IP is the solution for this problem, learn how to allocate an Elastic IP and associate it with an EC2 server [on this page](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html).

3. Update A record in your registrar to point to Nginx LB using Elastic IP address

Go to Route 53 in your AWS account and create a **Hosted Zone**.

![hosted_zone_1](https://github.com/wilfredoha/DevOps-Projects/blob/main/11-%20LOAD%20BALANCER%20SOLUTION%20WITH%20NGINX%20AND%20SSL%20TLS/images/hosted_zone_1.png)

Add your Domian name and create the hosted zone

![hosted_zone_2](https://github.com/wilfredoha/DevOps-Projects/blob/main/11-%20LOAD%20BALANCER%20SOLUTION%20WITH%20NGINX%20AND%20SSL%20TLS/images/hosted_zone_2.png)

Now you need to add de DNS generated in the hosted one to the Domain name in your Domain Name registrar.

![hosted_zone_3](https://github.com/wilfredoha/DevOps-Projects/blob/main/11-%20LOAD%20BALANCER%20SOLUTION%20WITH%20NGINX%20AND%20SSL%20TLS/images/hosted_zone_3.png)

Go to your Godaddy accont

![hosted_zone_4](https://github.com/wilfredoha/DevOps-Projects/blob/main/11-%20LOAD%20BALANCER%20SOLUTION%20WITH%20NGINX%20AND%20SSL%20TLS/images/hosted_zone_4.png)

Add the nameservers as shown in the following image.

![hosted_zone_5](https://github.com/wilfredoha/DevOps-Projects/blob/main/11-%20LOAD%20BALANCER%20SOLUTION%20WITH%20NGINX%20AND%20SSL%20TLS/images/hosted_zone_5.png)

Now we need to create two records for our Domain in Route 53. At this point you must have a EC2 VM based on Ubuntu Server 20.04 LTS runnning and with an Elastic IP associated.

Copy the public IP Address of the Loab Balancer.

![hosted_zone_6](https://github.com/wilfredoha/DevOps-Projects/blob/main/11-%20LOAD%20BALANCER%20SOLUTION%20WITH%20NGINX%20AND%20SSL%20TLS/images/hosted_zone_6.png)

![hosted_zone_7](https://github.com/wilfredoha/DevOps-Projects/blob/main/11-%20LOAD%20BALANCER%20SOLUTION%20WITH%20NGINX%20AND%20SSL%20TLS/images/hosted_zone_7.png)

Side Self Study: Read about different DNS record types and learn what they are used for.

## CONFIGURE NGINX AS A LOAD BALANCER

The security group of the Load Balancer must have open TCP port 443 – this port is used for secured HTTPS connections

Update the instance and Install Nginx

```
sudo apt update && sudo apt install nginx -y

sudo systemctl start nginx
sudo systemctl enable nginx
```

Create a configuration for the reverse proxy setting.

```
sudo nano /etc/nginx/sites-available/load_balancer.conf
```

Add the following cofiguration

```
upstream web {
	server <public IP WebServer01>;
	server <public IP WebServer02>;
}

server {
	listen 80;

	server_name whavsttt.xyz www.whavsttt.xyz

	location / {
		
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_pass http://web;
	}
}
```

Remove the default site

```
sudo rm -f /etc/nginx/sites-enabled/default
```

Check the Nginx configuration

```
sudo nginx -t
```

![nginx_ok](https://github.com/wilfredoha/DevOps-Projects/blob/main/11-%20LOAD%20BALANCER%20SOLUTION%20WITH%20NGINX%20AND%20SSL%20TLS/images/nginx_ok.png)


***
***
***

Configure Nginx LB using Web Servers’ names defined in /etc/hosts

Open the default nginx configuration file

sudo vi /etc/nginx/nginx.conf

```
#insert following configuration into http section

upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
    server Web3 weight=5;
}

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
}

#comment out this line
#       include /etc/nginx/sites-enabled/*;
```


Restart Nginx and make sure the service is up and running


```
sudo systemctl restart nginx
sudo systemctl status nginx
```

Side Self Study: Read more about HTTP load balancing methods and features supported by Nginx
https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/






***
***
***

# CONFIGURE SECURED CONNECTION USING SSL/TLS CERTIFICATES




4. Configure Nginx to recognize your new domain name
Update your nginx.conf with server_name www.your-domain-name.com instead of server_name www.domain.com

5. Install certbot and request for an SSL/TLS certificate
Make sure snapd service is active and running

```
sudo systemctl status snapd
```

Install certbot

```
sudo snap install --classic certbot
```

Request your certificate (just follow the certbot instructions – you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from nginx.conf file so make sure you have updated it on step 4).

```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```

Test secured access to your Web Solution by trying to reach https://<your-domain-name.com>

You shall be able to access your website by using HTTPS protocol (that uses TCP port 443) and see a padlock pictogram in your browser’s
search string.

Click on the padlock icon and you can see the details of the certificate issued for your website.

![6031](https://user-images.githubusercontent.com/85270361/210153397-250901f3-b2e3-4f9c-9ca4-ea190acbd034.PNG)


6. Set up periodical renewal of your SSL/TLS certificate
By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.

You can test renewal command in dry-run mode

```
sudo certbot renew --dry-run
```

Best pracice is to have a scheduled job that to run renew command periodically. Let us configure a cronjob to run the command twice
a day.

To do so, lets edit the crontab file with the following command:

```
crontab -e
```

Add following line:

```
* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1
```

You can always change the interval of this cronjob if twice a day is too often by adjusting schedule expression.

Side Self Study: Refresh your cron configuration knowledge by watching this video.
( https://youtu.be/4g1i0ylvx3A )


You can also use this handy online cron expression editor. ( https://crontab.guru/ )

