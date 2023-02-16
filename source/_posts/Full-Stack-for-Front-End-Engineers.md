---
title: Full Stack for Front End Engineers
date: 2019-05-30 10:34:15
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, fullstack, nodejs, DNS, server]
---

## Introduction

[Full Stack For Frontend](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.p)

### Things you’ll learn

+ Level up your command line skills
+ How the internet works
+ How to create and manage a web server
+ Create a deploy system for a Node app
+ Build a basic web page

<!--more-->

## Domains

### Introduction Domains

frontendmasters.com is a domain

tech.frontendmasters.com is a sub domain

domains are just a high level wrapper on an IP address

### DNS

+ IP(Internet Protocol) Address - 127.0.0.1
+ DNS(Domain Name System - "The internet phonebook”) - Maps IP addresses to domains

通常我们用tcp(transmission control protocol)/ip协议进行网络连接

www.jemyoung.com === 23.23.185.61

### Exercise 1: Ping

`ping google.com`

```bash
PING baidu.com (123.125.114.144): 56 data bytes
64 bytes from 123.125.114.144: icmp_seq=0 ttl=44 time=56.939 ms
64 bytes from 123.125.114.144: icmp_seq=1 ttl=44 time=58.034 ms
64 bytes from 123.125.114.144: icmp_seq=2 ttl=44 time=58.050 ms
64 bytes from 123.125.114.144: icmp_seq=3 ttl=44 time=57.737 ms
64 bytes from 123.125.114.144: icmp_seq=4 ttl=44 time=57.893 ms
^C
--- baidu.com ping statistics ---
5 packets transmitted, 5 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 56.939/57.731/58.050/0.412 ms
```

### DNS Caches

+ Local cache
+ LAN(Local Area Network) DNS server
+ ISP(Internet Service Provider) DNS server

### Security

DDoS(distributed denial of service)

### Cache Poisoning

通过伪造dns指向的ip地址来使用户访问伪造的网站（用户访问jemyoung.com，本来应该指23.23.185.61，通过伪造dns信息来指向例如104.24.122.137来获取用户信息）

可以通过使用https来避免此类事情发生

### Exercise 2: Traceroute

ping tells us is the server alive

traceroute tell us these are all the hops that your request took to get to a server.

Windows: `tracert netflix.com`
OSX: `traceroute netflix.com`

```bash
traceroute: Warning: baidu.com has multiple addresses; using 220.181.57.216
traceroute to baidu.com (220.181.57.216), 64 hops max, 52 byte packets
 1  domynet (192.168.10.1)  1.709 ms  1.000 ms  1.154 ms
 2  113.46.0.1 (113.46.0.1)  3.656 ms  3.410 ms  4.001 ms
 3  124.205.97.44 (124.205.97.44)  3.257 ms  5.572 ms  3.433 ms
 4  218.241.240.137 (218.241.240.137)  3.943 ms  3.075 ms  3.975 ms
 5  218.241.240.157 (218.241.240.157)  15.840 ms  4.387 ms  4.171 ms
 6  218.241.245.57 (218.241.245.57)  4.249 ms
    218.241.255.142 (218.241.255.142)  4.519 ms
    218.241.245.57 (218.241.245.57)  3.941 ms
 7  218.241.244.133 (218.241.244.133)  42.577 ms * *
 8  10.15.88.53 (10.15.88.53)  5.957 ms  5.507 ms *
 9  10.15.88.49 (10.15.88.49)  26.937 ms  26.990 ms  27.763 ms
10  * * *
11  10.0.36.1 (10.0.36.1)  29.355 ms  28.806 ms  30.571 ms
12  10.23.60.1 (10.23.60.1)  33.227 ms  35.562 ms *
13  10.143.142.5 (10.143.142.5)  35.238 ms
    10.143.142.1 (10.143.142.1)  33.953 ms  34.652 ms
14  172.16.10.13 (172.16.10.13)  33.798 ms
    172.16.11.25 (172.16.11.25)  32.811 ms
    172.16.11.17 (172.16.11.17)  33.849 ms
15  61.177.240.69 (61.177.240.69)  34.029 ms  34.730 ms
    61.177.248.129 (61.177.248.129)  32.671 ms
16  61.177.249.217 (61.177.249.217)  40.711 ms
    61.177.249.193 (61.177.249.193)  39.546 ms  40.457 ms
17  202.97.85.9 (202.97.85.9)  57.730 ms
    202.97.85.1 (202.97.85.1)  62.182 ms
    202.97.85.5 (202.97.85.5)  63.798 ms
18  220.181.177.230 (220.181.177.230)  58.605 ms  58.312 ms
    36.110.244.18 (36.110.244.18)  56.067 ms
19  * 36.110.246.201 (36.110.246.201)  66.259 ms  60.270 ms
```

ICMP - Internet Control Message Packet

## Vim

### Exercise 3: Introducing VIM

*What is VIM(VI Improved)?*

A ubiquitous text editor

*Why should I care?*

Because servers don’t have GUI’s

[Vim isn't an editor designed to hold its users' hands. It is a tool, the use of which must be learned.”](http://www.vim.org/about.php)

command line: `vi`

### Exercise 4: Vim modes

VIM has three modes

+ command mode
+ insert mode
+ last-line mode

#### VIM - insert mode

1. press i
2. type “Haha VIM is easy”

### VIM Command Mode

`u` undo

`control + r` redo

`dd` delete the line

### Exercise 5: Vim command mode

1. type “Hello, world!”
2. press enter
3. type “Hello, class!”
4. press esc
5. type /Hello
6. press enter
7. press n to search forward
8. press N to search backward

[Vi Commands Cheat Sheet](https://linuxmoz.com/vi-commands-cheat-sheet/)

+ Vi Page Down
  `ctrl + f` Vi page down - Moves forward a page `ctrl + d` - Moves forward half a page

+ Vi Page Up
  `ctrl + b` Vi page up - Moves back a page `ctrl + u` - Moves backward a half-page

+ Vi Delete Line
  `dd` Vi delete line, regradless of the cursors position on the line

+ Vi Save & Exit
  `:q` - Vi exit - this will close Vi
  `:wq` - Vi save & exit
  `:!q` - Exits vi and discards and changes you mode

### Editing and Saving in Vim

#### VIM - last-line mode

Quit without saving

1. press esc
2. press :
3. type q
4. :q!

Save and quit

1. press esc
2. press :
3. type wq

### Exercise 6: Editing & Saving in Vim

```bash
<html>
  <h1>VIM Commands</h1>
  <p>^F (CTRL F) Forward screenful</p>
  <p>^B Backward screenful</P>
  <p>^f one page forward</p>
  <p>^b One page backward</p>
  <p>u Undo</p>
  <p>^R Redo</p>
  <p>$ Move cursor to the end of current line</p>
  <p>0 (zero) Move cursor to the beginning of line</p>
  <p>w Forward one word</p>
  <p>b Backward one word</p>
</html>
```

## Servers

### Exercise 7: Introducing Servers

#### Logging into a server

`$ ssh student@138.197.253.87`

### SSH

SSH(Secure Socket Shell)

It's a way of connecting to remote devices and there is two basic ways of doing that.

there is username/password which we just did, so student was the username, and there is also ssh key.

*What’s wrong with logging in with a password?*

easy passwords:

+ 12345
+ password
+ qwerty
+ qazwsx
+ 12345678

A day in the life of a server

会看到一直会有用户试图破解登录

#### Public Key Authentication

We're gonna use something called Public Key Authentication(公共密钥身份验证), instead of user name, password. Beaucase passwords are easily guessable even if you have a 21 character password, that's not that secure.

your computer                           server

private key     =================>      public key
(secret)        encrypted messages
                <=================

### Exercise 8: Creating an SSH Key

```bash
cd ~/.ssh/
ssh-keygen -t rsa
```

-t为指定加密类型，rsa为具体的加密类型

```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/liuyong/.ssh/id_rsa): test_key
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in test_key.
Your public key has been saved in test_key.pub.
The key fingerprint is:
SHA256:R0qkBRqM0wK7dranNSqamcAru0sEqXEC5FwcaFxvIYg liuyong@liuyongs-MBP.lan
The key's randomart image is:
+---[RSA 2048]----+
|*o=Oo...o        |
|EB=.=o.+         |
|Bo.o.o. . .      |
|o=  .  . o       |
|oo o    S .      |
|+ o .    .       |
|.o . +           |
|+=. = .          |
|@=.o             |
+----[SHA256]-----+
```

```bash
less test_key.pub

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC8NJ5OyLNsVzCZ44Yg0wTAETrdH4yZzITYPIHizh0OBbVp9xnsLdAQNNv4xlrB6Qe+IJ5/AGi268A+qlc6GCdxrePQOET3VGbV+szjko/1HhKbMRxXDFi3HwMudTibdlZkxRuSl8Zg7WJCTfHlmSBgrOHzJDtrM1Qj3mHd9ed7Sgt1pRezsyU7HLtXl4PMUBLJiU6Ndu+vGp5rUkbTTb4NmRqqBHQ0k+jHP2sasijGMvucD+x3oCAt7mKdY3fiiBL5n7z8p57t94SderdL0wzqmkftTGsObt/CecH6T1nDeGJW6BWXB+2Kdr64nSH25zi71/AzDZWH0Rx1W9g2vOB7 liuyong@liuyongs-MBP.lan

test_key.pub (END)
```

```bash
less test_key

-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABFwAAAAdzc2gtcn
NhAAAAAwEAAQAAAQEAvDSeTsizbFcwmeOGINMEwBE63R+MmcyE2DyB4s4dDgW1afcZ7C3Q
EDTb+MZawekHviCefwBotuvAPqpXOhgnca3j0DhE91Rm1frM45KP9R4SmzEcVwxYtx8DLn
U4m3ZWZMUbkpfGYO1iQk3x5ZkgYKzh8yQ7azNUI95h3fXne0oLdaUXs7MlOxy7V5eDzFAS
yYlOjXbvrxqea1JG002+DZkaqgR0NJPoxz9rGrIoxjL7nA/sd6AgLe5inWN34ogS+Z+8/K
**********************************************************************
**********************************************************************
**********************************************************************
**********************************************************************
**********************************************************************
WXB+2Kdr64nSH25zi71/AzDZWH0Rx1W9g2vOB7AAAAAwEAAQAAAQAIGee6km8Ouc4Qwbdz
UEIDFnrHK+V8dU4+lJQutBqSEPLXJdjKzsoG+I+DbN5ZPHOl3neUFetr/EfhKqnoiUOhZA
/LVbC6SLrIeSw6m4Nw1KOOZJXItXJYJ5CWfls6beSYH9fo/nl+kJlLK1m/PVEkeCydr7CT
E7mKdbcWu9AEAX/0O7JtseUhqy1LzYChRSVjJLgjmrxh8VTD59rS6Z4P8i3rdX4oaR2t7M
FplbDnUL1QMDFF+v5DQGO3qKaJTjeFm9FVv3jjro7P3bt/4r5nuwCah6q8gewNRMkuPJq3
ZF75sM7QNeGOEYTqExspeJtggJgdTxwlUP/gVdE9q4EhAAAAgCgqM1IvhiPPDyHd/jeWfu
/a0n1bGzCEX4JdM/r9g+Gc3st+COWnFnkoj1qiVFQqhm2aLzF8lw6wQ//+xb9kp4+tzJM+
+a4BpJG6i8no8lwGstQd22NleSc7mY0Q908UPV02216ysOw6Lz2khOjnfbbfqbl5Pb/VXd
FX9hlHz2f4AAAAgQDw50EzpvP48vXAgyi1eN6Neql3A0TTr2fdX71dZlt9Gy5myMYLvqqR
N0lofGSZbgaqXwpUc3KcDAKNU59WauApLHeqBrrLGs0mUWGtsWD2zrhOYkOE/1gcqsWNPM
ouJxCfgzSYpRKXB3pJuYPJNwca9xK2aLZfWIFNKCBGqd7gywAAAIEAx//yk8sEEQGc9JHu
test_key
```

*Don’t lose your private key!*

## Setting up a VPS

### VPS & Cloud Computing

#### servers

+ Web server
+ Database server
+ Storage server

+ Dedicated server
+ VPS(Virtual Private Server)

Advantages of the cloud

+ flexible
+ scalable
+ on demand

Providers

+ AWS
+ Rackspace
+ Digital Ocean

### Exercise 9: Buy a VPS

[www.digitalocean.com](www.digitalocean.com)

```bash
cat ~/.ssh/my_key.pub
```

### Exercise 10: Log onto your own server

```bash
ssh -i ~/.ssh/my_key root@$YOUR_SERVER_IP
```

### Exercise 11: top

```bash
top
```

```bash
top - 01:35:00 up 12 min,  1 user,  load average: 0.00, 0.00, 0.00
Tasks:  80 total,   1 running,  44 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.3 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0
KiB Mem :  1009128 total,   720024 free,    75444 used,   213660 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   793064 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND  
    8 root      20   0       0      0      0 I  0.3  0.0   0:00.07 rcu_sched
 1249 root      20   0   44400   3796   3256 R  0.3  0.4   0:00.04 top
    1 root      20   0   77640   8692   6604 S  0.0  0.9   0:02.26 systemd  
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd
    3 root      20   0       0      0      0 I  0.0  0.0   0:00.00 kworker/+
    4 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 kworker/+
    5 root      20   0       0      0      0 I  0.0  0.0   0:00.02 kworker/+
    6 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 mm_percp+
    7 root      20   0       0      0      0 S  0.0  0.0   0:00.06 ksoftirq+
    9 root      20   0       0      0      0 I  0.0  0.0   0:00.00 rcu_bh
   10 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migratio+
   11 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 watchdog+
   12 root      20   0       0      0      0 S  0.0  0.0   0:00.00 cpuhp/0  
   13 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kdevtmpfs
   14 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 netns
   15 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_task+
   16 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kauditd  
```

```bash
q
```

```bash
htop
```

```bash
CPU[                        0.0%]   Tasks: 26, 12 thr; 1 running
  Mem[||||||||||        74.5M/985M]   Load average: 0.00 0.00 0.00
  Swp[                       0K/0K]   Uptime: 00:15:48

  PID USER      PRI  NI  VIRT   RES   SHR S CPU% MEM%   TIME+  Command
 1252 root       20   0 32064  4096  3516 R  0.0  0.4  0:00.04 htop
    1 root       20   0 77640  8692  6604 S  0.0  0.9  0:02.27 /sbin/init
  389 root       19  -1 78464  7048  6368 S  0.0  0.7  0:00.52 /lib/systemd/s
  391 root       20   0 97708  1876  1700 S  0.0  0.2  0:00.00 /sbin/lvmetad
  516 systemd-t  20   0  138M  3376  2852 S  0.0  0.3  0:00.00 /lib/systemd/s
  500 systemd-t  20   0  138M  3376  2852 S  0.0  0.3  0:00.03 /lib/systemd/s
  594 systemd-n  20   0 71972  5812  5084 S  0.0  0.6  0:00.01 /lib/systemd/s
  612 systemd-r  20   0 70624  5224  4660 S  0.0  0.5  0:00.02 /lib/systemd/s
  680 root       20   0 42876  4008  3296 S  0.0  0.4  0:00.13 /lib/systemd/s
  829 daemon     20   0 28332  2400  2192 S  0.0  0.2  0:00.00 /usr/sbin/atd
  844 root       20   0 95540  1552  1416 S  0.0  0.2  0:00.00 /usr/bin/lxcfs
  845 root       20   0 95540  1552  1416 S  0.0  0.2  0:00.00 /usr/bin/lxcfs
  832 root       20   0 95540  1552  1416 S  0.0  0.2  0:00.00 /usr/bin/lxcfs
  848 syslog     20   0  256M  4512  3628 S  0.0  0.4  0:00.00 /usr/sbin/rsys
  849 syslog     20   0  256M  4512  3628 S  0.0  0.4  0:00.00 /usr/sbin/rsys
  850 syslog     20   0  256M  4512  3628 S  0.0  0.4  0:00.00 /usr/sbin/rsys
  839 syslog     20   0  256M  4512  3628 S  0.0  0.4  0:00.00 /usr/sbin/rsys
F1Help  F2Setup F3SearchF4FilterF5Tree  F6SortByF7Nice -F8Nice +F9Kill  F10Qu
```

### Exercise 12: Setting up your server

#### update software

```bash
apt-get update
```

#### create a new user

```bash
adduser $USERNAME
```

#### add user to sudo(super user do) group

```bash
usermod -aG sudo $USERNAME
```

#### switch user

```bash
su $USERNAME
```

#### make sure user has sudo access

```bash
sudo cat /var/log/auth.log
```

#### exit

```bash
exit
exit
```

### Logging In with SSH

In case of secure, we wanna use an SSH key instead of a password.

We can do it through SSH commands.

```bash
cat ~/.ssh/my_key.pub | ssh $USERNAME@$SERVER_IP "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

#### print the contents of *my_key.pub* and pipe the output to the next command

```bash
cat ~/.ssh/my_key.pub |
```

#### ssh into your server

```bash
ssh $USERNAME@$SERVER_IP
```

#### make a *.ssh* folder in your home directory if it doesn't already exit

```bash
mkdir -p ~/.ssh
```

#### write the contents of the output to the file *authorized_keys*

```bash
cat >> ~/.ssh/authorized_keys"
```

#### log in

```bash
ssh -i ~/.ssh/my_key.pub $USERNAME@$SERVER_IP
```

### Disable Root Access

```bash
sudo vi /etc/ssh/sshd_config
```

```bash
PermitRootLogin no
```

```bash
sudo service ssh restart
```

## Getting a Domain Name

### Buying a Domain

[www.namecheap.com](https://www.namecheap.com/)

### Associating the DNS

1. Get the IP address of your VPS
2. Add 2 A Records with your IP address
   + @
   + www

### Using a CNAMEs

“The A record maps a name to one or more IP addresses, when the IP are known and stable.”

“The CNAME record maps a name to another name.”

[link](https://support.dnsimple.com/articles/differences-between-a-cname-alias-url/)

## Setting Up the Server

### Introducing Unix Operating Systems

Two main types of server operating systems

1. windows
2. unix

Why Unix?

+ ubiquity
+ open-source
+ (mostly) free

Common unix commands

+ cd
+ crontab
+ ls
+ mkdir
+ pwd
+ rm

### Introducing Nginx

Nginx(engine X)

“A HTTP and reverse proxy server, a mail proxy server, and a generic TCP/UDP proxy server”
“一个HTTP和反向代理服务器，一个电子邮件代理服务器，和一个通用TCP/UDP代理服务器。”

+ reverse proxy
+ http server

### Exercise 13: Nginx

install nginx

```bash
sudo apt-get install nginx
```

start nginx

```bash
sudo service nginx start
```

type your server's IP address into a browser

### Exercise 14: Nginx configuration

```bash
sudo cat /etc/nginx/sites-available/default
```

作者更推荐使用less

```bash
sudo less /etc/nginx/sites-available/default
```

cat dumps everything, less gives you more page by page.

jemyoung.com => 23.23.185.61 => Nginx/Apache

### Installing Git, Node.js, and NPM

install git

```bash
sudo apt-get install git
```

install node and npm

```bash
sudo apt-get install nodejs npm
```

### Making a Symbolic Link

symlink nodejs to node

新版本node似乎并不需要

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

### Creating a Web Directory

make a web directory(if it doesn't already exit)

似乎也不需要

```bash
sudo mkdir -p /var/www
```

### Changing Permisssions

change ownership of the web directory to the current user

```bash
sudo chown -R $USER:$USER /var/www
```

move into web directory

```bash
cd /var/www
```

### Cloning a Git Repository

```bash
git clone https://github.com/young/Dev-Ops-for-Frontend.git
```

### Rename directory and installing npm

remove directory to app/

```bash
mv Dev-Ops-for-Frontend/ app/
```

move into to app directory

```bash
cd app/
```

install npm packages

```bash
npm i
```

### Starting the Server

start server

```bash
node app.js
```

type in your server's IP address in a browser, port 3001.

## Modifying the Server

### Changing the Server Ports

```bash
sudo vi /etc/nginx/sites-available/default
```

```bash
location / {
  proxy_pass http://127.0.0.1:3001/;
}
```

restart nginx

```bash
sudo service nginx restart
```

start server

```bash
node app.js
```

#### html

move into a app directory

```bash
cd /var/www/app/
```

make a new branch

```bash
git checkout -b some_branch_name
```

modify the html to you hearts content

```bash
vi index.html
```

## Deploying Code

### Deploying Code & Introduction Gulp

How do we build and deploy our app?

Gulp: a Javascript task runner

Similar:

+ Grunt
+ Broccoli
+ Brunch
+ Jake

### Exercise 15: Creating Gulp Tasks

```js
gulp.task('default', function() {
  return gulp.src('sass/*.scss')
    .pipe(watch('sass/*.scss'))
    .pipe(sass())
    .pipe(gulp.dest('dist'))
})
```

1. Open gulpfile.js in VI. It’s in the root directory of the demo project.
2. Fill in the gulp task clean-css so that any css files in the dist folder are removed.
3. Add your new task to build task.

Create gulp tasks

1. Create a gulp task to compile and concat your less files. The css should be placed in the dist folder.
2. Add your new task to build task.

```js
// compile and concat less files to dist folder.
gulp.task('less', function() {
  gulp.src(['./less/base.less'])
    .pipe(less())
    .pipe(concat(CSS_DIST))
    .pipe(gulp.dest(DIST_FOLDER));
});
```

```js
gulp.task('bulid', ['clean-js', 'clean-css', 'scripts', 'less'])
```

### Installing Gulp

install gulp

```bash
npm i -g gulp
```

[Resolving EACCES permissions errors when installing packages globally](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)

### Keeping an app running

How do we keep our app up and running?

Forever- a process manager that keeps a process running indefinitely

Similar:

+ Strong Loop Process Manager
+ PM2

```bash
pwd
/var/www/app
```

#### install forever globally

```bash
npm i -g forever
```

##### start forever

```bash
forever start app.js
```

##### stop forever

```bash
forever stopall
```

## Log files

### Logging files

#### create a log file for the forever process

```bash
sudo mkdir /var/log/forever
```

#### change owner to current user

```bash
sudo chown -R $USER /var/log/forever
```

#### start forever and log the output

```bash
forever start app.js >> /var/log/forever/forever.log
```

### Exercise 16: Tailing a log file

```bash
sudo tail -f /var/log/auth.log
```

## Putting it all together

### Exercise 17: Putting it all together

package.json

```js
"scripts": {
  "deploy": "gulp deploy && forever stopall && forever start app.js >> /var/log/forever/forever.log"
}
```