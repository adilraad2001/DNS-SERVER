# DNS-SERVER
How To configure Dns server
A Step-by-Step Guide To Set Up A Bind9  

DNS Server 

![](Aspose.Words.56436bc8-5a01-44c9-b660-2e2fc212925f.001.jpeg)

Team Members: 

EL RHAZOUANI Zineb
ELOBBI Anass
ERROKHSSY Abdelatti
ERRAAD Adil  
KARTIT Hicham
YOUSSI Kenza
SAID EL OUARDI 

Major: **Cyber Security** 

**Index:** 

Introduction 

What is DNS Server? 

- DNS definition 
- How it works? 

What is Bind DNS Server? Setup Bind DNS Server 

- Install Bind DNS 
- Configure the named.conf.options file 
- Setup zone files  
- Verify the configuration 

Setup DNS Client 

The primary and secondary DNS servers Configuration 

- Configure the primary DNS server as a master 
- Configure the secondary DNS server as a slave  
- Test the configuration by stopping the master DNS  

Setup BIND9 with DHCP Server dynamic host registration 

- Generate secure key for updates  
- Setup the DHCP Server  
 
- Setup the BIND9 with Dynamic DNS 
  - The **/etc/bind/named.conf.local** file 
  - The zones files 
- Verify the configuration  

Conclusion  

![](Aspose.Words.56436bc8-5a01-44c9-b660-2e2fc212925f.002.png)

**Introduction:** 

Have you ever wondered how the Internet really works? Many people do, from simple web surfing for sharing pictures on social media. In fact, the Internet heavily relies on something called a DNS: a database of network names and IP addresses. These three little letters hold huge weight. Without DNS, the Internet as we know it would simply not exist, and we would be left to deal with ones and zeroes. That is why experts usually refer to DNS as the Phonebook of the Internet. 

In the beginning, in 1969, all the information a computer on the network needed to identify other computers, the "hosts" on the network, was contained in a simple text file called hosts.txt. The explosion of the Internet required the establishment of global governance and conventions. This is the appearance of the Domain Name System ("DNS") architecture in 1983. By Paul Mockapetris, after the advent and deployment of TCP / IP protocols, to allow the exchange of information associated with IP addresses, using a notification system accessible from all machines on the network. 

**What is DNS Server?** 

- **DNS definition:** 

The Domain Name System (DNS) is a hierarchical, distributed database that stores information for mapping Internet host names to IP addresses and vice versa, mail routing information, and other data used by Internet applications.  

DNS helps users contact web sites or resources in Internet with simple domain names, instead of long numeric IP addresses, since operating systems cannot understand domain names, DNS translates them into IP addresses which are logical addresses of devices. 

- **How it works?** 

Most of the time, when you resolve a name on the Internet, you are working on an endpoint (your phone, laptop, or on another device). On that device you usually find a minimal resolver called a stub resolver (often, just “stub”). It’s called a stub because it can’t do very much. It knows it needs an answer to a question about a domain name.  

So, it asks something else: it sends its resolution requests to another service 

on the network, called a recursive resolver. For most of the world, the default 

designated recursive resolver (often just “recursive”) is a server provided by 

the local internet service provider (ISP). The job of the recursive is to  ![](Aspose.Words.56436bc8-5a01-44c9-b660-2e2fc212925f.002.png)


make requests to the larger DNS ecosystem on behalf of the user, by asking a series of DNS servers that are authoritative for components of the DNS in order to process the request and hand back to the user the IP of the DNS name that was requested. 

The recursive will check its cache left to right (do I have www. example.com? example.com? com?). However, if it doesn’t have any of the answers in cache, the recursive goes to the root to get things started. The idea of the root is to provide an origin to the query, providing the nameservers for all the top-level domains (TLD) such as com, net, fr, edu, and others. The root then delegates the authority for the namespace of that name to the authoritative DNS of an organization designated to run that TLD independently. This process is intuitively called a delegation. It looks broadly like this: 

![](Aspose.Words.56436bc8-5a01-44c9-b660-2e2fc212925f.004.png)

**What is Bind DNS Server?** 

BIND which stands for “***Berkely Internet Name Domain***” is a free and Opensource software which is widely used in Linux servers for translating Domain names to IP address. BIND performs both of the main DNS server roles – acting as an authoritative name server for one or more specific domains, and acting as a recursive resolver for the DNS system generally. The current version of BIND is BIND 9. 

**Setup Bind DNS Server:** 

