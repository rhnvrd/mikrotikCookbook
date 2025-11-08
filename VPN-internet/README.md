# VPN Internet Setup

#### Device: MikroTik hAP ac²

#### RouterOS Version: 7.12.2

###### Backup: No password, no encryption

---

## Scenario

#### Goal

Receive internet from a wireless hotspot and share it with local MikroTik users through an L2TP VPN.
All LAN/bridge clients must route traffic **only** through the VPN — no direct internet access — while ensuring a **stable VPN connection**.

---

## Configuration (from scratch via Winbox)

1. **Wireless** → **wlan1**

   * Mode: `station`
   * SSID: `<hotspot SSID>`
   * Password: `<hotspot password>`
   * Enable interface

2. **IP → DHCP Client → +**

   * Interface: `wlan1`
   * *Add Default Route*: **NO**

3. **Bridge → +**

   * Create `bridge1`

4. **Bridge → Ports → +**

   * Add LAN ports to `bridge1`

5. **IP → Addresses → +**

   * Address: `192.168.88.1/24`
   * Interface: `bridge1`

6. **PPP → Interfaces → + L2TP Client**

   * Enter your VPN credentials/configuration
   * *Add Default Route*: **NO**

7. **IP → Firewall → NAT → +**

   * Chain: `srcnat`
   * Action: `masquerade`

8. **Routing → Tables → +**

   * Name: `vpn`
   * *FIB*: **Enabled**

9. **IP → Firewall → Mangle → +**

   * Chain: `prerouting`
   * In. Interface: `bridge1`
   * Action: `mark routing`
   * New Routing Mark: `vpn`

10. **IP → Routes → +**

    * Dst. Address: `0.0.0.0/0`
    * Gateway: `l2tp-out1`
    * Routing Table: `vpn`

11. **IP → Routes → +**

    * Dst. Address: `0.0.0.0/0`
    * Gateway: `<hotspot gateway IP>` (e.g. `172.20.10.1`)
    * Routing Table: `main`
