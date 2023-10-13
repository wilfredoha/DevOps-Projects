# CLIENT-SERVER ARCHITECTURE WITH MYSQL

![client-server_mysql](https://github.com/wilfredoha/DevOps-Projects/blob/main/06%20-%20CLIENT-SERVER%20ARCHITECTURE%20WITH%20MYSQL/images/client-server_mysql.png)

## Understanding Client-Server Architecture

As you proceed your journey into the world of IT, you will begin to realise that certain concepts apply to many other areas. One of such concepts is – Client-Server architecture.

Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another.

In their communication, each machine has its own role: the machine sending requests is usually referred as "Client" and the machine responding (serving) is called "Server".

A simple diagram of Web Client-Server architecture is presented below:

![client-server](https://github.com/wilfredoha/DevOps-Projects/blob/main/06%20-%20CLIENT-SERVER%20ARCHITECTURE%20WITH%20MYSQL/images/client_sever.png)

In the example above, a machine that is trying to access a Web site using Web browser is a client and it sends HTTP requests to a Web server (Apache, Nginx, IIS or any other) over the Internet.

If we extend this concept further and add a Database Server to our architecture, we can get this picture:

![client-server-db](https://github.com/wilfredoha/DevOps-Projects/blob/main/06%20-%20CLIENT-SERVER%20ARCHITECTURE%20WITH%20MYSQL/images/client_sever_db.png)

In this case, our Web Server has a role of a "Client" that connects and reads/writes to/from a Database (DB) Server (MySQL, MongoDB, Oracle, SQL Server or any other), and the communication between them happens over a Local Network (it can also be Internet connection, but it is a common practice to place Web Server and DB Server close to each other in local network).

The setup on the diagram above is a typical generic Web Stack architecture that you have already implemented in previous projects (LAMP, LEMP, MEAN, MERN), this architecture can be implemented with many other technologies – various Web and DB servers, from  small Single-page applications SPA to large and complex portals.

Real example of LAMP website
In [02 -LAMP](https://github.com/wilfredoha/DevOps-Projects/tree/main/02%20-%20LAMP) you implemented a LAMP STACK website, let us take an example of commercially deployed LAMP website – www.propitixhomes.com.

This LAMP website server(s) can be located anywhere in the world and you can reach it also from any part of the globe over global network – Internet.

Assuming that you go on your browser, and typed in there www.propitixhomes.com. It means that your browser is considered the "Client".
Essentially, it is sending request to the remote server, and in turn, would be expecting some kind of response from the remote server.

Lets take a very quick example and see Client-Server communicatation in action.

Open up your Ubuntu or Windows terminal and run curl command:

```
curl -Iv www.propitixhomes.com
```

Note: If your Ubuntu does not have ‘curl’, you can install it by running sudo apt install curl

In this example, your terminal will be the client, while www.propitixhomes.com will be the server.

See the response from the remote server in below output. You can also see that the requests from the URL are being served by a computer with an IP address 75.2.115.196 on port 80. More on IP addresses and ports when we get to Networking related projects

![curl](https://github.com/wilfredoha/DevOps-Projects/blob/main/06%20-%20CLIENT-SERVER%20ARCHITECTURE%20WITH%20MYSQL/images/client_sever_curl.png)

Another simple way to get a server’s IP address is to use a simple diagnostic tool like ‘ping’, it will also show round-trip time – time for packets to go to and back from the server, this tool uses ICMP protocol.

![ping](https://github.com/wilfredoha/DevOps-Projects/blob/main/06%20-%20CLIENT-SERVER%20ARCHITECTURE%20WITH%20MYSQL/images/client_sever_ping.png)

Side Self Study
Read about ping and traceroute network diagnostic utilities. Be able to make sense out of the results of using these tools.
Refresh your knowledge of basic SQL commands, be able to perform simple SHOW, CREATE, DROP, SELECT and INSERT SQL queries.

