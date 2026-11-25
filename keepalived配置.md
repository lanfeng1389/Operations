# keepalived配置

```javascript
! Configuration File for keepalived
global_defs {
  notification_email {
    aaa@qq.com
  }
  notification_email_from root@localhost
  smtp_server 127.0.0.1
  smtp_connect_timeout 30
  router_id LVS_DEVEL
}
vrrp_script chk_haproxy {
   script "/etc/keepalived/chk-haproxy.sh"
   interval 2
   weight 2
   fall 3
}
vrrp_instance VI_1 {
   state MASTER  //备节点设置为BACKUP
   interface eth0  //网卡设备接口名称
   mcast_src_ip 172.16.61.138  //主备调换
   unicast_peer {
      172.16.61.55  //主备调换
}
virtual_router_id 51
priority 100  //备节点设置比这个值小即可
advert_int 1
authentication {
auth_type PASS
auth_pass 1111
}
track_script {
chk_haproxy
}
virtual_ipaddress {
172.16.62.170
}
}
```
```javascript
! Configuration File for keepalived
global_defs {
  notification_email {
    aaa@qq.com
  }
  notification_email_from root@localhost
  smtp_server 127.0.0.1
  smtp_connect_timeout 30
  router_id LVS_DEVEL
}
vrrp_script chk_haproxy {
   script "/etc/keepalived/chk-haproxy.sh"
   interval 2
   weight 2
   fall 3
}
vrrp_instance VI_1 {
   state BACKUP  //备节点设置为BACKUP
   interface eth0  //网卡设备接口名称
   mcast_src_ip 172.16.61.55  //主备调换
   unicast_peer {
       172.16.61.138  //主备调换
}
virtual_router_id 51
priority 90//备节点设置比这个值小即可
advert_int 1
authentication {
auth_type PASS
auth_pass 1111
}
track_script {
chk_haproxy
}
virtual_ipaddress {
172.16.62.170
}
}
```