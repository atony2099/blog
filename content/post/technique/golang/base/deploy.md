
## how 

1.  send bin to  a host 
```
scp ./myapp root@123456:/var/www/
```
1. run by a system service
```service
[Unit]
Description=Go sample web app
After=multi-user.target

[Service]
User=root
Group=root
ExecStart=/root/go/hello_world

[Install]
WantedBy=multi-user.target

```


