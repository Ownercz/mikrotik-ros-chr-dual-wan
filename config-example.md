```
# 2024-02-17 11:04:56 by RouterOS 7.13.4
# software id = 
#
/interface bridge
add arp=proxy-arp name=bridge-core port-cost-mode=short
/interface ethernet
set [ find default-name=ether1 ] comment="LTE uplink" disable-running-check=\
    no mtu=1200 name=ether1-up-lte
set [ find default-name=ether2 ] comment="WIFI uplink" disable-running-check=\
    no name=ether2-up-wifi
set [ find default-name=ether3 ] comment="Core lan" disable-running-check=no \
    name=ether3-core
/interface pppoe-client
add add-default-route=yes default-route-distance=5 disabled=no interface=\
    ether2-up-wifi name=pppoe-wifi user=XXX
/interface list
add name=WAN-WIFI
add name=WAN-LTE
add name=LAN-CORE
add include=WAN-LTE,WAN-WIFI name=WAN
add include=\
    LAN-CORE \
    name=LAN
/interface list member
add interface=ether1-up-lte list=WAN-LTE
add interface=bridge-core list=LAN-CORE
add interface=pppoe-wifi list=WAN-WIFI
/ip dhcp-client
add default-route-distance=10 interface=ether1-up-lte use-peer-dns=no
/ip firewall nat
add action=masquerade chain=srcnat out-interface-list=WAN
/ipv6 dhcp-client
add interface=pppoe-wifi pool-name=ppoe-pool request=address,prefix \
    use-peer-dns=no
add interface=ether1-up-lte pool-name=lte-pool request=prefix use-peer-dns=no
/ipv6 firewall filter
add action=drop chain=input in-interface-list=WAN
```
