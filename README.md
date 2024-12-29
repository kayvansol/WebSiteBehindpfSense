# WebSite Behind pfSense

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/bigPicture.png?raw=true)

<hr />

PfSense is a firewall/router computer software distribution based on FreeBSD. The open source pfSense Community Edition (CE) and pfSense Plus is installed on a physical computer or a virtual machine to make a dedicated firewall/router for a network.[3] It can be configured and upgraded through a web-based interface, and requires no knowledge of the underlying FreeBSD system to manage.

One very common use case for using pfSense is to host a single website. So here we are going to show you how simple that is to do. As this is the very first step towards understanding how to host multiple websites behind your pfSense firewall, the reality is that the approach is very different to when you get to the point of hosting multiple websites behind your pfSense firewall.

So the first step towards understanding how to host a single website behind your pfSense firewall is to understand the concept of Port Forwarding. Port Forwarding at the simplest level is to translate Port 1 Inbound on your WAN, to Port 2 on your LAN. To visualise what this means in an extremely basic setup, the diagram above looks at how Port Forwarding works.

<hr />

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

we can test the web server access inside our internal network :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/5apacheFromInternalNet.png?raw=true)

testing it from the outside network (client machine) encounters with unreachable 🚫.

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/6clientPingUnreachable.png?raw=true)

<hr />

The pfsense 🛡️ server interfaces’ addresses shows in the below picture :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/7pfsense.png?raw=true)

We can access to the pfsense dashboard 📊 :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/8pfsenseDashbourd.png?raw=true)

Before apply the port forwarding config in the pfsense, when we send a request to the pfsense wan interface (like IP Valid) , we can’t access to the web server (apache) on the internal network that means the routing is not happen :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/9preNatConfig_FromClient.png?raw=true)

the LAN Interface menu :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/10gw0.png?raw=true)

pfsense LAN properties :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/11gw1.png?raw=true)

We must define a default gateway for the LAN Interface :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/12gw2.png?raw=true)

And go to Interfaces > WAN for some changes for local networks :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/2/1.webp?raw=true)

please uncheck two options for the private networks & loopback addresses test :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/2/2.webp?raw=true)

<hr />

DNAT
It’s time to configure the NAT (Port Forwarding) on pfsense. 🚀

Configure : Firewall > NAT > Port Forward

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/13nat1.png?raw=true)


Navigate to the page, Firewall > NAT > Port Forward, then click ‘Add’.

This will take you through to the page where you can configure your port forwarding rules. Enter the following information,

The options we selected in WAN Interface side :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/14nat2.png?raw=true)

And the options we selected in redirection ip (192.168.56.132) & port (80) that is the exact web server address :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/15nat3.png?raw=true)

The core settings that you need to look at in the above screenshot are :

Interface: WAN
This is telling pfSense that it should listen on the physical port on your firewall that is being used for the WAN traffic. This is the port that is connected to your modem (Internet or outside network) using an Ethernet RJ45 cable.

Protocol: TCP
This is fine to keep as it is. For the vast majority of configuration, TCP is the protocol you want to be using.

Source: Ignore
For the purpose of hosting a website, you can ignore configuring the source as you want as many potential customers around the world to access you website and buy something. On the other hand, if you are wanting to public a website that only certain IP addresses should be able to access, you are probably going to want to configure this so that you can whitelist their IP addresses in here. For now though, we’re going to keep things simple here.

Destination: WAN Address
This is a bit of an odd one when you first look at it, as you would assume that this should be set to LAN Address. Weirdly though, on the whole with a standard pfSense setup, this needs to be set to WAN Address. You tend to set this to LAN Address when you want to access something on localhost 127.0.0.1, which is actually the pfSense device itself. So technically when you think about this a little more, a WAN address from the pfSense software on the pfSense hardware is actually anything that is not on the pfSense hardware.

Destination Port Range: HTTP (80)
If you just want to test getting a single website hosted behind your pfSense firewall initially, then by all means set this to HTTP then you can set up Let’s Encrypt later and update your configuration accordingly. Generally speaking you probably want to keep things simple so that there is only one port used, rather than a range of ports — although you can configure the From Port and To Port as a range of ports if you want to try something more advanced.

