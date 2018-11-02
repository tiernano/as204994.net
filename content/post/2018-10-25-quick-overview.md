---
date: 2018-10-25T00:00:00Z
tags:
- Announcements
- Infromation
title: Quick Overview of the network
slug: quick-overview
---

AS204994 spans 5 countries (Ireland, UK, Germany, Amsterdam and USA), with 5 servers from 4 providers (3 from [Vultr](https://www.vultr.com/?ref=6925432), 1 from [VServer.site](https://vserver.site) and 1 from [Packet.Net](https://www.packet.net)) and some on-prem machines (mix of VMs and Physical routers and machines). this may change over time, so check the [homepage](https://as204994.net)

Upstream providers are listed on the [homepage](https://as204994.net) also, but in house I use [Virgin Media Business broadband](https://www.virginmedia.ie/business/business-broadband-phone/) and from there tunnel back to the upstream servers using [ZeroTier](https://www.zerotier.com). I also connect to [EVIX](https://evix.org/) over ZeroTier and to both [LocIX](https://www.locix.online) and [KleyReX](http://kleyrex.net/) with the VM in VServer.site.

Once the connection is made, I use [Bird](https://bird.network.cz/) BGP to announce and receive routes from each of the providers. This is done as follows:

* I have a /24 block of IPs announced though Vultr, Packet, VServer.Site and also though each of the IXs I'm connected to. Currently I am only using V4 to try and get this sorted first, then will add my /48 V6 space to the mix.
* the /24 is split into a /28 for "internal peering" (ZeroTier network), a /28 for Any cast stuff (websites hosted across multiple sites, etc), a /27 for the house and multiple /28s, one for each of the VMs. 
* Any upstream VM that gets a full (or even partial) route feed announces all those routes (bar some filtered routes) back to Dublin. It also announces the /28 it has back to all other (internal) VMs. Only the /24 is announced to the upstream. 
* If a provider does not give any routes back, we leave it as is. It will still announce its internal /28 back to the rest of the network.
* All servers also run a copy of [NGinx](https://www.nginx.com) which then hosts any of the anycast websites ([tiernanotoole.ie](https://www.tiernanotoole.ie) currently being the main one). That site is "hidden" behind [CloudFlare](https://www.cloudflare.com) for their security and bandwidth/caching features. 
* In house, all routes are managed by a single Ubuntu VM running on Hyper-V. It has a main internet connection with Virgin Media, and then connects internally to the rest of the network. Its set to allow internal routing, and the /27 for the house is connected to an internal VLAN.
* I have a Ubiquiti EdgeRouter POE connected to that VLAN and it gets multiple of those IPs. Workstations, phones, etc, the connect to it and use it as a connection to the internet.

So far, this works about 90% of the time. This is not currently the production network: Netflix, Amazon Prime Video, etc, wont work, so all media devices go direct to the internet over a different VLAN (Luckily Virgin Media Business gave me a /29 to use). There is some work that will need to be done before it can go full production. Ideally, if I could announce my V4 and V6 though Virgin Media, that would make life easier, but we will see if they can sort it out. 

So, Any questions? You can mail me at Tiernan [at] as204994 [dot] net. 