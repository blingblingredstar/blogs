---
title: Full-Stack-for-Front-Ends-Part-2
date: 2019-06-22 09:56:18
categories: 学习笔记
tags: [FrontEndMasters, NodeJs, HTTPS, HTTP/2, Websockets, Nginx, Databases]
---

## Getting Started with Full Stack

### Course Introduction

[Slides](jemyoung.com/fefs2)

[Part 1](jemyoung.com/fefs)

#### Things you'll learn

+ Learn how to use Node and Bash to create shell scripts
+ Learn advanced Nginx configuration
+ Common server vulnerabilibies and how to mitigate them
+ How to add HTTPS to your server
+ Understand databases
+ Containers and automating deployments
<!--more-->

### Full Stack for Frontend Part 1 Recap & Purpose

#### Recap

+ How the internet works
  + domain => IP address
+ Command line basics
  + `ping`
  + `traceroute`
  + `vi`
+ How to create and manage a web server
+ Create a deploy system for a Node app
+ Build a basic web page

#### Why full stack

emm.. a lot of reasons...

## Server Setup

### Basic Server Setup, Updating and User Permissions

1. [Create a new Ubuntu server](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.g15d90c66a5_0_75)
  a. Use Ubuntu 16.04.x
  b. Be sure to use an SSH key
2. [Point domain to new server](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.g15e0fb5a48_1_172)
3. [Log into server as root](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.g15e0fb5a48_2_87)
4. [Update server](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.g15e0fb5a48_2_127)
5. [Add a new user ‘test’](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.g15e0fb5a48_2_127)
6. [Grant ‘test’ sudo access](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.g15e0fb5a48_2_127)
7. Switch to ‘test’ user

### Node setup and installation

#### update apt repo for nodejs

```bash
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
```

