---
title: "Cloudflare Tunnels on Windows"
date: 2021-06-14T19:53:28+01:00
draft: true
---

The documentation for running cloudflared appears to be somewhat lacking and/or confusing to most people. It's fine for people that are familiar with cloudflared and it's inner workings, but for a newcomer, it's a bit daunting.

Let's step through getting it installed and configured, and then present an RDP session via cloudflared to be accessed remotely.

## Overview

Cloudflare announced argo tunnels a while ago, but has recently pushed them further by allowing free usage of them for small business and personal use. By doing so, they've basically removed the need for port forwarding and even traditional VPN's in most cases. 

Say goodbye to old fashioned VPN's, say hello to zero-trust!

To read more about the concept of "Zero Trust" - Cloudflare have written a pretty neat article about it, I'll let them do the explaining... https://www.cloudflare.com/en-gb/learning/security/glossary/what-is-zero-trust/

## Installation & Setup

First, grab the 64-bit ZIP from here: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation#windows

Extract the ZIP somewhere handy e.g. C:/temp or anywhere you can access.

Run powershell as admin and cd to the directory you extracted the cloudflared zip to (In my case, G:\Downloads).

![image-20210614220507809](/images/posts/image-20210614220507809.png)




First, test the tunnel with the following command. 

~~~
.\cloudflared.exe tunnel
~~~

Browse to the link provided and you should be directed to a cloudflare error page and see some errors show up in powershell. This is good! This means the web request hit your machine but couldn't go anywhere, since there isn't a web server running (Unless you have one running on port 8080?).

![image-20210614211701897](/images/posts/image-20210614211701897-1623702173167.png)

Now that we know the tunnel works, we can set one up properly. To to this, we need to log into cloudflare to provide the cloudflared client with credentials. s

~~~
.\cloudflared.exe tunnel login
~~~

![](/images/posts/image-20210614212014970-1623702201730.png)

At this point, your browser should pop up and ask for a cloudflare login, log in as normal and it will take you to the screen shown below. Select the domain you want to attach it to and click Authorize. 

![image-20210614211508025](/images/posts/image-20210614211508025-1623702203952.png)

![image-20210614211435404](/images/posts/image-20210614211435404-1623702205210.png)

Once you're done, you should see a success message in powershell and you'll now have a cert.pem file saved. Keep this safe!

![image-20210614212106608](/images/posts/image-20210614212106608-1623702207236.png)