- **Install Bind9:**

Download the necessary packages: 

```bat

sudo apt install -y bind9 bind9utils 

```

- **Configure the name.conf.options file:** 

The DNS main configuration directory is **/etc/bind**. It contains the zone- lookup files and other configuration files, such as **named.conf.options** where the global DNS configuration. But before we should create a backup for this file: 

```bat

sudo cp named.conf.options named.conf.options.backup

```

Then we need to edit this file (with the IP: **192.168.1.4**): 

```bat

options { 

directory "/var/cache/bind"; recursion yes; 

listen-on {192.168.1.4;}; }; 

```

- **Set up zone files:** 

In order to create the forward and reverse zones we should first backup the **named.conf.local** file, then edit it. 

```bat

sudo cp named.conf.local named.conf.local.backup

```

Below is a forward zone entry for our **example.com** domain: 

```bat

//forward lookup zone 

zone "example.com" IN{ 

type master; // Primary DNS 

file "/etc/bind/db.example.com"; // Forward lookup file }; 

```

Where: 

- **example.com** is the zone name. 
- **db.example.com** is the name of the forward lookup zone. 

Below is a reverse zone entry for our **example.com** domain: 

```bat

//reverse lookup zone

zone "1.168.192.in-addr.arpa" IN{ 

type master; // Primary DNS 

file "/etc/bind/db.1.168.192"; //Reverse lookup file }; 

```

Where: 

- **1.168.192.in-addr.arpa** is the zone name of reverse DNS.  
- **db.1.168.192** is the reverse DNS file. 

After creating zones, the next task is to create the records for each zone. 

For the forward zone, we should copy the sample forward zone lookup file from **db.local** file to a file called **db.example.com**(this file will be created with **cp** command if it doesn’t find it). 

```bat

sudo cp db.local db.example.com

```

Then edit **db.example.com** file: 

```bat

  	@    IN  SOA  ns1.example.com. root.example.com. (       2  ; Serial 

												604800  ; Refresh 

												86400   ; Retry 

											   2419200  ; Expire 

											   604800)  ; Negative Cache TTL 

														; 

    @	 IN  NS  ns1.example.com.
	ns1	 IN  A  192.168.1.4  
	www	 IN  A  192.168.1.4 

```

For the reverse zone, as before we should copy the sample reverse zone lookup file from **db.127** file to a file called **db. 192**. 

```bat

	sudo cp db.127 db.192

```

Then edit **db.192** file: ![](Aspose.Words.56436bc8-5a01-44c9-b660-2e2fc212925f.014.png)

```bat

	@ IN  SOA  ns1.example.com root.example.com. (       1  ; Serial 

													604800  ; Refresh 

													86400   ; Retry 

												   2419200  ; Expire 

												   604800)  ; Negative Cache TTL 

	; 	

	@ IN  NS  ns1.example.com.
    4  IN  PTR  ns1.example.com. 
	4  IN  PTR  www.example.com.

```

- **Verify the configuration:** 

The **named-checkzone** command is used to check if the syntax is okay or if there is any error. The command should return to shell if there is no error. For the **example.com** zone: 

```bat

named-checkzone example.com db.example.com!

```

The output: 

```bat

zone example.com/IN: loaded serial 2 OK png)

```

And for **1.168.192.in-addr.arpa** reverse zone: 

```bat

named-checkzone 1.168.192.in-addr.arpa db.192 

```

The output:

```bat

zone 1.168.192.in-addr.arpa/IN: loaded serial 1 OK 

```

Now, after checking the syntax, we should restart the Bind DNS Server and check its status: 

```bat

systemctl restart bind9 systemctl status bind9

```

An active status is shown without any configuration error, indicates that the DNS Server has successfully picked up the configuration and is ready to map IP Addresses with domain names. 

![](Aspose.Words.56436bc8-5a01-44c9-b660-2e2fc212925f.020.png)

**Setup DNS Client:** 

For configuring the DNS Client, we need to Add the following lines to **/etc/resolv.conf** file: 

```bat

search example.com nameserver 192.168.1.4 

```

BIND 9 is now configured. It's time to check whether it is working or not. Here, we will use the **nslookup** command line: 

```bat

nslookup www.example.com

```

The response is: 

```bat

Server:   192.168.1.4 Address: 
			 	192.168.1.4#53

Name:  www.example.com Address: 192.168.1.4

```

```bat

nslookup 192.168.1.4 

```

