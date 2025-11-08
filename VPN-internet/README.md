# VPN-internet

#### Device : Mikrotik HAP ac^2

#### RouterOS version : 7.12.2

###### backup no password and encryption

---

## Scenario

#### Receiving internet from a wireless hotspot and sharing it with MikroTik users via VPN

> We want a MikroTik router that receives internet from a hotspot and routes all bridge clients through an L2TP VPN, with no direct internet access from the LAN, while maintaining a stable VPN connection.

## Configuration via winbox (from scratch) (HAP ac^2)

1. wireless -> wlan1 -> mode:station --- user:pass (from your hotspot) -> enable

2. IP -> DHCP client -> + -> interface:wlan1 -> Add default route (NO)

3. Bridge -> + create

4. Bridge -> Ports -> add ports you want to have internet

5. IP -> Addresses -> + -> Address:192.168.88.1/24 --- interface:bridge1

6. PPP -> + L2TP client -> enter your vpn configuration -> Add default route (NO)

7. IP -> Firewall -> NAT -> preroute --- Chain:srcnat --- Action:masquerade

8. Routing -> Tables -> + -> name:vpn --- FIB enable

9. IP -> Firewall -> Mangle -> + -> Chain:prerouting --- In. interface:bridge1 Action:MarkRouting --- newRoutingMark:vpn

10. IP -> Routes -> + -> Dst. Address:0.0.0.0/0 --- Gateway:l2tp-out1 --- RoutingTable:vpn

11. IP -> Routes -> + -> Dst. Address:0.0.0.0/0 --- Gateway:<your wlan1 ip>172.20.10.1 --- RoutingTable:main
