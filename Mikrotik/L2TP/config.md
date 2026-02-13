## MIKROTIK  
```
# 2026-02-12 21:08:30 by RouterOS 7.20.6
/interface bridge add name=Loopback0
/interface ethernet set [ find default-name=ether1 ] disable-running-check=no
/interface ethernet set [ find default-name=ether2 ] disable-running-check=no
/interface ethernet set [ find default-name=ether3 ] disable-running-check=no
/interface ethernet set [ find default-name=ether4 ] disable-running-check=no
/ip pool add name=L2TP_POOL ranges=10.110.30.10-10.110.30.20
/port set 0 name=serial0
/ppp profile add dns-server=8.8.8.8 local-address=192.168.0.6 name=L2TP_PROFILE remote-address=L2TP_POOL use-compression=no use-ipv6=no use-mpls=no
/interface l2tp-server server set enabled=yes use-ipsec=yes
/ip address add address=192.168.0.2/30 comment=EXTERNAL interface=ether1 network=192.168.0.0
/ip address add address=192.168.0.6/30 comment=INTERNAL interface=ether2 network=192.168.0.4
/ip address add address=192.168.200.11/24 interface=Loopback0 network=192.168.200.0
/ip dhcp-client add interface=ether1
/ip route add dst-address=0.0.0.0/0 gateway=192.168.0.1
/ip route add disabled=no distance=1 dst-address=10.0.0.0/8 gateway=192.168.0.5 routing-table=main suppress-hw-offload=no
/ppp secret add  name=noc profile=L2TP_PROFILE service=l2tp
```

## Cisco GW  
```
interface Ethernet0/1.101
 description EXTERNAL
 encapsulation dot1Q 101
 ip address 192.168.0.1 255.255.255.252
 ip nat inside
 ip virtual-reassembly in
!
interface Ethernet0/1.102
 description INTERNAL
 encapsulation dot1Q 102
 ip address 192.168.0.5 255.255.255.252
 ip nat inside
 ip virtual-reassembly in
!
ip access-list extended NAT-ACL
 permit ip 10.0.0.0 0.255.255.255 any
!
ip route 10.110.0.0 255.255.0.0 Null0 name BLACKHOLE
ip route 10.110.30.0 255.255.255.0 192.168.0.6
ip route 192.168.200.11 255.255.255.255 Ethernet0/1.101 192.168.0.2
ip route 0.0.0.0 0.0.0.0 192.168.200.1
!
ip nat inside source route-map NAT-RMAP interface Ethernet0/0 overload
```