The response is: 

```bat

4.1.168.192.in-addr.arpa name = www.example.com.
4.1.168.192.in-addr.arpa	name = ns1.example.com.

```

**The primary and secondary DNS servers Configuration:** 

In this part we will see how to configure a Master-Slave DNS Sever using Bind9. 

The Master DNS Server is also known as the primary DNS server. It reads data related to the domain zone. While the Slave DNS Server gets a copy of the zone data from the Master DNS using the zone transfer method. This method keeps the zone data in a cache for a particular time and uses it to serve DNS queries immediately after being set up. 

For the Master-Slave setup, we need to configure the master DNS server and enable zone transfer to the secondary Name Server. 

We will use two VMs: 

- 1st Ubuntu VM whose IP:192.168.1.4 
- 2nd Ubuntu VM whose IP:192.168.1.14 

![](Aspose.Words.56436bc8-5a01-44c9-b660-2e2fc212925f.026.png)

- **Configuring the primary DNS Server as a Master** 

For configuring the primary DNS Server (**ns1.example.com**) as Master we will edit the **named.conf.local**  file by adding the **allow-transfer** and **also- notify** parameters for forward and reverse zone. 

```bat

//forward lookup master zone 

zone "example.com" IN{ 

type master;

file "/etc/bind/db.example.com"; allow-transfer {192.168.1.14}; also-notify {192.168.1.14;}; 

}; 

//reverse lookup master zone 

zone "1.168.192.in-addr.arpa" IN{ 

type master; 

file "/etc/bind/db.1.168.192"; allow-transfer {192.168.1.14;};  also-notify {192.168.1.14;}; 

}; 

```


The allow-transfer parameter allows transfer of zone files from the master to the slave DNS while the also-notify helps notify the slave whenever there is an update on the zone files from the master. 

Then, to apply changes, we need to restart the Bind DNS Server. 

- **Configuring the secondary DNS server as a Slave** 

For configuring the secondary DNS Server (**ns2.example.com**) as slave we will edit the **named.conf.local**  file by adding both the forward and reverse zone parameters as shown below: 


```bat

//forward lookup slave zone 

zone "example.com" IN{ 

type slave; 

file "/var/lib/bind/db.example.com";  masters { 192.168.1.4; };

}; 

//reverse lookup slave zone 

zone "1.168.192.in-addr.arpa" IN{ 

type slave; 

file "/var/lib/bind/db.192"; 

masters { 192.168.1.4; }; }; 

```

Notice that the zones files must be in **/var/cache/bind/** because, by default, **AppArmor** only allows write access inside it (this was made specifically for a slave configuration. See AppArmor's configuration in **/etc/apparmor.d/usr.sbin.named**).  

**AppArmor** ("Application Armor") is a Linux kernel security module that allows the system administrator to restrict programs' capabilities with per- program profiles. Profiles can allow capabilities like network access, raw socket access, and the permission to read, write, or execute files on matching paths. 

If we read the **/etc/apparmor.d/usr.sbin.named** we will find that: 

```bat

# /var/cache/bind is for slave/stub data, since we're not the origin of it.

```

Another thing is that we should change the ownership to bind user for writing the zones updates by the following command: 

```bat

sudo chown bind:bind db.192

sudo chown bind:bind db.example.com

```

Now, we should restart the bind9 service and check its status: 

![](Aspose.Words.56436bc8-5a01-44c9-b660-2e2fc212925f.031.png)

So, the status is active without any error. 

- **Test the configuration by stopping the Master DNS**  

Before starting we need to add secondary DNS  to the **resolv.conf** file by the following command: 

```bat

sudo sh -c "echo nameserver 192.168.1.14 >> /etc/resolv.conf"

```

For testing the Secondary DNS, we could shut down BIND9 on the Primary then try pinging **example.com** from a host configured to use the Secondary as well as the Primary for name resolution. If all goes well, the Secondary should resolve **example.com**. 

![](Aspose.Words.56436bc8-5a01-44c9-b660-2e2fc212925f.033.png)

The **nslookup www.example 192.168.1.14** gives as output: 

```bat

Server:   192.168.1.14 Address: 
			 192.168.1.14#53

Name:  www.example.com Address: 192.168.1.4

```

And if we try **nslookup 192.168.1.4**, the output gives: 

```bat

4.1.168.192.in-addr.arpa name = ns1.example.com.
4.1.168.192.in-addr.arpa name = www.example.com. 
4.1.168.192.in-addr.arpa name = mail.example.com.

```