[explain shell](https://explainshell.com/explain?cmd=curl+-sL)

#### install nodejs and npm

```bash
sudo apt install nodejs
```

#### check npm directory

```bash
npm config get prefix
```

**If the npm directory is not /usr/local, follow instructions [here](https://docs.npmjs.com/getting-started/fixing-npm-permissions#option-2-change-npms-default-directory-to-another-directory)**.

更改npm安装目录主要是权限问题。如果安装在全局文件夹下，需要sudo权限才能安装全局包。

#### install forever module

```bash
npm i -g forever
```

#### change working directory

```bash
cd /var/www
```

#### 更改文件夹权限

```bash
sudo chown -R $USER:$USER /var/www
```

#### clone repo

```bash
git clone https://github.com/young/fsfe2.git
```

#### change working directory again

```bash
cd fsfe2
```

#### install module

```bash
npm i
```

## Server Security

### Security Overview

+ Control access
  + Strong authentication
  + Firewalls
  + user/file permissions
+ Secure your applications
  + Keep software up to date
  + limit application use

### Adding SSH keys

#### create a new directory

```bash
mkdir -p ~/.ssh
```

#### paste **public** key into *authorized_keys* file

```bash
vi ~/.ssh/authorized_keys
```

**Be sure to test logging in with your new user**.

### Security Setting

1. Add a password for root
   + use the `passwd` command
2. [Disable root login](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.g18b69b3036_0_249)
3. [Disable password login](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.g18b69b3036_0_249)

### Firewalls

[nmap](https://nmap.org/)

```bash
nmap YOUR_SERVER_IP
```

### Firewall configurations: iptables

```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

+ `-A` append rule
+ `-p` protocol(tcp, icmp)
+ `--dport` destination port
+ `-j` jump(DROP, REJECT, ACCEPT, LOG)

#### Create an iptable rule to block all outgoing HTTP connections

```bash
iptables -A OUTPUT -p tcp --dport 80 -j REJECT
```

#### Create an iptable rule to only allow icmp connections on port 892 from the IP address 192.0.0.1

```bash
iptables -A INPUT -s 192.0.0.1 -p icmp --dport 892 -j ACCEPT
```

### Firewall configurations: UFW and GUI options

There has to be a better way!

#### ufw - uncomplicated firewall

```bash
sudo ufw allow ssh
sudo ufw enable
```

Create a ufw rule to block all outgoing HTTP connections

```bash
ufw reject out http
```

#### GUI

Degital Ocean的Droplets下的networking下的firewalls选项

### Automatic Updates

```bash
sudo apt install unattended-upgrades
```

`/etc/apt/apt.conf.d/20auto-upgrades`

```bash
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

[https://wiki.debian.org/UnattendedUpgrades](https://wiki.debian.org/UnattendedUpgrades
)

```bash
sudo vi /etc/apt/apt.conf.d/50unattended-upgrades
```

### Fail2ban: Exercise

#### Install fail2ban

```bash
sudo apt install fail2ban
```

#### Copy jail file

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

### Edit configuration file

```bash
sudo vi /etc/fail2ban/jail.local
```

*WARNING*!

If you misconfigure fail2ban, you can lock yourself out of your server!

## Advanced Shells

+ Finding Things
+ Redirection Operators
+ Shells
+ Shell Scripts

### Shell Tools: Find and Grep

#### find: search file *names*

| `find` | `/bar`    | `-name` | `foo.txt`   |
| ------ | --------- | ------- | ----------- |
| find   | directory | option  | file/folder |

useful options

+ -name
+ -type
+ -empty
+ -executable
+ -writable

#### grep: search file *contents*

| `grep` | `-i`    | `'jem'`           | `/var/www` |
| ------ | ------- | ----------------- | ---------- |
| grep   | options | search expression | directory  |

search inside gzip file

```bash
zgrep FILE
```

### Find: Exercise

find all log files in /log

```bash
find /var/log/ -type f -name *.log
```

find all empty files

```bash
find /etc -type f -empty
```

find all directories with the word log

```bash
find / -type d -name log
```

### Grep: Exercise

```bash
ps aux | grep node
```

### Redirection

+ `|`
  + read from stdout
+ `>`
  + write stdout to file
+ `>>`
  + append stdout to file
+ `<`
  + read from stdin
+ `2<`
  + read from stderr

#### write to file

```bash
ps aux > foo
```

```bash
bar < foo > baz
```

### Shells

shell => application => kernel

show current shell:

```bash
echo $0
```

#### Changing Shells

list acceptable shells to change to

```bash
cat /ect/shells
```

change shell to `sh`

```bash
chsh -s /bin/sh
```

login into new shell to see the change

```bash
su $USERNAME
```

change shell to `bash`

```bash
chsh -s /bin/bash
```

### Shell scripting

Why shell scripting ?

+ simple
+ portable

```bash
#!/bin/sh

trap 'echo "/nPlease choose!"' INT

FILES= `find /var/log/ -name *.log -mtime 1`
echo "Old files ready to be deleted:\n"
echo "$FILES"
echo "Do you want to continue? \c"

while :
do
  read INPUT
  case $INPUT in
        y)
          echo "deleting files..."
          break
          ;;
        *)
          echo "Please make a valid selection"
          ;;
  esac
done
```

### Bash scripting: Exercise

```bash
vi load.sh
```

```sh
#!/bin/sh

cat /proc/loadavg | awk '{print $1"-"$2"-"$3}'
```

Get load average:

```sh
cat /proc/loadavg
```

Extract the 1st, 2nd, and 3rd columns of data:

```sh
awk '{print $1"-"$2"-"$3}'
```

Make executable:

```bash
sudo chmod 755 ./load.sh
```

### Creating a shell script with Node: Exercise

create a workspace folder:

```bash
mkdir ~/workspace
```

move into workspace folder:

```bash
cd ~/workspace
```

create index.js:

```bash
touch index.js
```

initialize project:

```bash
npm init
```

add reference to script:

```bash
vi package.json
```

```json
"bin": {
  "myscript": "index.js"
},
```

```bash
vi index.js
```

```js
#!/user/bin/node

const exec = require('child_process').exec
const stat = exec(`cat /proc/loadavg | awk '{print $1"-"$2"-"$3}'`)


stat.stdout.on('data', function(data) {
console.log(data)
})
```

## HTTPS

+ Nginx setup
+ Why HTTPS
+ Getting a certificate
+ Cron

### Nginx Setup: Installation

1. [Install nginx](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.g15f62b0f20_1_259)
   + `sudo apt-get install nginx`
   + `sudo service ngnix start`
2. [Proxy traffic to node server](https://docs.google.com/presentation/d/1FPpbZ919vt8e1k2EGPd7BKhDlHao79ykvYLfDcMOsMo/edit#slide=id.g15e0fb5a48_2_267)
3. [Add domain name](https://docs.google.com/presentation/d/17Iy_MMPTUI0lldjz0AkhDIiVGh_CnXMfKO3ESmCnuyo/edit#slide=next)
4. [Open port 443](https://docs.google.com/presentation/d/17Iy_MMPTUI0lldjz0AkhDIiVGh_CnXMfKO3ESmCnuyo/edit#slide=id.g27c6e6b415_0_692)
5. Reload nginx

### Nginx Setup: proxy traffic to node server

```bash
cd /etc/ngnix/site/available
```

```bash
vi defalut
```

```bash
location / {
                proxy_pass http://127.0.0.1:3001/;
        }
```

测试配置文件是否有效：

```bash
sudo nginx -t

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

```bash
cd /var/www/fsfe2/
```

```bash
forever start app.js
```

启动forever是提示command not found，查了一下是全局变量配置问题，使用如下命令修改配置

```bash
sudo echo -e "export PATH=$(npm prefix -g)/bin:$PATH" >> ~/.bashrc && source ~/.bashrc
```

此时打开网址，可以看到已经显示站点。

### Nginx Setup: Adding a domain name and opening port 443

add domain name to nginx conf

```bash
sudo vi /etc/nginx/sites-available/default
```

```bash
server_name $YOUR_DOMAIN;
```

open port 443:

```bash
sudo ufw allow 443
```

open ssh link

```bash
sudo ufw allow ssh
```

enable ufw

```bash
sudo ufw enable
```

check ufw status

```bash
sudo ufw status
```

### Why HTTPS

简单来说，HTTPS更加安全，是一种必要措施。

+ Security
+ Technology
  + Service Workers
  + Web Bluetooth
+ HTTP/2

### Installing HTTPS certificate

Old way:

[acme-tiny](https://github.com/diafygi/acme-tiny)

New way:

[certbot](https://certbot.eff.org/)

1. Add the certbot repository
   + `sudo add-apt-repository ppa:certbot/certbot`
2. Pull in new repository information
   + `sudo apt update`
3. Install certbot with nginx plugin
   + `sudo apt install python-certbot-nginx`
4. Use certbot to get certificate
   + `sudo certbot --nginx`
5. Test auto renew
   + `sudo certbot renew --dry-run`

安装时开始一直失败，输入`sudo ufw disable`，把防火墙暂时关闭后安装成功。

此时在通过域名访问网站即可发现https已经启用。

### cron

How do we run periodic tasks?

| minute | hour | day of month | month | day of week | command to execute               |
| ------ | ---- | ------------ | ----- | ----------- | -------------------------------- |
| `00`   | `16` | `*`          | `*`   | `5`         | `echo "It's party time!" | wall` |

[crontab-The quick and simple editor for cron schedule expressions](https://crontab.guru)

### cron: Exercise

Open crontab for editing

```bash
sudo crontab -e
```

Renew certificate every week at 12pm on Monday

```bash
00 12 * * 1 certbot renew
```

## Nginx Tuning

+ gzip
+ Websockets
+ http/2

### Nginx Tuning: Overview and Gzip

gzip is an algorithm that runs through and compresses files

```bash
sudo vi /etc/nginx/nginx.conf
```

### Expires headers

```bash
sudo vi /etc/nginx/sites-available/default
```

添加如下规则：

```bash
location /static/ {
  expires 30d;
  proxy_padd http://127.0.0.1:3001/static/;
}
```

测试规则

```bash
sudo nginx -t
```

重启Nginx，刷新网页，会在`Response Headers`的`Cache-Control`中看到`max-age=2592000`。

当缓存时间设置比较长时，网站更新后并不会在客户端更新。

### Caching

上述是客户端缓存设置，也可以对服务端进行缓存设置，来使得一些复杂的sql语句查询或者一些大计算量的请求进行缓存，提高访问速度（缓存时间不宜过长，否则无法进行更新）。

```bash
sudo vi /etc/nginx/sites-available/default
```

文件头部添加（`server{}`外）

```bash
proxy_cache_path /tmp/nginx levels=1:2
keys_zone=slowfile_cache:10m inactive=60m
use_temp_path=off;

proxy_cache_key "$request_uri";
```

```bash
location /slowfile {
  proxy_cache_valid 1m;
  Proxy_ignore_headers Cache-Control;
  add_header X-Proxy-Cache $upstream_cache_status;
  proxy_cache slowfile_cache;
  proxy_pass http://127.0.0.1:3001/slowfile;
}
```

warm cache：对一些耗时较长的请求首先进行缓存，这样在他人访问时可以直接从缓存中获取，提高访问速度。

此时访问网站的/slowfile，第一次访问可以看到请求头添加了`X-Proxy-Cache`字段，并且为`MISS`，请求时间较长。刷新后`X-Proxy-Cache`字段为`HIT`，请求仅耗时300ms。

### Websockets: Setup

```bash
sudo vi /etc/nginx/sites-avaliable/default
```

```bash
location / {
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";

  proxy_pass http://127.0.0.1:3001;
}
```

通过上述配置，Nginx开启Websockets。

### Websockets: Client-side code and observables

### Websockets: Exercise

```bash
cd /var/www/fsfe2/

vi app.js
```

```js
function getServerLoad() {
  return setInterval(() => {
    // Execute bash script
    const loadScript = exec(`~/load.sh`); // 把这里的'~/load.sh'更改为之前创建了shell commands文件。

    loadScript.stdout.on('data', function(data) {
      wss.broadcast(JSON.stringify({name: 'load', data}));
    });

  }, 2 * 1000);
}
```

### http/2

```bash
sudo vi /etc/nginx/sites-avaliable/default
```

```bash
listen 443 http2 ssl; # managed by Cerbot
```

[demo](https://http2.akamai.com/demo)

### http/2: Exercise

更改配置文件后，重启Nginx，打开/cats网址，会看到图片传输协议已经更改为h2

### Redirect

Redirect request to new url

```bash
sudo vi /ect/nginx/sites-avaliable/default
```

```bash
location /help {
  return 301 https://developer.mozilla.org/zh-CN/;
}
```

## Databases

### Database Types

1. relational
   + SQL
     + MySQL
     + Microsoft SQL Server
     + Postgre SQL
2. non-relational
   + NoSQL
     + redis
     + mongoDB
     + cassandra

### Exercise: MySQL Installation

Install MySQL

```bash
sudo apt install mysql-server
```

Run setup script

```bash
mysql_secure_installation
```

Login as root

```bash
mysql -u root -p
```

### Database Best Practice

Tips:

1. Back up your database
2. Use a strong root password
3. Don't expose the database outside the network
4. Sanitize your SQL
5. **Back up your database**

![Little Bobby Tables](https://imgs.xkcd.com/comics/exploits_of_a_mom.png)

## Containers and More

### Containers

+ shared resources
+ no OS
+ fast deployment

[Docker-Enterprise Container Platform for High-Velocity Innovation](https://www.docker.com/)

[Installing Postgres on Docker](https://docs.docker.com/engine/examples/postgresql_service/#installing-postgresql-on-docker)

### Orchestration

[Tools](https://www.linux.com/news/8-open-source-CONTAINER-ORCHESTRATION-TOOLS-KNOW)

[Kubernetes](https://www.aliyun.com/product/containerservice?utm_content=se_1002469384)

### Automated Deployment

+ ANSIBLE
+ VAGRANT
+ puppet
