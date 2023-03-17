---
title: system
date: "2021-05-01T21:36:34+0800"
draft: false
categories: ["system"]
---
[system](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/chap-managing_services_with_systemd#sect-Managing_Services_with_systemd-Targets)
[Understanding Systemd Units and Unit Files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files)

## what

![Zw5T6w](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210418/Zw5T6w.jpg)

is the init process(pid=1); a replacement of sysV and upstart;

1. 初始化系统：start the rest of system 
2. 管理服务/进程



## the unit(resouce) type

1. service: an applicaton;
2. target: g group of unit;
3. timer: like cron 
4. socket:  the servie active by a socket

## the process of a application

1. start: ExecStart
2. stop:
   1. use ExecStop=
   2. send signTerm, after 90s, send SINGKILL

## configure file

file path:
1. `/usr/lib/systemd/system/: install  by package manager 
2. ` /etc/systemd/system/` : user local  create

```conf
[Unit]
Description=<description about this service>

[Service]
User=<user e.g. root>
WorkingDirectory=<directory_of_script e.g. /root>
ExecStart=<script which needs to be executed>
Restart=always

[Install]
WantedBy=multi-user.target
```



```shell
systemctl list-unit-files # list install units
systemctl list-unit   # list unit in memory
```

the base resoruce systemed manage


2. unit file example

```systemd
[Unit]
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
User=kafka
ExecStart=/home/kafka/kafka/bin/zookeeper-server-start.sh /home/kafka/kafka/config/zookeeper.properties
ExecStop=/home/kafka/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```


### syntax

#### unit

1. required:
   activate the target

2. after:
   make sure that current unit be started after target unit is active;












## command

1. systemctl list-units: list all install files

   1. --type: unit type
   2. --state: load, acive, enabled

   `systemctl list-units --type=service --state=active`


## components

what: view  log of systemd  service

collect type:
1. auto collect: standout, standerr 
2. upload to syslog server  : 
	```nginx

	server {
       error_log syslog:server=unix:/dev/log;
       access_log syslog:server=unix:/dev/log;
    }
```


command： 
journalctl -f -u nginx 
journalctl -p err -u nginx



## the best practice to start a service

1. create user
2. assign the user privilege of some files
3. create unit files

## run level

 define a mode  a os run in  

1. shut down
2. single user
3. multi-user
5. multi-user with gui
6. reboot the system

systemd use target to descirpt it ;

target have two meaning: in a state and  a set of service;

1. network.target：
2. network-online.targe
