---
title: Hidden settings in Telias Sagemcom F@st 5370e
date: 2021-11-08 20:05:00 +/-TTTT
categories: [Network]
tags: [telia, sagemcom]     # TAG names should always be lowercase
---
I got fiber from Telia installed this year and a router from Sagemcom was included. I am mostly happy with it but there are some functions missing or in some cases hidden. 

Since I run my own DNS resolver with [Pi-hole](https://pi-hole.net/) I want the IP for that local server to be served to my clients instead of the ISP provided nameservers.

I also wanted to setup a guest network trying to get some basic segmentation started

Thankfully both settings for DNS and guest network could be accessed through hidden pages in the web interface. Also found settings for parental control but thats nothing I am interested in.

# Guest network: 
* [https://192.168.1.1/0.1/gui/#/wifi/2.4GHz/guest/basic](https://192.168.1.1/0.1/gui/#/wifi/2.4GHz/guest/basic)

# DNS settings:
* [https://192.168.1.1/0.1/gui/#/mybox/dns/server](https://192.168.1.1/0.1/gui/#/mybox/dns/server)

# Parental control: 
* [http://192.168.1.1/0.1/gui/#/access-control/parental-control/planning](http://192.168.1.1/0.1/gui/#/access-control/parental-control/planning)
* [http://192.168.1.1/0.1/gui/#/access-control/parental-control/filtering](http://192.168.1.1/0.1/gui/#/access-control/parental-control/filtering)


There is also the possibility to access settings with XPATH but I haven't used that. Would like a way to add static routes so might explore this more in the future.

## Some sources for more reading:

* [https://github.com/wuseman/SAGEMCOM-FAST-5370e-TELIA](https://github.com/wuseman/SAGEMCOM-FAST-5370e-TELIA)

* [https://krishnendu.com/telia-se-router-sagemcom-f-st-5370e-mod/](https://krishnendu.com/telia-se-router-sagemcom-f-st-5370e-mod/)
* [https://krishnendu.com/part-2-telia-se-router-sagemcom-f-st-5370e-mod/](https://krishnendu.com/part-2-telia-se-router-sagemcom-f-st-5370e-mod/)
* [https://krishnendu.com/part-3-telia-se-router-sagemcom-f-st-5370e-mod-finale/](https://krishnendu.com/part-3-telia-se-router-sagemcom-f-st-5370e-mod-finale/)


