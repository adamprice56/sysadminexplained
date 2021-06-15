---
title: Cloudflare Tunnels on Windows
date: 2021-06-14T19:53:28.000+01:00

---
The documentation for running cloudflared appears to be somewhat lacking and/or confusing to most people. It's fine for people that are familiar with cloudflared and it's inner workings, but for a newcomer, it's a bit daunting.

Let's step through getting it installed and configured, and then present an RDP session via cloudflared to be accessed remotely.

## Overview

Cloudflare announced argo tunnels a while ago, but has recently pushed them further by allowing free usage of them for small business and personal use. By doing so, they've basically removed the need for port forwarding and even traditional VPN's in most cases.

Say goodbye to old fashioned VPN's, say hello to zero-trust!

To read more about the concept of "Zero Trust" - Cloudflare have written a pretty neat article about it, I'll let them do the explaining... https://www.cloudflare.com/en-gb/learning/security/glossary/what-is-zero-trust/

## Installation & Authentication

First, grab the 64-bit ZIP from here: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation#windows

Extract the ZIP somewhere handy e.g. C:/temp or anywhere you can access.

Run powershell as admin and cd to the directory you extracted the cloudflared zip to (In my case, G:\\Downloads).

![image-20210614220507809](/images/posts/image-20210614220507809.png)

First, test the tunnel with the following command.

    .\cloudflared.exe tunnel

Browse to the link provided and you should be directed to a cloudflare error page and see some errors show up in powershell. This is good! This means the web request hit your machine but couldn't go anywhere, since there isn't a web server running (Unless you have one running on port 8080?).

![image-20210614211701897](/images/posts/image-20210614211701897-1623702173167.png)

Now that we know the tunnel works, we can set one up properly. To to this, we need to log into cloudflare to provide the cloudflared client with credentials. s

    .\cloudflared.exe tunnel login

![](/images/posts/image-20210614212014970-1623702201730.png)

At this point, your browser should pop up and ask for a cloudflare login, log in as normal and it will take you to the screen shown below. Select the domain you want to attach it to and click Authorize.

![image-20210614211508025](/images/posts/image-20210614211508025-1623702203952.png)

![image-20210614211435404](/images/posts/image-20210614211435404-1623702205210.png)

Once you're done, you should see a success message in powershell and you'll now have a cert.pem file saved. Keep this safe!

![image-20210614212106608](/images/posts/image-20210614212106608-1623702207236.png)

# Tunnel Creation

Next, we need to create a tunnel and give it a name.

    .\cloudflared.exe tunnel create mytunnel

![](/uploads/screenshot-2021-06-15-213013.png)

Make a note of the tunnel ID and the location the json file is stored for reference later. You'll need it for your config file! 

_You may also see a message that cloudflared needs updating, this is fine to ignore, we'll address this later._

You can check the status of your tunnel(s) by running a list command

    .\cloudflared.exe tunnel list

![](/uploads/screenshot-2021-06-15-213206.png)

As you can see here, mytunnel has been created and has no connections currently. You can also see the status of any other tunnels e.g. ls02 shown here is currently connected to Cloudflares LHR (London Heathrow) and Dublin datacenters twice. Cloudflare tunnels automatically set up redundant connections to provide automatic load balancing and failover between Cloudflare endpoints, handy!

# Tunnel Configuration

Cloudflared created a hidden folder in your C:/users/youruser folder which stores the configuration files for the tunnel once created. To start routing things to the tunnel, we need to create a config.yaml file with some rules to tell cloudflare what services are available on the tunnel. You can add web servers/services, RDP and SSH sessions currently. 

_You can also go a step further and present an entire subnet to cloudflared which can be used in conjunction with the warp client (a.k.a 1.1.1.1) on another device to VPN into your network via the tunnel, but that is for another guide!_

To access this folder from powershell, you can use the following command. (Of course, replace it with your username as shown in the output after creating your tunnel above)

    explorer.exe C:\Users\Adam\.cloudflared\

You need to create a file called config.yaml in here. To do this, you will need to enable file name extensions. Then you can right click and use new > Text Document and name it config.yaml, then click yes to the prompt shown. 

![](/uploads/screenshot-2021-06-15-235212.png)

To edit the yaml file, notepad is fine, but I'd suggest using VS Code or Notepad++ to ensure the formatting is correct and to help with syntax.