Redirect Target IP: 192.168.56.132
Naturally enter the LAN IP address of your web server here.

Redirect Target Port: HTTP (80)
In this specific example, this is set to 80

Done. Save this and you’re good to go. Once you’ve saved this, make sure you click Apply Changes.

Once you have clicked Apply Changes, you will notice a message appear that asks you if you want to view the status of the firewall rules being reloaded (and hence working…) which can be handy to double check that this has loaded correctly :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/2/4.webp?raw=true)

When you click on the Monitor link in the above image, this shows you the reload process in real time so you can make sure that it completes successfully.

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/2/5.webp?raw=true)

Once you have done this, you will notice that your Port Forwarding rules have now been applied :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/16nat4.png?raw=true)

What is important to note here is that this action has actually done something automatically for you in the background. To see what this is, first, click back into that Port Forward rule you just created to edit it. You will notice towards the bottom of the page that this has automatically created a Filter Rule Association for you with the name “Rule NAT redirect to 132” which is the same name you used in your previous Description field.

What you’ll notice is that the Firewall Rule has been automatically populated with the information you entered in the previous screen we were looking at on Firewall > NAT > Port Forward. pfSense has even automatically generated the same name for you to make your life as easy as possible which is a great little feature.

The auto generated Rule for the defined NAT in the background :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/17nat5.png?raw=true)

<hr />

Test Your Website ♻️
Now we’ve gone through the process of setting up all of the pfSense configuration, you should now be able to access your website easily.

we can access to the web server (internal network) from the clinet machine (outside network) :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/18postNatConfig_FromClient.png?raw=true)

The routing 🔀 action is happening now successfully.✔️

<hr />

Senario 2 🎉:

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/1.webp?raw=true)

Nginx (“engine x”) is an HTTP web server, reverse proxy, content cache, load balancer, TCP/UDP proxy server, and mail proxy server.

<hr />

We install a Nginx Server 🔀 on a new server with ip of 192.168.56.154 :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/2.webp?raw=true)

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/3.webp?raw=true)

we had an apache web server already with ip of 192.168.56.132 :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/4.webp?raw=true)

and a new apache web server with ip of 192.168.56.155 :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/5.webp?raw=true)

<hr />

write some configs in the nginx server with the name of apache :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/6.webp?raw=true)

```
server {
        listen 80;
        server_name web1.com www.web1.com;

        location / {
            proxy_pass http://192.168.56.132:80;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

server {
        listen 80;
        server_name web2.com www.web2.com;

        location / {
            proxy_pass http://192.168.56.155:80;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
```

check the nginx config before apply it :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/7.webp?raw=true)

restart the nginx server with new config :

```
sudo systemctl restart nginx
```

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/8.webp?raw=true)

Test the 2 web server from inside of nginx server :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/9.webp?raw=true)

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/10.webp?raw=true)

DNAT
At pfsense 🛡️ side, change the NAT Port Forwarding to redirect to new nginx server :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/11.webp?raw=true)

the related generated rule :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/12.webp?raw=true)

test the pfsense WAN IP that redirects to the nginx web server gateway :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/13.webp?raw=true)

Create a link for the nginx apache config to the sites-enabled to be active :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/14.webp?raw=true)

Please set DNS Server A records for the domains that at this article we set them in the /etc/hosts inside of the clinet machine :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/15.webp?raw=true)

<hr />

Test ♻️ :
and it’s time to test the web sites from the client (Outside Network or the Internet) :

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/16.webp?raw=true)

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/17.webp?raw=true)

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/18.webp?raw=true)

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/19.webp?raw=true)

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/20.webp?raw=true)

![alt text](https://raw.githubusercontent.com/kayvansol/WebSiteBehindpfSense/refs/heads/main/img/3/21.webp?raw=true)

Congratulation 🍹, the DNS Server, pfsense, nginx gateway and 2 apache web servers works well with togethers.✔️
