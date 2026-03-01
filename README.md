![Topology Diagram](https://github.com/furkangurses/Layer-2-Layer-3-EtherChannel-Implementation-in-Enterprise-Switching/blob/main/42356.PNG?raw=true)

# Enterprise EtherChannel Configuration Lab (Layer 2 & Layer 3)

---

## 🎯 Lab Objective

The objective of this lab is to:

- Eliminate blocked redundant links caused by STP
- Configure Layer 2 EtherChannel between Access and Distribution switches
- Configure Layer 3 EtherChannel between Distribution switches
- Implement LACP, PAgP, and Static EtherChannel
- Enable inter-switch routing with static routes
- Modify EtherChannel load-balancing method

This lab demonstrates how to increase bandwidth and maintain redundancy in an enterprise network.

---

## 🌐 Topology Overview

The topology consists of:

- **Access Layer:** ASW1, ASW2
- **Distribution Layer:** DSW1, DSW2
- Redundant uplinks between:
  - ASW1 ↔ DSW1 (LACP)
  - ASW2 ↔ DSW2 (PAgP)
  - DSW1 ↔ DSW2 (Layer 3 Static EtherChannel)

Key networks:

- DSW1–DSW2 L3 link: `10.0.0.0/30`
- User network: `172.16.1.0/24`
- Server network (SRV1): `172.16.2.0/24`

Initially, STP blocks one redundant link. EtherChannel aggregates physical links into a single logical interface (Port-Channel), allowing both links to forward traffic.

---

## ⚙️ Configuration Steps

### 1️⃣ Configure Layer 2 EtherChannel (LACP) – ASW1 ↔ DSW1

- Bundle G0/1–G0/2 into Port-Channel 1
- Use LACP (active mode)
- Configure trunk on Port-Channel

---

### 2️⃣ Configure Layer 2 EtherChannel (PAgP) – ASW2 ↔ DSW2

- Bundle G0/1–G0/2 into Port-Channel 1
- Use PAgP (desirable mode)
- Configure trunk on Port-Channel

---

### 3️⃣ Configure Layer 3 EtherChannel – DSW1 ↔ DSW2

- Convert interfaces to routed ports (`no switchport`)
- Create Port-Channel 2 (static mode)
- Assign IP addresses
- Verify connectivity with ping

---

### 4️⃣ Enable Routing & Configure Static Routes

- Enable `ip routing`
- Configure static routes for remote networks
- Verify with `show ip route`

---

### 5️⃣ Modify Load-Balancing Method

- Verify default (source MAC)
- Change to `src-dst-ip`
- Confirm configuration

---

## 📝 Commands Used

```bash
! Verify STP
show spanning-tree

! Configure L2 EtherChannel (LACP)
interface range g0/1 - 2
 channel-group 1 mode active
exit
interface port-channel 1
 switchport mode trunk

! Configure trunk encapsulation (if required)
switchport trunk encapsulation dot1q

! Verify EtherChannel
show etherchannel summary
show interfaces trunk

! Configure L2 EtherChannel (PAgP)
interface range g0/1 - 2
 channel-group 1 mode desirable

! Configure L3 EtherChannel (Static)
interface range g1/0/1 - 2
 no switchport
 channel-group 2 mode on
exit
interface port-channel 2
 ip address 10.0.0.1 255.255.255.252

! Enable routing
ip routing

! Configure static routes
ip route 172.16.2.0 255.255.255.0 10.0.0.2
ip route 172.16.1.0 255.255.255.0 10.0.0.1

! Verify routing
show ip route
ping 172.16.2.1

! Verify and modify load balancing
show etherchannel load-balance
port-channel load-balance src-dst-ip

```

## 🌍 Real-World Use Case

This configuration is commonly used in:

- Access-to-Distribution uplinks in enterprise campus networks
- Core switch interconnections
- Server farm connections
- High-bandwidth backbone links
- Redundant uplinks requiring both redundancy and increased throughput

Without EtherChannel:
- STP blocks redundant links
- Bandwidth is wasted

With EtherChannel:
- Full bandwidth utilization
- Redundancy maintained
- Simplified logical topology


## 🛠️ Skills Gained

- Layer 2 EtherChannel configuration (LACP, PAgP)
- Layer 3 EtherChannel configuration
- Static routing on multilayer switches
- STP behavior analysis
- EtherChannel troubleshooting
- Load-balancing optimization
- Enterprise uplink design principles


## ⚡ Possible Improvements

- Replace static routing with OSPF or EIGRP
- Implement HSRP between DSW1 and DSW2
- Add VLAN segmentation
- Configure EtherChannel consistency checks
- Monitor traffic distribution with real traffic flows


## 🧩 Troubleshooting Notes

If EtherChannel does not form:

- Ensure both sides use compatible modes (active/passive, desirable/auto)
- Check VLAN and trunk configuration consistency
- Verify speed/duplex match
- Confirm encapsulation type (dot1q vs ISL)
- Look for flags in `show etherchannel summary`:
  - SU → Layer 2 and Up
  - RU → Layer 3 and Up
  - P  → Properly bundled
  - I  → Individual (not bundled)

If routing fails:

- Confirm `ip routing` is enabled
- Verify static routes
- Check ARP resolution
- Test with ping from Port-Channel interface
