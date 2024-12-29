# WebSite Behind pfSense

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/bigPicture.png?raw=true)

PfSense is a firewall/router computer software distribution based on FreeBSD. The open source pfSense Community Edition (CE) and pfSense Plus is installed on a physical computer or a virtual machine to make a dedicated firewall/router for a network.[3] It can be configured and upgraded through a web-based interface, and requires no knowledge of the underlying FreeBSD system to manage.

One very common use case for using pfSense is to host a single website. So here we are going to show you how simple that is to do. As this is the very first step towards understanding how to host multiple websites behind your pfSense firewall, the reality is that the approach is very different to when you get to the point of hosting multiple websites behind your pfSense firewall.

So the first step towards understanding how to host a single website behind your pfSense firewall is to understand the concept of Port Forwarding. Port Forwarding at the simplest level is to translate Port 1 Inbound on your WAN, to Port 2 on your LAN. To visualise what this means in an extremely basic setup, the diagram above looks at how Port Forwarding works.

Senario 1 🎉 :
We have 3 machine (a client in the outside network like the internet that send some http request to web server through pfsense firewall, a pfsense with 2 NICs, for firewall and port forwarding and a web server in the internal network).

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/1Servers.png?raw=true)

The client machine (named Lab) is in the outside network (like Internet) with ip (192.168.5.6) 🌍:

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/2clientNICs.png?raw=true)

The web server machine (named DesktopTest) is in the internal network (LAN) with ip (192.168.56.132) 🏡 :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3webServerNICs.png?raw=true)

We want to write some changes 🔄 in apache default page on the web server :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/4apacheOn132.png?raw=true)

check the apache service on the web server to be active and running :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/4apacheStatusOn132.png?raw=true)


