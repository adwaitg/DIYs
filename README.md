# DIY Home automation/security with secure remote access

There are many home automation/security products available in the market and in opensource today. They all have
a phone app, that lets you remotely control your devices in your home that are behind your residential gateway/WiFi
router. To achieve this connectivity, the phone app (or the web-browser version) in reality connects to a public
server hosted by the company and the local IoT home automation devices connect to this server. Any config updates/
state changes that are done by the user are pushed down (or pulled from - depending on the implementation) via this
server to the local devices. Notifications also work in a similar fashion.

There is a huge security risk you are taking on in all of this - if any of these systems (cloud server/IoT home
automation devices) get compromised, you open up your local network to intruders. Today, there are ton of such cheap
automation devices available (wifi/bluetooth/zwave bulbs, door sensors, wifi cameras etc) that have a very low bar for
security.

I didn't want to use any of these commercial off-the-shelf products for home automation, so I setup Home Assistant
(https://www.home-assistant.io/) on Raspberry Pi. There are numerous tutorials and config files availabe on github
with amazing customizations. However, for remote access to home assistant, I found very few solutions. All of those
solutions need you to do port forwarding on your home router + Duck DNS like in this tutorial:
https://www.juanmtech.com/hassio-duckdns-add-on-set-up/

Port forwarding is not safe either. So what is an available alternative? If you rent out a cheap virtual machine in
the cloud (I used DigitalOcean's $5 droplet) you could setup a VPN between your cloud VM and raspberryPi. But there
are a few challenges here - setting up a VPN is first of all daunting for sure, secondly - has a lot of overhead that
can adversely affect a video upload/download and thirdly - VPN is a tunnel between two points only. What if I need
to throw in another raspberry Pi in my home network for video surveillance? A peer-to-peer design would be ideal!
Tinc (https://www.tinc-vpn.org/) comes to the rescue here to build out a mesh network between all your participating
nodes.

Tinc is secure and set up is really simple. Once setup, all your nodes (cloud VM and your raspberryPis in your local
network) are in the same network namespace! From then on, you can run a simple Nginx in reverse proxy mode, secure
it with letsencrypt certs and create a custom domain using Duck DNS with auto cert updates using certbot + cron job.
The home assistant on the local RaspberryPis can be setup with plain HTTP, since you have SSL termination at your
cloud VM's nginx and Tinc encrypted tunnel down to your local RaspberryPi!

You now have a fully remote accessible home automation/security system with full control over security via your cloud
VM without any need for port forwarding!
