# 🧪 Homelab: Networking and Linux

> **Status:** structure (v0, nothing built yet) · **Location:** `cyber-lab/homelab/`

---

## 🎯 What it is

An isolated physical lab (no internet, no link to other networks) to practice **networking** (VLANs, routing, packet capture) and **Linux**. A Cisco switch routes between zones, a Linux server hosts the services, and a laptop captures traffic with Wireshark.

## 🚫 Scope

| In | Out (for now) |
|---|---|
| VLANs and inter-VLAN routing | Blue Team, SOC, SIEM |
| Linux services (DHCP, DNS, HTTP/TLS) | Windows |
| Packet capture and analysis | Active pentesting |
| Reproducible documentation | OSPF and T14 VMs |

---

## 🧱 Parts

| Part | Role |
|---|---|
| Cisco SG250-10P | L3 core (VLANs, static routes, port mirroring) |
| ThinkPad T440 (Linux) | Services server |
| Mercusys MW301R | Simulated edge ("fake internet") |
| MacBook M1 | Analyzer (Wireshark) on the mirrored port |
| ThinkPad T14 *(future)* | VMs |

## 🗺️ Topology

```
        (isolated: no internet, no link to the home network)

 [MW301R edge] ──VLAN 2 (10.10.0.0/30)── [SG250 L3 core]
      .2                                       .1 │    │
                                                  │    └─ SPAN port (mirrors VLAN 20) ──► [Mac: Wireshark]
                                                  └─ trunk (VLANs 10/20/30) ──► [T440: Linux server]
                                                        (future: trunk ──► [T14: VMs])
```

## 🔢 Zones

**Rule:** the VLAN ID is the third octet of the IP (VLAN 20 → `10.10.20.0/24`).

| VLAN | Name | Subnet | Use | State |
|---|---|---|---|---|
| 2 | TRANSIT | 10.10.0.0/30 | SG250 (.1) ↔ MW301R WAN (.2) | active |
| 10 | MGMT | 10.10.10.0/24 | Switch management and SSH to the server | active |
| 20 | SERVERS | 10.10.20.0/24 | Linux services | active |
| 30 | CLIENTS | 10.10.30.0/24 | Test hosts | active |
| 40 | ATTACK | 10.10.40.0/24 | Future pentesting | 🔒 reserved |
| 50 | TARGETS | 10.10.50.0/24 | Future vulnerable targets | 🔒 reserved |
| — | SPAN | no IP | Mirroring destination → analyzer | active |

## 🧭 Design choices

- Inter-VLAN routing lives on the switch (static L3).
- The edge router sits in its own transit VLAN.
- VLAN 20 is mirrored to the analyzer; trunk mirroring comes in v2.
- Zones 40 and 50 are reserved and unused.
- Priority: Linux and networking.

## 📎 References

- [Cisco 250 Series administration guide](https://cisco.com/go/cbs250ag)
- [Cisco 250 Series datasheet](https://www.cisco.com/c/en/us/products/collateral/switches/250-series-smart-switches/datasheet-c78-737061.html)