Now, if we want to add another A record ftp, we need to increment the serial number by 1 because a zone is only transferred if the **Serial Number** on the Primary is larger than the one on the Secondary.  

**nslookup 192.168.1.4** gives: 

```bat

4.1.168.192.in-addr.arpa name = mail.example.com. 
4.1.168.192.in-addr.arpa name = ftp.example.com. 
4.1.168.192.in-addr.arpa name = ns1.example.com. 
4.1.168.192.in-addr.arpa name = www.example.com.

```

**Setup BIND9 with DHCP Server dynamic host registration:** 

Many web properties, such as[ APIs ](https://www.cloudflare.com/learning/security/api/what-is-an-api/)or websites, run on internet connections that have their[ IP addresses ](https://www.cloudflare.com/learning/dns/glossary/what-is-my-ip-address/)changed frequently; this creates a problem if the operators of those properties want to give a hosted resource a specific domain name, which must then store an IP address in[ Domain Name System ](https://www.cloudflare.com/learning/dns/what-is-dns/)

[(DNS) ](https://www.cloudflare.com/learning/dns/what-is-dns/)records. 

` `Dynamic DNS (DDNS) is a service that keeps the DNS updated with a web property's correct IP address, even if that IP address is constantly being updated, by adding, replacing, or deleting records in a primary server and sending it a special form of DNS messages. 

- **Generate secure key for updates:** 

For updates to happen securely, we need to generate a random key for both **BIND** and **ISC-DHCP-Server** to use so no one else can update the DNS zones, using the **RNDC** (Remote Name Daemon Control) which is a name server control utility in bind: 

```bat

sudo sh -c "rndc-confgen -a | tee /etc/bind/rndc.key"

```

where: 

- **rndc-confgen**: is a command that generates configuration files for 

the **rndc** command. 

- **-a**: performs automatic **rndc** configuration. This creates a 

file **rndc.key** in **/etc/bind** (or whatever sysconfdir was specified as when **BIND** was built) that is read by both the **rndc** command and 

the named daemon on startup. The **rndc.key** file defines a default command channel and authentication key allowing the **rndc** command to communicate with the named daemon on the local host with no further configuration. 

Now we need to change the file permissions so only root and bind can read it, and create a symlink or a soft link for DHCP (to include to it from dhcp folder): 


```bat

sudo chmod 660 /etc/bind/rndc.key 

sudo chown root:bind /etc/bind/rndc.keys 

cd /etc/dhcp

ln -s /etc/bind/rndc.key 

```
- **Setup DHCP:** 

For the DHCP configuration that we will be used, we have: 

```bat

- Network address: 192.168.1.0/24 
- DHCP/DNS IP address: 192.168.1.4 
- DHCP range: 192.168.1.5-192.168.1.254 
- Default Gateway IP: 192.168.1.4 


```

now, we need just to add the following lines to our configuration: 

```bat

ddns-domainname "example.com."; 
ddns-rev-domainname "in-addr.arpa.";
option domain-name "example.com."; 
option domain-name-servers 192.168.1.4;
ddns-update-style  interim;
ignore client-updates;
update-static-leases on;
ddns-updates on;

include "/etc/dhcp/rndc.key";
 update-optimization off;
 update-conflict-detection off;
 use-host-decl-names on; 

zone example.com. { 

		primary 192.168.1.4; 

key rndc-key; 

} 

zone 1.168.192.in-addr.arpa. {  
       primary 192.168.1.4; 

key rndc-key; 

} 

```

Where: 

- **ddns-update-style interim**: is used to specify whether the DHCP server will make DNS updates of the client's name along with the IP assignment.  
- **ignore client-updates**: tells the DHCP server don’t honor the client's intention to do its own update of its A record. 
- **update-static-leases on**: causes the DHCP server to do DNS updates for clients even if those clients are being assigned their IP address using a fixed-address statement - that is, the client is being given a static assignment.  
- **ddns-updates on**: controls whether or not the server will attempt to do a DNS update when a lease is confirmed. 
- **update-optimization off**: if this parameter is false for a given client, the server will attempt a DNS update for that client each time the client renews its lease, rather than only attempting an update when it appears to be necessary. This will allow the DNS to heal from database inconsistencies more easily, but the cost is that the DHCP server must do many more DNS updates. 
- **update-conflict-detection off**: the server will skip this check and instead simply tear down any previous bindings to install the new binding without question. The default is true.  
- **use-host-decl-names on**:  for every host declaration within that scope, the name provided for the host declaration will be supplied to the client as its hostname. 

Note that, we have created a symlink **/etc/bind/rndc.key** in **/etc/dhcp**, and we have changed the ownership of this file to both bind and root, so if we want to include to this file, we need to add the following lines to the   **/etc/apparmor.d/usr.sbin.dhcpd** to have read and write permissions: 

```bat

/etc/bind/**rw,
/etc/bind/**rw,

```

A bad DHCP configuration will prevent the service from starting. So, we can check it anytime by running **dhcpd -t**. As long as we don’t see any warnings, then the syntax check has passed.  

```bat

sudo dhcpd -t 

```

- **Setup BIND9 with Dynamic DNS:** 

The BIND configuration files are split up into many different, so We’ll look at each file, one at a time: 

- **The /etc/bind/named.conf.local file:** 

Notice that the zones files must be in **/var/lib/bind** because, by default, **AppArmor** only allows write access inside it (this was made specifically for dynamically updated zone (and journal) files). 

If we look at the **/var/lib/bind** folder we will find what we call the **Journal File,** where all changes made to a zone using dynamic update are stored in. This file is automatically created for each zone by the server when the first dynamic update takes place.  

The name of the journal file is formed by appending the extension **.jnl** to the name of the corresponding zone file unless specifically overridden. The journal file is in a binary format and should not be edited manually. 

Then, the **named.conf.local** file will be configured as shown below: 

```bat

include "/etc/bind/rndc.key"; 

//forward lookup zone

zone "example.com" IN{ 

type master;

file "/var/lib/bind/db.example.com"; allow-update { 

key rndc-key; 

};  

}; 

//reverse lookup zone

zone "1.168.192.in-addr.arpa" IN{ 

type master;

file "/var/lib/bind/db.192"; allow-update { 

key rndc-key; 

}; 

}; 

```

- **The zones files:** 

To give the bind the write permission we need to change the ownership for both **/var/lib/bind/db.192** file and **/var/lib/bind/db.example.com** file: 

```bat

sudo chown root:bind /var/lib/bind/db.example.com 
sudo chown root:bind /var/lib/bind/db.192 

```

The DNS database files are now being rewritten by the bind service. 

Always you should **stop the bind service before making any changes to the database files**, otherwise they might be overwritten by bind. 

- **Verify the configuration:**  

To verify the configuration, we need to restart the two services bind9 and DHCP, then add a Windows 7 client VM whose IP:192.168.1.13(which is provided by our DHCP). 

In the client machine we will run **ipconfig /registerdns** command to register the A record of this host in our zones reverse and forward: 

**/var/lib/bind/db.192:** 

```bat

$TTL 3600  ; 1 hour

13  PTR  Windows7-PC.example.com. **/var/lib/bind/db.example.com:** 

$TTL 3600  ; 1 hour 

Windows7-PC   A  192.168.1.13

TXT  "3121614b07d02924910da01fdc285ba851"

```

For the test, we will use the **host** utility, which is a simple utility for performing DNS lookups. It is normally used to convert names to IP addresses and vice versa. 

```bat

host 192.168.1.13 192.168.1.4

```

The output is: 

```bat

Using domain server: 
	Name: 192.168.1.4 Address: 192.168.1.4#53
	Aliases: 13.1.168.192.in-addr.arpa 
	domain name pointer Windows7-PC.example.com.

```

And if we write the command below: 

```bat

host Windows7-PC.example.com www.example.com

```

the output is: 

```bat

Using domain server: Name: www.example.com 
Address: 192.168.1.4#53 
Aliases: Windows7-PC.example.com has address 192.168.1.13

```

**Conclusion:** 

Understanding fundamental Domain Name System (DNS) concepts is a critical part of the understanding of how the internet works. DNS is the mechanism that helps find the network endpoint that you’re trying to reach. This paper will cover basic DNS terminology, in general terms, with nothing vendor- specific or proprietary. 

Trying to imagine a world of IT without Domain Name Servers (DNS) can be very intimidating and prohibitive to say the least. With all of the websites and servers accessed via the web around the world, who can dare imagine having a database of all of those IP Addresses in case you would wish to access a given resource? With such a question posed, the power of DNS immediately comes to the fore 
![](Aspose.Words.56436bc8-5a01-44c9-b660-2e2fc212925f.002.png)
