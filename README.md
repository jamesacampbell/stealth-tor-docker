# stealth-tor-docker
## NOT WORKING YET DO NOT USE

## GOAL   
spin up a new ubuntu based tor-enabled hidden ssh server & client quickly and easily

## WHAT I GOT WORKING

1. server torrc correctly configured with HiddenServiceAuthorizeClient
  ```
  HiddenServiceAuthorizeClient stealth somerandom16charkey    
  
  HiddenServiceDir /var/lib/tor/ssh_onion_service/   
  
  HiddenServicePort 22 127.0.0.1:51900 
  
  HiddenServicePort 80 127.0.0.1:8099
  ```
2. client torrc correctly configured with HidServAuth 
  ```
  HidServAuth blahblahblahserver.onion cookiekey # client somerandom16charkey
  ```

3. ssh configured with ~/.ssh/config and 
  ```
  host hidden2   
  
  CheckHostIP no   
  
  Compression yes   
  
  Port 22 
  
  user root   # (or whatever you set)
  
  hostname blahblahblahserver.onion   
  
  proxyCommand ncat --proxy 127.0.0.1:9050 --proxy-type socks5 %h %p # ubuntu
  ```
4. Hidden webserver only curl'able from the client authorized (tested using simple Hello World) `curl --socks5-hostname 127.0.0.1:9050 blahblahblahserver.onion`

## DETAILS & GOTCHAS

_assuming Ubuntu 16x_

`sudo apt-get install tor nginx openssh-server`

`sudo nano /etc/ssh/sshd_config` and then change port to the one you use for torrc line in server

`port 91854` 

`AllowUsers root #or whatever`

`PermitRootLogin no # recommended`

`sudo service ssh restart`

_for nginx setup_

`sudo nano /etc/nginx/sites-available/blahblahblahserver.onion` then   

```
server {
listen   127.0.0.1:8099;

root /home/cjer/projects/lol/;
index index.html index.htm;
server_name qhzwfy24i22jchdw.onion;
}
```
then `sudo nginx restart`

if you have issues, `sudo nginx -t` is your best friendz...

_ssh config for OSX_

```
host hidden1
        CheckHostIP no
        Compression yes
        Port 22
        user root # (or whatever you set)
        hostname blahblahblahserver.onion
        proxyCommand nc -x 127.0.0.1:9050 -X 5 %h %p
```

_if nc command doesn't work then `brew install nmap --upgrade`_

_the command `ssh hidden1` should connect you to server and ask for password_

### !!!! DONT FORGET TO SET HARD PASS FOR SSH USER !!!!
