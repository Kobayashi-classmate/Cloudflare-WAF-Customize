# source
Original address: [Luminous' Home - 【CloudFlare】通用防火墙安全配置分享](https://luotianyi.vc/6140.html)

## Whitelist release
The highest priority goal is to allow known normal traffic to pass through the firewall, because our subsequent configuration, such as targeting the computer room AS, will cause verification to be applied to search engine crawlers such as GoogleBot. In addition, the whitelist IP can include your own debugging IP, known good-faith RSS crawlers, etc.

```
(ip.src in {1.1.1.1 2.2.2.2 3.3.3.3 4.4.4.4}) or (cf.client.bot)
```

## IDC ASN verification
The second rule uses CF's judgment of ASN. The ASN mentioned here is the code of the autonomous network, such as AS4134 for China Telecom. The following list is basically the code names of data center service providers, because leasing them to external parties is also a common source of network attacks, rather than the home broadband ISPs commonly used by real overseas visitors.

```
(ip.geoip.asnum in {174 195 209 577 792 793 794 1215 1216 1217 2497 2914 3223 3255 3269 3326 3329 3457 3462 3598 4184 4190 4637 4694 4755 4785 4788 4816 4826 4835 5056 5610 5617 6471 6584 6830 6876 6877 6939 7029 7224 7303 7489 7552 7684 8068 8069 8070 8071 8074 8075 8100 8220 8560 8881 8987 9009 9299 9312 9370 9534 9678 9952 9984 10026 10453 11351 11426 11691 12076 12271 12334 12367 12874 12876 12989 14061 14117 14140 14576 14618 15169 16276 16509 16591 16629 17043 17428 17707 17788 17789 17790 17791 18013 18228 18403 18450 18599 18734 18978 19527 19740 20207 20473 20552 20554 20860 21704 21769 21859 21887 22773 22884 23468 23724 23885 23959 23969 24088 24192 24424 24429 24940 25429 25697 25820 25935 25961 26160 26496 26818 27715 28429 28431 28438 28725 29066 29286 29287 29802 30083 30823 31122 31235 31400 31898 32097 32098 32505 32613 34081 34248 34549 34947 35070 35212 35320 35540 35593 35804 35816 35908 35916 36351 36352 36384 36385 36444 36492 36806 37963 37969 38001 38197 38283 38365 38538 38587 38588 38627 39284 40065 40676 40788 41009 41096 41264 41378 42652 42905 43289 43624 43989 45011 45012 45062 45076 45085 45090 45102 45102 45102 45103 45104 45139 45458 45566 45576 45629 45753 45899 45932 46484 46844 47232 47285 47927 48024 48024 48337 48905 49327 49588 49981 50297 50340 50837 51852 52000 52228 52341 53089 54463 54538 54574 54600 54854 54994 55158 55330 55720 55799 55924 55933 55960 55967 55990 55992 56005 56011 56109 56222 57613 58073 58199 58461 58466 58519 58543 58563 58593 58772 58773 58774 58775 58776 58844 58854 58862 58879 59019 59028 59048 59050 59051 59052 59053 59054 59055 59067 59077 59374 60068 60592 60631 60798 61154 61317 61348 61577 61853 62044 62240 62468 62785 62904 63018 63023 63075 63288 63314 63545 63612 63620 63631 63655 63677 63678 63679 63727 63728 63729 63835 63838 63888 63916 63949 64050 131090 131106 131138 131139 131140 131141 131293 131428 131444 131477 131486 131495 132196 132203 132509 132510 132513 132591 132839 133024 133199 133380 133478 133492 133746 133752 133774 133775 133776 133905 133929 134238 134327 134760 134761 134763 134764 134769 134770 134771 134835 134963 135061 135290 135300 135330 135377 135629 137693 137697 137699 137753 137784 137785 137787 137788 137876 137969 138366 138407 138607 138915 138949 138950 138952 138982 138994 139007 139018 139124 139144 139201 139203 139220 139316 139327 139726 139887 140096 140596 140701 140716 140717 140720 140723 140979 141157 141180 142570 149167 177453 177549 197099 197540 198047 198651 199490 199506 199524 199883 200756 201094 201978 202053 202675 203087 204601 204720 206092 206204 206791 206798 207319 207400 207590 208425 208556 211914 212708 213251 213375 262187 263022 263196 263639 263693 264344 264509 265443 265537 266706 267784 269939 270110 328608 394699 395003 395936 395954 395973 398101 })
```

ASN list: [Go](./List-IDCAS.txt)

## Risk IP verification
The third rule uses the judgment of threat scores and IP lists. The former is a score used by Cloudflare to determine IP reputation, ranging from good to bad, ranging from 0-100; the latter is captured by honeypots and other methods, and can be increased or decreased based on your actual performance.

Since the number of IP lists provided is large, direct configuration exceeds the character limit of firewall rules. Therefore, you need to create a list containing high-risk IPs through [Manage Account]-[Configuration]-[List]-[Create New List] (the csv for import is below), and then directly match this list in the firewall.

Primitive expression:
```
(ip.src in $risk_ip) or (cf.threat_score gt 30)
```
Threat score will no longer be supported as a field for rules. Cloudflare's upgraded security automatically protects your domain from malicious traffic. Read the blog [post](https://blog.cloudflare.com/enhanced-security-and-simplified-controls-with-automated-botnet-protection/) to learn more.
```
(ip.src in $risk_ip)
```
$risk_ip is your IP list name

Risk IP list: [GO](./List-RiskIP.txt)
Risk IP CSV: [GO](./List-RiskIP.csv)

## Host name details


| Matching rules | Explain |
| ----------- | ----------- |
| Hostname	|  Configuration for entering website domain name |
| URL path	|  Matching against content in the access link path |
| Country/Region	|  Matching for access IP source region |
| SSL/HTTPS	|  Matching whether to use https access |

The combination of the first three contents and the above four can flexibly define the scope. For example, host name + URL path can set higher verification requirements for specific directories and specific files (such as login pages, etc.). Take the blogger himself as an example. The directory where the blogger's static resources are located will be synchronized to the overseas origin site. With the following configuration, you can delineate access to PHP files and directories that do not match the two static files, and block them. There are many similar applications, you can think more and try~

### Risk UA Basic Rules

```
(http.user_agent contains "fuck") or (http.user_agent contains "lient" and http.user_agent contains "ttp") or (http.user_agent contains "java") or (http.user_agent contains "Joomla") or (http.user_agent contains "libweb") or (http.user_agent contains "libwww") or (http.user_agent contains "PHPCrawl") or (http.user_agent contains "PyCurl") or (http.user_agent contains "python") or (http.user_agent contains "wrk") or (http.user_agent contains "hey/") or (http.user_agent contains "Acunetix") or (http.user_agent contains "apache") or (http.user_agent contains "BackDoorBot") or (http.user_agent contains "cobion") or (http.user_agent contains "masscan") or (http.user_agent contains "FHscan") or (http.user_agent contains "scanbot") or (http.user_agent contains "Gscan") or (http.user_agent contains "Researchscan") or (http.user_agent contains "WPScan") or (http.user_agent contains "ScanAlert") or (http.user_agent contains "Wprecon") or (http.user_agent contains "virusdie") or (http.user_agent contains "VoidEYE") or (http.user_agent contains "WebShag") or (http.user_agent contains "Zeus") or (http.user_agent contains "zgrab") or (http.user_agent contains "zmap") or (http.user_agent contains "nmap") or (http.user_agent contains "fimap") or (http.user_agent contains "ZmEu") or (http.user_agent contains "ZumBot") or (http.user_agent contains "Zyborg") or (http.user_agent contains "attachment") or (http.user_agent eq "undefined") or (http.user_agent eq "")
```

### Risk UA Other Rules

```
(http.user_agent contains "Abonti") or (http.user_agent contains "admantx") or (http.user_agent contains "aipbot") or (http.user_agent contains "AllSubmitter") or (http.user_agent contains "Backlink") or (http.user_agent contains "backlink") or (http.user_agent contains "Badass") or (http.user_agent contains "Bigfoot") or (http.user_agent contains "blexbot") or (http.user_agent contains "Buddy") or (http.user_agent contains "CherryPicker") or (http.user_agent contains "cloudsystemnetwork") or (http.user_agent contains "cognitiveseo") or (http.user_agent contains "Collector") or (http.user_agent contains "cosmos") or (http.user_agent contains "CrazyWebCrawler") or (http.user_agent contains "Crescent") or (http.user_agent contains "Devil") or (http.user_agent contains "domain" and http.user_agent contains "spider") or (http.user_agent contains "domain" and http.user_agent contains "stat") or (http.user_agent contains "domain" and http.user_agent contains "Appender") or (http.user_agent contains "domain" and http.user_agent contains "Crawler") or (http.user_agent contains "DittoSpyder") or (http.user_agent contains "Konqueror") or (http.user_agent contains "Easou") or (http.user_agent contains "Yisou") or (http.user_agent contains "Etao") or (http.user_agent contains "mail" and http.user_agent contains "olf") or (http.user_agent contains "mail" and http.user_agent contains "spider") or (http.user_agent contains "exabot.com") or (http.user_agent contains "getintent") or (http.user_agent contains "Grabber") or (http.user_agent contains "GrabNet") or (http.user_agent contains "HEADMasterSEO") or (http.user_agent contains "heritrix") or (http.user_agent contains "htmlparser") or (http.user_agent contains "hubspot") or (http.user_agent contains "Jyxobot") or (http.user_agent contains "kraken") or (http.user_agent contains "larbin") or (http.user_agent contains "ltx71") or (http.user_agent contains "leiki") or (http.user_agent contains "LinkScan") or (http.user_agent contains "Magnet") or (http.user_agent contains "Mag-Net") or (http.user_agent contains "Mechanize") or (http.user_agent contains "MegaIndex") or (http.user_agent contains "Metasearch") or (http.user_agent contains "MJ12bot") or (http.user_agent contains "moz.com") or (http.user_agent contains "Navroad") or (http.user_agent contains "Netcraft") or (http.user_agent contains "niki-bot") or (http.user_agent contains "NimbleCrawler") or (http.user_agent contains "Nimbostratus") or (http.user_agent contains "Ninja") or (http.user_agent contains "Openfind") or (http.user_agent contains "Page" and http.user_agent contains "Analyzer") or (http.user_agent contains "Pixray") or (http.user_agent contains "probethenet") or (http.user_agent contains "proximic") or (http.user_agent contains "psbot") or (http.user_agent contains "RankActive") or (http.user_agent contains "RankingBot") or (http.user_agent contains "RankurBot") or (http.user_agent contains "Reaper") or (http.user_agent contains "SalesIntelligent") or (http.user_agent contains "Semrush") or (http.user_agent contains "SEOkicks") or (http.user_agent contains "spbot") or (http.user_agent contains "SEOstats") or (http.user_agent contains "Snapbot") or (http.user_agent contains "Stripper") or (http.user_agent contains "Siteimprove") or (http.user_agent contains "sitesell") or (http.user_agent contains "Siphon") or (http.user_agent contains "Sucker") or (http.user_agent contains "TenFourFox") or (http.user_agent contains "TurnitinBot") or (http.user_agent contains "trendiction") or (http.user_agent contains "twingly") or (http.user_agent contains "VidibleScraper") or (http.user_agent contains "WebLeacher") or (http.user_agent contains "WebmasterWorldForum") or (http.user_agent contains "webmeup") or (http.user_agent contains "Webster") or (http.user_agent contains "Widow") or (http.user_agent contains "Xaldon") or (http.user_agent contains "Xenu") or (http.user_agent contains "xtractor") or (http.user_agent contains "Zermelo")
```
