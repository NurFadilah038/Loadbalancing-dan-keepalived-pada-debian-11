# Loadbalancing-dan-keepalived-pada-debian-11

Loadbalancing
-------------------
Master node :
#nano /etc/haproxy/haproxy.cfg
Edit: master dan web-master
#Systemctl restart haproxy

Client node:
#cd /var/www/html
#nano index.html
Edit : frontend
#systemctl restart apache2


Keepalived
---------------
# apt install keepalived
# nano /etc/keepalived/keepalived.conf
Edit : Master LB
global_defs {
  # Keepalived process identifier
  router_id haproxy2
}
vrrp_script haproxy {
   script "/usr/bin/kill -0 haproxy"
  interval 2
  weight 2
}
# Virtual interface - The priority specifies the order in which the assigned interface to take over in a failover
vrrp_instance VI_1 {
  state MASTER
  interface eth0
  virtual_router_id 51
  priority 255
  advert_int 1
# The virtual ip address shared between the two NGINX Web Server which will float
  virtual_ipaddress {
    10.2.15.69/24 dev eth0
  }
  track_script {
    haproxy
  }
  authentication {
    auth_type AH
    auth_pass secret
  }
}
-----
#systemctl restart keepalived haproxy
Edit : Backup LB
global_defs {
  # Keepalived process identifier
  router_id haproxy2
}
vrrp_script haproxy {
   script "/usr/bin/kill -0 haproxy"
  interval 2
  weight 2
}
# Virtual interface - The priority specifies the order in which the assigned interface to take over in a failover
vrrp_instance VI_1 {
  state MASTER
  interface eth0
  virtual_router_id 51
  priority 254
  advert_int 1
# The virtual ip address shared between the two NGINX Web Server which will float
  virtual_ipaddress {
    10.2.15.69/24 dev eth0
  }
  track_script {
    haproxy
  }
  authentication {
    auth_type AH
    auth_pass secret
  }
}
----
#systemctl restart keepalived haproxy
