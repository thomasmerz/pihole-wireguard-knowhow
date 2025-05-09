<sup>This project is suitable for:</sup>
[![Linux](https://img.shields.io/badge/os-Linux-blue)](https://en.wikipedia.org/wiki/Linux)
<sup>+</sup>
[![macOS](https://img.shields.io/badge/os-macOS-blue)](https://en.wikipedia.org/wiki/MacOS)
<sup>and has been</sup>
[![made-with-bash](https://img.shields.io/badge/Made%20with-Bash-1f425f.svg)](https://www.gnu.org/software/bash/)
<sup>+</sup>
[![Vim](https://img.shields.io/badge/--019733?logo=vim)](https://www.vim.org/)
<sup>+❤️</sup>

# How to and why combine Pi-hole and WireGuard
This project documents my Setup for [Pi-hole](https://github.com/pi-hole/) 
* at home
* and in the cloud to be used with [WireGuard](https://www.wireguard.com/)  

for me and the whole family and gives some food for thought.  

You would rightly be disappointed if I wouldn't show you my **hacks and tweaks with an explanation** of why I did this and which goals I wanted to achieve and whether I did a good or a bad job.


Now have a look at my Pi-hole(s):  

<img width="826" alt="image" src="https://github.com/user-attachments/assets/07d3921d-01a9-4cf8-95dd-ad5b42057cd2" />


<p>&nbsp;</p>

---

<p>&nbsp;</p>

# Table of contents
1. [Prerequisites](#prerequisites)
2. [Why Pi-hole?](#why-pi-hole)
3. [Why WireGuard?](#why-wireguard)
4. [Setup and Installation](#setup-installation)
5. [Some words regarding choosing a DNS upstream resolver](#upstream-resolvers)
6. [Blocklists and consolidation / deduplication](#blocklists)
7. [General Infos regarding your Pi-hole and network setup](#general-infos)
8. [Some tools for Pi-hole](#tools)
9. [Tweaks](#tweaks)
10. [Usage at home](#usage-at-home)
11. [Usage in foreign networks ("free" WiFi / Hotspots and mobile data (4G/5G))](#usage-on-the-road)
12. [Maintenance](#maintenance)

<p>&nbsp;</p>

---

<p>&nbsp;</p>

## Prerequisites <a name="prerequisites"></a>
You should have at least some **basic knowledge about Linux and Docker**. Which [linux distro](https://en.wikipedia.org/wiki/List_of_Linux_distributions) you are using is not relevant. Use a linux distro of your choice! I'm using Ubuntu and openSUSE, which are basically totally different regarding package management: Ubuntu is Debian-based, openSUSE is RPM-based.

<p>&nbsp;</p>

## Why Pi-hole? <a name="why-pi-hole"></a>
![image](https://user-images.githubusercontent.com/18568381/160894511-80cbe2ae-3006-4144-84e7-a83db6edb866.png)  
* It started that I wanted no or at least less **advertisments**, because it's annoying and because it disrupts the flow of reading, it uses not a little bandwidth but more than you think (for what you don't want or what is of no interest to you when surfing on a website, for example news pages) and it slows down the loading of much websites due to loading lots of ads and images and videos. If you don't download all this unneccessary stuff, you save on your mobile plan and profit from faster page loading 👍  
* Then I wanted more **Privacy**, which means no or at least less **Tracking**, which means no or at least less **Surveillance**!  
* I'm a dad of three kids and I don't want them consuming too much **Social Media** or sometimes no Social Media at all! With Pi-hole it's totally easy to block Facebook or Instagram for example.  
* Because my family runs all their computers with Windows they really needed more and additional **Security** than only an anti-virus scanner or defender and no **Malware** by adding another level of protection. 🦠
  Did you know that it pays off more for attackers to inject advertising networks than a single or some website by [Malvertising](https://en.wikipedia.org/wiki/Malvertising)? Because they maximize their reach by hacking only one single advertising network instead of many, many websites.  
* And finally when they get older: **Youth protection** becomes a big issue! 🔞  

🥅 The MAIN GOAL for me is to block ads, tracker, malware, ... (everything that **harms PRIVACY or DATA INTEGRITY** or is just ANNOYING us)  
🥅 And the SECONDARY GOAL is to block stuff, that is USED WAY TOO MUCH BY THE KIDS... or what is NOT SUITABLE for this age group.  

💡 **Conclusion: Pi-hole can save you from "too curious" corporates (Facebook, Google, Amazon, Apple, Microsoft - just to name some) and can save young people from adult content.** 💡 

<p>&nbsp;</p>

## Why WireGuard? <a name="why-wireguard"></a>

<img width="775" alt="image" src="https://user-images.githubusercontent.com/18568381/160894411-a0bbb699-adfa-4e4f-afa0-be1eecb3a79c.png">  
WireGuard **tunnels all traffic from my mobile device(s) when leaving my home** to use my Pi-hole that isn't exposed on the internet because I **don't want to run a public DNS resolver** by forwarding ports from WAN interface to my computer where Pi-hole is running.  

💡  **Conclusion: WireGuard can save you from "too curious" ISPs at home, mobile providers or (free) VPN providers - but now you still have to trust your cloudserver provider! This is still not 100 percent safe from state authorities or from intelligence services!** 💡  
This german [Howto](https://www.kuketz-blog.de/howto-internet-zensur-umgehen-und-anonym-bleiben/) about "Avoiding Internet-censorship and stay anonym" gives some interesting details and thoughts.

Finally the **combination of Pi-hole and WireGuard** (also known as [WireHole](https://github.com/IAmStoxe/wirehole) - but I like both projects being separated for reasons of nicer and easier updates which I will explain later) gives you the **ability to access your private Pi-hole on all your devices at any place/network any time** 👍

ℹ️  More Information (in german only) can be found [here](https://www.rhoenwurz.de/digitalautonomy_2021/).

<p>&nbsp;</p>

## Setup and Installation <a name="setup-installation"></a>
My motivation to run everything for this project in **[Docker](https://en.wikipedia.org/wiki/Docker_(software))** containers has been the following:
* I didn't want to install some additional packages on my [openSUSE](https://www.opensuse.org/) desktop at home which already runs 24x7 (because it's a fan- and noiseless and power-saving manufactured desktop computer from [Cirrus7](https://www.cirrus7.com/en/produkte/cirrus7-nimbus/) - end of my kind of personal "advertising" 😉.
* It would have been no matter to "bloat" an [Ubuntu LTS](https://ubuntu.com/blog/what-is-an-ubuntu-lts-release) server that I've intended for this project. But why should I run two different setups?
* Last but not least: I had already some experience with Docker containers due to some other projects already running on my Ubuntu LTS server…

💡 I can really recommend Docker, because it's so super easy! 💡

So I used [https://github.com/pi-hole/docker-pi-hole](https://github.com/pi-hole/docker-pi-hole) and followed the "Quick Start" to install and start Pi-hole on my machines (at home and on my cloudserver).

To get the advantages and the same "surf and shelter experience" when not being at home/in my home WiFi I thought about a VPN to my Pi-hole. Because I already had an Ubuntu LTS server running at [Hetzner](https://www.hetzner.com/) with an 1 Gbit/s connection instead of 50 Mbit/s Upload at home, I installed https://github.com/linuxserver/docker-wireguard and followed the "Usage" to install and start my personal WireGuard VPN server 😄 There's a 20 TByte volume for outgoing traffic free of charge, which really should be enough for many WireGuard users and covering all current existing mobile plans (usually some GByte).

BTW: I use `docker-compose` and not native `docker` because I use it already for other projects ([Nextcloud](https://en.wikipedia.org/wiki/Nextcloud)).

⚠️ I will not explain and repeat how to install a basic setup - please refer to the links above. ⚠️  
⚠️ Don't forget to change DNS resolver in your routers DHCP options - please refer to your router's documentation how to do this! ⚠️

Keep in mind to set **Permit all origins** in Web-GUI ("Settings" > "DNS") for your **Pi-hole at home** so that all clients can use it!  
<img width="480" alt="image" src="https://user-images.githubusercontent.com/18568381/160889180-29240833-029b-4ac4-9cb3-32bb2e96d9d6.png">

Keep in mind to set **Allow only local requests** for your **Pi-hole on your cloudserver** if you won't become a public DNS resolver! 💣  
<img width="484" alt="image" src="https://user-images.githubusercontent.com/18568381/160889335-7da539ec-f4c9-4389-86e5-f965c7ab74ec.png">

<p>&nbsp;</p>

## Some words regarding choosing a DNS upstream resolver <a name="upstream-resolvers"></a>
Remember why we choose running a local DNS resolver:  
* to gain more **privacy**!
* and potentielly or hopefully to **get DNS queries answered faster** than with the provider's DNS resolver ([Vodafone's DNS resolver is very lousy](https://github.com/thomasmerz/dnspingtest_rrd_ka/blob/master/images/dnsping_80.69.96.12_month.png)) or some privacy-disrespecting DNS resolvers like the ones from Google (8.8.8.8 and 8.8.4.4).  

Go to "Settings" > "DNS" in Pi-hole's Web-GUI:
* unmark all without "[DNSSEC](https://en.wikipedia.org/wiki/Domain_Name_System_Security_Extensions)"
* forget Google, Cloudflare - they disrespect your privacy!
* forget Quad9 - they have [severe performance issues](https://github.com/thomasmerz/dnspingtest_rrd_ka/blob/master/images/dnsping_9.9.9.11_month.png) and [latency can be up to 1 sec (if there is a response at all](https://github.com/thomasmerz/dnspingtest_rrd_ka/blob/master/images/dnsping_149.112.112.11_month.png))
* only [DNS.WATCH](https://dns.watch/) remains - but they don't support [ECS](https://en.wikipedia.org/wiki/EDNS_Client_Subnet) which "allows better use of DNS-based load balancing to select a service address near the client" which speeds up many service for you by getting connected to a nearer target to you! And they also have a much more [severe performance issues](https://github.com/thomasmerz/dnspingtest_rrd_ka/blob/master/images/dnsping_84.200.69.80_month.png) than Quad9 ([if there is a response at all](https://github.com/thomasmerz/dnspingtest_rrd_ka/blob/master/images/dnsping_84.200.70.40_month.png))!
* So, for me [nextDNS.io's](https://nextdns.io) DNS resolvers are working best regarding latency ([20ms for dns2.nextdns.io](https://github.com/thomasmerz/dnspingtest_rrd_ka/blob/master/images/dnsping_45.90.30.39_month.png) and [40ms for dns1.nextdns.io](https://github.com/thomasmerz/dnspingtest_rrd_ka/blob/master/images/dnsping_45.90.28.39_month.png))
> 45.90.28.39 and 45.90.30.39 (IPv4)  
> 2a07:a8c0::75:86b2 and 2a07:a8c1::75:86b2 (IPv6)  
* But also [AdGuard](https://adguard.com/en/blog/adguard-dns-new-addresses.html) is doing a great job with their pre-filtering DNS resolvers. Since monitoring "filtering" and "non-filtering" DNS resolvers and adding the "filtering" ones them to my Pi-hole's `setupVars.conf` they beat nextDNS.io from scratch/within a week in a head-to-head race: [20ms for dns1.adguard.com](https://github.com/thomasmerz/dnspingtest_rrd_ka/blob/master/images/dnsping_94.140.14.14_week.png) and [20ms for dns2.adguard.com](https://github.com/thomasmerz/dnspingtest_rrd_ka/blob/master/images/dnsping_94.140.15.15_week.png):  
<img width="491" alt="image" src="https://user-images.githubusercontent.com/18568381/204162021-cdeb55ce-35cc-4a86-a64f-9e06bfb60a60.png">

> 94.140.14.14 and 94.140.15.15 (IPv4)  
> 2a10:50c0::ad1:ff and 2a10:50c0::ad2:ff (IPv6)

If you're wondering why [raw performance](https://www.dnsperf.com/#%21dns-resolvers%2CEurope) on dnsperf.com differ so much from my results and are "much better":  
"All DNS providers are tested every minute from 200+ locations around the world"
* which means under optimal conditions
* which means "datacenters" with high-speed-connectivity and low latency.

My project is measuring very close to an end user via WiFi and via Vodafone ISP (coax/cable). So these results are what you also can expect on your home internet connection regardless which ISP (Telekom, Vodafone, 1-und-1, …) and which technology (DSL, Coax/Cable, Fiber or even mobile (4G/5G)).

💡 You can check this out before installing Pi-hole with my little project [dnspingtest_rrd](https://github.com/thomasmerz/dnspingtest_rrd/).  You may have want to have a look at [real data from my home](https://github.com/thomasmerz/dnspingtest_rrd_ka). For one-time-checks you could also use:
```
docker run --rm --name=dnstrace redsift/dnstrace --color -n 10 -c 10 --server 45.90.28.39 --recurse web.de
```

<img width="550" alt="image" src="https://user-images.githubusercontent.com/18568381/161646518-181d1b13-6a59-4453-930f-d88b8c8a3825.png">
("mean" is the relevant/interesting timing for you!)

<p>&nbsp;</p>

## Blocklists and consolidation / deduplication <a name="blocklists"></a>
There's already at least one single blocklist shipped with Pi-hole: [https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts](https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts). But that didn't fit my needings (see "goals" above). So I constantly and repeatedly check and add/remove blocklists on an irregular base.  

Currently I'm using these blocklist (Web-GUI: **"Group management"** > "Adlist" - you may want to assign a group to some blocklists to be able to block some domains only for some devices) in column "address" (this is an extract from real output from `pihole_adlist_tool` which I will introduce later). These list sum up to more than 4 mio domains/hosts. For better reading I rearranged the rows by groups:

```
id  enabled  total_domains  domains_covered  hits_covered  unique_domains_covered  address
--  -------  -------------  ---------------  ------------  ----------------------  --------------------------------------------------------------------------------------------------------
# default
1   1        189924         1253             125255        105                     https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts

# additional blocklists:
11  0        11395          262              46693                                 https://someonewhocares.org/hosts/zero/hosts
13  1        2196                                                                  https://raw.githubusercontent.com/StevenBlack/hosts/master/extensions/fakenews/hosts
19  1        23782          102              19612         1                       https://raw.githubusercontent.com/blocklistproject/Lists/master/crypto.txt
20  1        26562          1                1             1                       https://raw.githubusercontent.com/blocklistproject/Lists/master/drugs.txt
21  1        196085         6                64            4                       https://raw.githubusercontent.com/blocklistproject/Lists/master/fraud.txt
23  1        190229         5                70            5                       https://raw.githubusercontent.com/blocklistproject/Lists/master/phishing.txt
27  1        2499           3                23            1                       https://raw.githubusercontent.com/blocklistproject/Lists/master/gambling.txt
28  1        2134           6                70            6                       https://raw.githubusercontent.com/blocklistproject/Lists/master/piracy.txt
33  1        4185           3                90            3                       https://raw.githubusercontent.com/RPiList/specials/master/Blocklisten/Streaming
34  1        476667         11               3502          4                       https://raw.githubusercontent.com/RPiList/specials/master/Blocklisten/Phishing-Angriffe
35  1        283757                                                                https://zerodot1.gitlab.io/CoinBlockerLists/hosts
37  1        441936                                                                https://raw.githubusercontent.com/RPiList/specials/master/Blocklisten/Corona-Blocklist
43  1        91462          70               1209          5                       https://raw.githubusercontent.com/RPiList/specials/master/Blocklisten/malware
59  1        347            13               868                                   https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt
60  1        347            13               868                                   https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy_v6.txt
62  1        1679                                                                  https://raw.githubusercontent.com/lightswitch05/hosts/master/docs/lists/hate-and-junk-extended.txt
63  1        431080         1516             142449        451                     https://raw.githubusercontent.com/lightswitch05/hosts/master/docs/lists/ads-and-tracking-extended.txt
64  1        171731         86               15259         53                      https://raw.githubusercontent.com/lightswitch05/hosts/master/docs/lists/tracking-aggressive-extended.txt
67  1        539365         41               14947         13                      https://raw.githubusercontent.com/RPiList/specials/master/Blocklisten/spam.mails
74  1        2571           2                13                                    https://raw.githubusercontent.com/Sinfonietta/hostfiles/master/gambling-hosts
82  0        201416                                                                https://raw.githubusercontent.com/notracking/hosts-blocklists/master/hostnames.txt
84  1        916            47               4657          20                      https://raw.githubusercontent.com/liamengland1/mischosts/master/microsoft-telemetry
86  0        2325                                                                  https://hosts.tweedge.net/malicious.txt
87  1        3757           263              14157         6                       https://pgl.yoyo.org/as/serverlist.php?showintro=0;hostformat=hosts
89  1        291106                                                                https://big.oisd.nl/

# adult and NSFW:
42  1        414048         790              57435         211                     https://raw.githubusercontent.com/RPiList/specials/master/Blocklisten/child-protection
71  1        23                                                                    https://raw.githubusercontent.com/Sinfonietta/hostfiles/master/snuff-hosts
72  1        19670          36               429           8                       https://raw.githubusercontent.com/Sinfonietta/hostfiles/master/pornography-hosts
73  1        369                                                                   https://raw.githubusercontent.com/tiuxo/hosts/master/porn
77  1        30786          52               473           13                      https://raw.githubusercontent.com/StevenBlack/hosts/master/extensions/porn/clefspeare13/hosts
80  1        56711          23               240           6                       https://mypdns.org/my-privacy-dns/matrix/-/raw/master/source/porn_filters/explicit_content/hosts.list
81  1        5629           3                8             1                       https://mypdns.org/my-privacy-dns/matrix/-/raw/master/source/porn_filters/explicit_content/domains.list
88  1        341009                                                                https://nsfw.oisd.nl

# tracking on mobiles:
30  1        767            173              66383         12                      https://raw.githubusercontent.com/d43m0nhLInt3r/socialblocklists/master/MobileAppAds/appadsblocklist.txt
68  1        80             32               40481                                 https://raw.githubusercontent.com/Perflyst/PiHoleBlocklist/master/android-tracking.txt
83  1        234            66               223348        55                      https://raw.githubusercontent.com/liamengland1/mischosts/master/apple-telemetry

# smart TVs:
5   0        209                                                                   https://raw.githubusercontent.com/d43m0nhLInt3r/socialblocklists/master/SmartTV/smarttvblocklist.txt
32  1        20             5                264           4                       https://www.technoy.de/lists/FireTVAds.txt

# tracking in minecraft:
69  1        6                                                                     https://raw.githubusercontent.com/StevenBlack/hosts/master/data/minecraft-hosts/hosts

# tracking on tiktok:
85  1        184            6                13029         2                       https://raw.githubusercontent.com/liamengland1/mischosts/master/tiktok-hosts
```

Some infos about `big.oisd.nl`:

> Contrary to it's name, FULL does NOT include NSFW
The [NSFW](https://en.wikipedia.org/wiki/Not_safe_for_work) list can be used as stand-alone, or alongside basic or full
>
> What is the difference between the full and basic list? --> https://oisd.nl/faq#basic
>
> The Basic list is a smaller, less comprehensive variant of the full list, which focussus mainly on Ads, (Mobile) App Ads
>
> The Full list blocks: Ads, (Mobile) App Ads, Phishing, Malvertising, Malware, Spyware, Ransomware, CryptoJacking, Scam ... Telemetry/Analytics/Tracking (Where not needed for proper functionality)

<p>&nbsp;</p>

## General Infos regarding your Pi-hole and network setup <a name="general-infos"></a>
I'm using only **static IP adresses** in my home-network because this is essential for the **"Client group management"** (Web-GUI: "Group management" > "Clients"). It's essential to **turn off "private wifi address"** on iOS and the pendant on android. Otherwise you won't be able to identify your clients and you won't be able to use static IP addresses! ([IP addresses](https://en.wikipedia.org/wiki/IP_address) are easier to manage and to "remember" than [MAC addresses](https://en.wikipedia.org/wiki/MAC_address). As Apple writes on their [support site](https://support.apple.com/en-us/HT211227) "private Wi-Fi addresses (**can**) … improve privacy …". For "network administrator" Apple continues to write "use an MDM-defined network profile to turn off Private Address for enrolled devices that join their Wi-Fi network".

After you have once identified your "known clients" by IP address or MAC addres you are now able to assign groups of blocklists to your clients. Assume you have an Adlist group "social" and don't want your kids to consume social media, then you might assign group "social" additional to "default" to your kids device(s).

⚠️ Please refer to your router's documentation how to setup "static DHCP" addresses! ⚠️

If you are experiencing **false positives for some domains**,
- you can **open an issue at the blocklist owner's repository** on GitHub/GitLab/…
- and you can (temporarilly) **whitelist** it in "Group Management" > "Domains". There you can also **blocklist** some domains or even top-level-domains with an regular expression.  
I'm blocklisting `(\.cn$|\.ru$|\.su$|\.vn$|\.top$)` because currently I don't know a reason why to surf to these top-level-domains (despite I can't read mandarin, russian or vietnamese 😉).  

I'm also blocking:
* `(^|\.)xn--.*$` which blocks all **punycode domains** because they're [often/mostly used for phishing](https://www.startpage.com/sp/search?q=malicious+punycode+lookalike+domains)
* `graph.facebook.com` for **tracking by Facebook on non-facebook sites** (none of my many blocklist does block this! but `graph.instagram.com` is already blocked by some blocklists. Check this out by `docker exec -it pihole pihole -q graph.instagram.com`). Some more details can be found [here](https://forum.kuketz-blog.de/viewtopic.php?t=552) in the german "Kuketz-Forum".
* `(\.casino$|\.bet$|\.poker$)` because I don't like **online-gambling**
* `(^|.+\.|.+)app-measurement\.com(.+|$)` is one of my absolute "top blocked domains". This domain is already in many blocklists, but due to some developer's error there are also many invalid queries for 'https://app-measurement.com/sdk-exp' which I want to block with this reg-ex. This domain is **widely used by many, many smartphone apps because they use a shitty SDK** and would be absolutely tracking-free by the developers of the app if they wouldn't use this SDK 😞

<p>&nbsp;</p>

## Some tools for Pi-hole <a name="tools"></a>
### [PADD](https://github.com/pi-hole/PADD)
> PADD provides in-depth information about your Pi-hole.

I can also be used as a systemd-service on tty11 in addition to `pihole-live-output.service` on tty10 that can easily be implemented:

**pihole-padd.service**  
My cloudserver:  
<img width="768" alt="image" src="https://user-images.githubusercontent.com/18568381/160881402-ddbc7588-1ed2-4a60-8043-81ca37c9221e.png">

My desktop computer at home:  
<img width="887" alt="image" src="https://user-images.githubusercontent.com/18568381/161450856-76e41be0-4130-4a7f-99b9-541185de3144.png">

**pihole-live-output.service**  
<img width="961" alt="image" src="https://user-images.githubusercontent.com/18568381/160881744-7235c698-b50a-462c-876b-62ace72e0505.png">

📝 Just add these lines in your `/etc/systemd/system/pihole-padd.service` and do a `systemctl daemon-reload` and a `systemctl start pihole-padd.service`:
```
# PADD - A more advanced version of the chronometer provided with Pihole.

[Unit]
Description=PADD - A more advanced version of the chronometer provided with Pihole.
#Requires=pihole.service
After=docker.service network-online.target dhcpd.service pihole.service

[Service]
Type=simple
Environment=TERM=linux
User=root
TTYPath=/dev/tty11
ExecStart=/usr/bin/sh -c "/usr/bin/docker exec --tty pihole /etc/pihole/padd.sh > /dev/tty11 < /dev/tty11"
ExecStop=/usr/bin/sh -c "/usr/bin/clear > /dev/tty11 < /dev/tty11"
Restart=always
RestartSec=1
TTYReset=yes
TTYVHangup=yes
TTYVTDisallocate=yes
KillSignal=SIGHUP

[Install]
WantedBy=multi-user.target
```

📝  Do the same procedure for `/etc/systemd/system/pihole-live-output.service`:
```
# Pi-hole: View the live output of the Pi-hole log

[Unit]
Description=View the live output of the Pi-hole log.
#Requires=pihole.service
After=docker.service network-online.target dhcpd.service pihole.service

[Service]
Type=simple
Environment=TERM=linux
User=root
TTYPath=/dev/tty10
ExecStart=/usr/bin/sh -c "/usr/bin/docker exec -it pihole pihole -t > /dev/tty10 < /dev/tty10"
ExecStop=/usr/bin/sh -c "/usr/bin/clear > /dev/tty10 < /dev/tty10"
Restart=always
RestartSec=1
TTYReset=yes
TTYVHangup=yes
TTYVTDisallocate=yes
KillSignal=SIGHUP

[Install]
WantedBy=multi-user.target
```

Now you can switch to your tty10 and tty11 console to view Pi-hole's query log or to displays stats about your piHole 👍

🚧 [Here](https://github.com/pi-hole/PADD/pull/208/files) I documented how to run `padd.sh` with your docker container. 🚧

### [pihole_adlist_tool](https://github.com/yubiuser/pihole_adlist_tool)
> A tool to analyse how your pihole adlists cover you browsing behavior.

⚠️ This has to be run from outside your Docker container in the directory where your Pi-hole database files reside! ⚠️

### [dnspingtest_rrd](https://github.com/thomasmerz/dnspingtest_rrd)
> Monitoring of average response times of DNS resolvers in RRD databases and simple HTML pages with PNG graphs.

Use this if you want to find out **which DNS resolver will be the fastest and most reliable for you**. I run it closest to a user experience via WiFi because most devices today are connected via WiFi and not via LAN. Think of smartphones and tablets! After some time (a day, a week; what you expect as "reliable") there's some data on a 5 minute base available and by interpreting the generated charts you should have more than just a clue which DNS resolvers from the ones you monitored are the fastest and most reliable for your internet connection at home 👍

⚠️ Please have a look at the [README](https://github.com/thomasmerz/dnspingtest_rrd/blob/main/README.md) to check and how to set up this nice tool for your needings. ⚠️

You might be also interested in my DNS-Ping Monitoring with **real data** from [my home](https://thomasmerz.github.io/dnspingtest_rrd_ka/) or my cloudservers at Hetzner: [Nuremberg, Germany](https://thomasmerz.github.io/dnspingtest_rrd_nbg/), [Falkenstein, Germany](https://thomasmerz.github.io/dnspingtest_rrd_fsn/) and [Helsinki, Finnland](https://thomasmerz.github.io/dnspingtest_rrd_hel/).

<p>&nbsp;</p>

## Tweaks <a name="tweaks"></a>
### Reduce top lists from useless information
If you find your "top" lists on the dashboard of the Web-GUI not suitable for the information you really need, you might exclude some **top domains / top adverters** and/or **top clients** in "Settings" > "API / Web Interface" (you need to turn 'basic' to 'expert'). For example I exclude these domains because they are much to generic or are queried very, very often by my linux machine's cronjobs and therefore useless to know:

```
*.aaplimg.com
*.amazonaws.com
*.akadns.net
*.akamai.net
*.akamaiedge.net
*.apple-dns.net
*.apple.com
*.atomile.com
*.azure.com
*.cdn77.org
*.cloudapp.net
*.cloudfront.net
*.dm-drogeriemarkt.com
*.docker.com
*.docker.io
*.dropbox.com
*.dropboxapi.com
*.dropboxstatic.com
*.events.data.microsoft.com
*.fing.io
*.githubusercontent.com
*.gvt2.com
*.gvt3.com
*.icloud.com
*.in-addr.arpa
*.ip6.arpa
*.localdomain
*.mooo.com
*.ms-acdc.office.com
*.msedge.net
*.nanoleaf.me
*.office365.com
*.pipe.aria.microsoft.com
*.push.apple.com
*.root-servers.net
*.safebrowsing.apple
*.sharepoint.com
*.speedtest.net
*.spo-msedge.net
*.syncthing.net
*.teams-msgapi.trafficmanager.net
api.mobile.immobilienscout24.de
apple.com
apple-finance.query.yahoo.com
de
detectportal.firefox.com
dmdrogeriemarkt.jamfcloud.com
dmtech-opsgenie-eu.mobileapp.eu.opsgenie.com
freedns.afraid.org
geo-applefinance-cache.internal.query.g03.yahoodns.net
ghcr.io
github.com
gitlab.com
hc-ping.com
imap.strato.de
internetcheck.fing.com
ipv4only.arpa
mugshot0.assets-yammer.com
org
push.services.mozilla.com
receiver.yamalytics.yammer.com
safebrowsing.googleapis.com
www.comdirect.de
www.yammer.com
com
io
net
arpa
```

And `pi.hole` itself does a lot of DNS queries as a client with types [DS and DNSKEY](https://en.wikipedia.org/wiki/List_of_DNS_record_types) which are technically absolutely neccessary for [DNSSEC](https://en.wikipedia.org/wiki/Domain_Name_System_Security_Extensions); but this information (how much queries) is absolutely worthless for me 😜

### Keep database "small" / disable DoH and Apple's iCloud Private Relay / reduce queries
⚠️ With V6 of Pi-hole "the configuration file can be found at /etc/pihole/pihole.toml, all of the options are documented within this file."

I've added these lines in my `etc-pihole/pihole-FTL.conf`. Read the comments/links to understand:
```
# https://docs.pi-hole.net/ftldns/configfile/
MAXLOGAGE=24.0
DBIMPORT=yes
#MAXDBDAYS=365
MAXDBDAYS=100
DBINTERVAL=1.0
DBFILE=/etc/pihole/pihole-FTL.db

# ---
# https://docs.pi-hole.net/ftldns/configfile/#mozilla_canary
# ---
# Should Pi-hole always replies with NXDOMAIN to A and AAAA queries
# of use-application-dns.net to disable Firefox automatic DNS-over-HTTP?
# This is following the recommendation on
# https://support.mozilla.org/en-US/kb/configuring-networks-disable-dns-over-https
MOZILLA_CANARY=true

# ---
# https://docs.pi-hole.net/ftldns/configfile/#icloud_private_relay
# ---
# Should Pi-hole always replies with NXDOMAIN to A and AAAA queries of
# mask.icloud.com and mask-h2.icloud.com to disable Apple's iCloud Private Relay
# to prevent Apple devices from bypassing Pi-hole?
# This is following the recommendation on
# https://developer.apple.com/support/prepare-your-network-for-icloud-private-relay
BLOCK_ICLOUD_PR=true

# https://discourse.pi-hole.net/t/how-to-stop-all-of-the-ptr-queries/22085/4
# due to https://github.com/thomasmerz/issue-tracker/issues/522
ANALYZE_ONLY_A_AND_AAAA=true

# https://docs.pi-hole.net/ftldns/configfile/#rate_limit
# Control FTL's query rate-limiting. Rate-limited queries are answered with a REFUSED reply and not further processed by FTL.
# …
# Rate-limiting may be disabled altogether by setting RATE_LIMIT=0/0
# this results in the same behavior as before FTL v5.7.
#RATE_LIMIT=1000/60
#RATE_LIMIT=0/0

# https://docs.pi-hole.net/ftldns/configfile/#block_ttl
# FTL's internal TTL to be handed out for blocked queries.
# This settings allows users to select a value different from the dnsmasq config option local-ttl.
# This seems useful in context of locally used hostnames that are known to stay constant over long times
# Note that large values may render whitelisting ineffective due to client-side caching of blocked queries.
#BLOCK_TTL=2
BLOCK_TTL=300

# https://docs.pi-hole.net/ftldns/configfile/#reply_addr4
# This option is deprecated and may be removed in future versions, please use BLOCK_IPV4 and LOCAL_IPV4 instead.
# LOCAL_IPV4= (unset by default, PR #1293)
# BLOCK_IPV4= (unset by default, PR #1293)
REPLY_ADDR4=0.0.0.0
```

You may also raise `min-cache-ttl` in your `etc-dnsmasq.d/05-pihole-custom-ttl.conf` so that all your devices don't need to query your Pi-hole quite often (every minute), but only every hour:
```
# https://00f.net/2019/11/03/stop-using-low-dns-ttls/
# https://discourse.pi-hole.net/t/increase-ttl/25157
# https://discourse.pi-hole.net/t/change-the-ttl/6903/14
min-cache-ttl=3600
```

### Reduce also blocked queries
In conjunction with `BLOCK_TTL=300` (blocked domains will be cached on client side for 5 minutes instead of 2 seconds!) this **drasically reduces the amount of queries and of the blocked-domain-ratio**!  
In my case this reduced daily queries from around usually 80.000/100.000 to 40.000/50.000 and block-ratio dropped from 30-40% down to 15-20%  

I'm running a daily cronjob to gather these stats with `padd.sh`:
```
59 23 * * * echo -e $(date +\%Y-\%m-\%d) $(docker exec --tty pihole /etc/pihole/padd.sh -j) >> ~/logs/pihole-padd.log
```

### Conclusion
With these tweaks I'm usually getting a cache hit ratio of 50% (see [screenshot on top of this page](https://user-images.githubusercontent.com/18568381/160889683-5d2849e5-7ef5-4bef-9e87-e3a16affdb44.png): "Upstream servers", the blue cake slice is "cached")) and more with a very low latency for most DNS queries with an [average of slightly below 20ms](https://github.com/thomasmerz/dnspingtest_rrd_ka/blob/master/images/dnsping_192.168.0.13_week.png) (on a weekly basis and if there are no internet outages).

### Force network-wide usage of SafeSearch (Google, Startpage.com):
"This method leverages SafeSearch VIP to force all users on your network to use SafeSearch on Google Search while still allowing a secure connection via HTTPS. The VIP in SafeSearch VIP refers to a Virtual IP, which is an IP address that can be routed internally to multiple Google servers. We will serve SafeSearch results for all requests that we receive on this VIP, which includes Google search, image search, and video search.
It works for all browsers on your device and only users who are administrators on the device can undo this change."
[Map google domains to forcesafesearch.google.com](https://support.google.com/websearch/answer/186669?hl=en#zippy=%2Cunter-linux)  
Just add these hostnames/domains to your "Local DNS" / "DNS Records":
```
google.com      216.239.38.120
google.de       216.239.38.120
www.google.com  216.239.38.120
www.google.de   216.239.38.120
```
Other search engines use "moderate search" by default, but "safe search" can be turned off at any time). I didn't find any offer like this for Bing, DuckDuckGo and others.

⚠️ Caution: This forces SafeSearch for **all** devices on your network! ⚠️

<p>&nbsp;</p>

## Usage at home <a name="usage-at-home"></a>
Just change DNS resolver in DHCP options of your router so that **all** devices in your home network use your Pi-hole. For example:  
<img width="585" alt="image" src="https://user-images.githubusercontent.com/18568381/160890390-8dc7eaa2-3fea-482c-9176-909d3db163f1.png">

💣 If you're a **Vodafone Customer** and if you're only using the provided Vodafone Station you will **not** be able to do this! VF Station has no option to change DNS resolvers, so go and buy a router that you own and that you can configure as you want and build up a [router cascade](https://www.heise.de/ct/artikel/Router-Kaskaden-1825801.html?view=print). 💣

<p>&nbsp;</p>

## Usage in foreign networks ("free" WiFi / Hotspots and mobile data (4G/5G)) <a name="usage-on-the-road"></a>
Go and download **WireGuard** app for your device:  

* [Windows Installer](https://download.wireguard.com/windows-client/wireguard-installer.exe)
* [Android play store](https://play.google.com/store/apps/details?id=com.wireguard.android)
* [F-Droid](https://f-droid.org/en/packages/com.wireguard.android/)
* [iOS](https://itunes.apple.com/us/app/wireguard/id1441195209?ls=1&amp;mt=8)
* For MacOS or Linux installation see [download](https://www.wireguard.com/install/) link

After you've set up your WireGuard server with one or the desired amount of peers you can **show your WireGuard tunnel config as QR-code**:
```
docker exec -it wireguard /app/show-peer <peer-name>
```

On your **mobile device** you can simply **scan this QR-code** and you're finished. Edit your config to enable "on-demand tunneling" for "mobile data" and all WiFi except your WiFi at home which is already protected by your Pi-hole at home.

On your **computer** you have to copy and paste the following output from your WireGuard server into your WireGuard app:
```
docker exec -it wireguard cat /config/peer_<peer-name>/peer_<peer_name>.conf
```

<p>&nbsp;</p>

## Maintenance <a name="maintenance"></a>
I use some **scripts for regular and automated updates**:
### pi-hole-update.sh
```
#!/bin/bash

cd ~/dev/docker-pi-hole && {
  git stash; git pull; git stash pop -q
  # https://github.com/docker/compose/issues/5960#issuecomment-390952779
  docker-compose pull --no-parallel 2>&1 | grep "is up to date" || \
    { docker-compose stop && docker-compose up -d; }
}

```

### docker-wireguard-update.sh
```
#!/bin/bash

docker pull ghcr.io/linuxserver/wireguard | grep "is up to date" || \
{
docker stop wireguard
docker rm wireguard

# https://github.com/linuxserver/docker-wireguard

# peer names are only supported alphanumeric:
# https://github.com/linuxserver/docker-wireguard/issues/135

#  -e ALLOWEDIPS=192.168.1.0/24,192.168.2.0/24 `#optional` \
docker run -d \
  --name=wireguard \
  --cap-add=NET_ADMIN \
  --cap-add=SYS_MODULE \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Europe/Berlin \
  -e SERVERURL=wireguard.example.com\
  -e SERVERPORT=12345 \
  -e PEERS=1 `#optional` \
  -e PEERDNS=auto `#optional` \
  -e INTERNAL_SUBNET=10.13.13.0 `#optional` \
  -p 12345:12345/udp \
  -v ~/temp/etc-wireguard:/config \
  -v /lib/modules:/lib/modules \
  --sysctl="net.ipv4.conf.all.src_valid_mark=1" \
  --restart unless-stopped \
  ghcr.io/linuxserver/wireguard

}
```

With these **cronjobs** for daily WireGuard updates and weekly Pi-hole updates:
```
10  5 * * * ~/bin/docker-wireguard-update.sh &> ~/logs/docker-wireguard-update.sh.log
15  7 * * 1 ~/bin/pi-hole-update.sh &> ~/logs/pi-hole-update.sh.log
```
Have fun and stay safe! 💚

