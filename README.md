********************************LOADBALANCING WEB SERVER USING HAPROXY LOADBALANCER********************************

OBJECTIVE:
  - Curl of your Load Balancer should return a “Hello World” that is being served by one of your web servers.
    Removal of either one of your web servers should automatically fail over to the other server.

==================================================================

RESOURCES USED:
  - AWS EC2 INSTANCE (2 Web Servers, 1 HAProxy Server)

==================================================================

HAPROXY:  
  - HAProxy is a Open source, It is very fast and reliable solution offering high availability, load balancing, and proxying for TCP and HTTP-based applications.
  
==================================================================

EC2 INSTANCE:
  - An EC2 Instance is a virtual server is Amazon's elastic compute cloud (EC2) to run the application on the Amazon web services (AWS) infrastructure.
  
==================================================================

### RESULT:
    - Host webserver1 IP in browser or curl
    IP: 54.210.97.214
  
    ubuntu@webserver1:~$ curl http://localhost
    "Hello World 1"

    ubuntu@webserver1:~$ curl http://localhost
    "Hello World 1"

    ubuntu@webserver1:~$ curl http://localhost
    "Hello World 1"

    - Host webserver1 IP in browser or curl
    IP: 3.88.110.110
  
    ubuntu@webserver2:~$ curl http://localhost
    "Hello World 2"

    ubuntu@webserver2:~$ curl http://localhost
    "Hello World 2"

    ubuntu@webserver2:~$ curl http://localhost
    "Hello World 2"

    - Host Load Balance IP in browser or curl
    IP: 54.163.53.130
   
    ubuntu@HAProxysever:~$ curl http://localhost
    "Hello World 2"

    ubuntu@HAProxysever:~$ curl http://localhost
    "Hello World 1"

    ubuntu@HAProxysever:~$ curl http://localhost
    "Hello World 2"

    ubuntu@HAProxysever:~$ curl http://localhost | grep Hello
  
     % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
    100    16  100    16    0     0   1600      0 --:--:-- --:--:-- --:--:--  1600

    "Hello World 2"


    - HAProxy Server: http://54.163.53.130:8181/

    ID: HAPAdmin

    PASS: AdminPass

====================================================================

CONFIGURATION:

  - Webserver1 has hosted with below configuration using AWS EC2 instance
      - OS: Ubuntu
      - Webserver: Apache2 
      - IP: 54.210.97.214
 
### Commnets: 
      - sudo apt-get update
      - sudo apt-get install Apache2
      - apache2 -v
              Server version: Apache/2.4.41 (Ubuntu)
      - cd /var
              backups  cache  crash  lib  local  lock  log  mail  opt  run  snap  spool  tmp  www
      - cd /www
              html
      - cd /html
              index.html
      - systemctl status apache2.service
      
    ● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2020-11-29 20:18:08 UTC; 3 days ago
       Docs: https://httpd.apache.org/docs/2.4/
    Process: 45906 ExecReload=/usr/sbin/apachectl graceful (code=exited, status=0/SUCCESS)
     Main PID: 27269 (apache2)
      Tasks: 55 (limit: 1164)
     Memory: 8.0M
     CGroup: /system.slice/apache2.service
             ├─27269 /usr/sbin/apache2 -k start
             ├─45910 /usr/sbin/apache2 -k start
             └─45911 /usr/sbin/apache2 -k start

     Nov 29 20:18:08 ip-172-31-21-8 systemd[1]: Starting The Apache HTTP Server...
     Nov 29 20:18:08 ip-172-31-21-8 systemd[1]: Started The Apache HTTP Server.
     Dec 01 00:00:15 ip-172-31-21-8 systemd[1]: Reloading The Apache HTTP Server.
     Dec 01 00:00:15 ip-172-31-21-8 systemd[1]: Reloaded The Apache HTTP Server.
     Dec 02 00:00:00 ip-172-31-21-8 systemd[1]: Reloading The Apache HTTP Server.
     Dec 02 00:00:01 ip-172-31-21-8 systemd[1]: Reloaded The Apache HTTP Server.
     Dec 03 00:00:00 ip-172-31-21-8 systemd[1]: Reloading The Apache HTTP Server.
     Dec 03 00:00:01 ip-172-31-21-8 systemd[1]: Reloaded The Apache HTTP Server.
  
      - ubuntu@webserver1:/var/www/html$ sudo vi index.html
      - We will get access to edit the default index.html file.
      - i
      - Now We can right our own web page. 
            "Hello World 1"
      - :wq (to save and quit the edit)
      - curl http://localhost
            "Hello World 1"

Same steps has to be follow for the Webserver2 Installation.   
  - Webserver2 has hosted with below config using AWS EC2 instance
      - OS: Ubuntu
      - Webserver: Apache2 
      - IP: 3.88.110.110
      
### Commnets:      
      - systemctl status apache2.service (check the apache server status)
      - ubuntu@webserver2:/var/www/html$ sudo vi index.html
      - We will get access to edit the default index.html file.
      - i
      - Now We can right our own web page. 
            "Hello World 2"
      - :wq (to save and quit the edit)
      - curl http://localhost
            "Hello World 2"
      
  - Loadbalaceserver has hosted with below config using AWS EC2 instance
      - OS: Ubuntu
      - LBserver: HAProxy V1.8.27 
      - IP: 54.163.53.130
      
### Comments: 
      - sudo apt-get update
      - sudo add-apt-repository ppa:vbernat/haproxy-1.7
      - sudo apt install -y haproxy
      - haproxy -v
      - HA-Proxy version 1.8.27-1ppa1~bionic 2020/11/08
        Copyright 2000-2020 Willy Tarreau <willy@haproxy.org>
      - sudo vi /etc/haproxy/haproxy.cfg
      
      - global
        log /dev/log    local0
        log /dev/log    local1 notice
        chroot /var/lib/haproxy
        stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
        stats timeout 30s
        user haproxy
        group haproxy
        daemon

        # Default SSL material locations
        ca-base /etc/ssl/certs
        crt-base /etc/ssl/private

        # Default ciphers to use on SSL-enabled listening sockets.
        # For more information, see ciphers(1SSL). This list is from:
        #  https://hynek.me/articles/hardening-your-web-servers-ssl-ciphers/
        # An alternative list with additional directives can be obtained from
        #  https://mozilla.github.io/server-side-tls/ssl-config-generator/?server=haproxy
        ssl-default-bind-ciphers ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:RSA+AESGCM:RSA+AES:!aNULL:!MD5:!DSS
        ssl-default-bind-options no-sslv3

        defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
        errorfile 400 /etc/haproxy/errors/400.http
        errorfile 403 /etc/haproxy/errors/403.http
        errorfile 408 /etc/haproxy/errors/408.http
        errorfile 500 /etc/haproxy/errors/500.http
        errorfile 502 /etc/haproxy/errors/502.http
        errorfile 503 /etc/haproxy/errors/503.http
        errorfile 504 /etc/haproxy/errors/504.http

        frontend http_front
        bind *:80
        stats uri /haproxy?stats
        default_backend http_back

        backend http_back
        balance roundrobin
        server webserver1 54.210.97.214:80 check
        server webserver2 3.88.110.110:80 check

        listen stats
        bind *:8181
        stats enable
        stats uri /
        stats realm Haproxy\ Statistics
        stats auth HAPAdmin:AdminPass
      - :wq
      - sudo systemctl restart haproxy 
========================================================================================================================================================================

      
      
    
    
