# ISP network lab

Small service-provider network built with real network OS images (FRR) on Containerlab,
to practise OSPF, BGP, troubleshooting and network automation.

## Status

- [x] Phase 0: two FRR routers linked with Containerlab, lab deploys and destroys with one command
- [x] Phase 1: OSPF area 0 (R1-R2-R3), loopbacks, client LAN
- [ ] Phase 2: eBGP between R3 (AS 65001) and R4 (AS 65002)

## Topology (phase 1)

```
pc-a ── R1 ──────── R2 ──────── R3
      eth2   eth1  eth1  eth2  eth1
```

| Segment | Prefix | Addresses |
|---|---|---|
| R1-R2 | 10.0.12.0/31 | R1 .0, R2 .1 |
| R2-R3 | 10.0.23.0/31 | R2 .0, R3 .1 |
| Loopbacks | 10.0.0.X/32 | R1 .1, R2 .2, R3 .3 (also OSPF router-id) |
| LAN-A | 192.168.8.0/24 | R1 .1 (gateway), pc-a .2 |
| Management | 172.20.20.0/24 | eth0 on every node, set by Containerlab |

## Design decisions

- **/31 point-to-point links** (RFC 3021): no network or broadcast address to waste.
- **OSPF point-to-point network type** on router links: no DR/BDR election and no type 2 (Network) LSA.
- **Passive interfaces** on loopbacks and LAN-A: the prefixes are advertised, but no hellos are
  sent, so no host on the LAN can form an adjacency, read the LSDB or inject routes.
- **Management kept out of OSPF and without a default route**: eth0 is only for access to the
  nodes. Removing the Containerlab default route makes a missing route fail with
  `Network unreachable` instead of leaking traffic through the management network.

## Verification

    docker exec clab-lab-redes-router2 vtysh -c "show ip ospf neighbor"   # 2 neighbors, Full/-
    docker exec clab-lab-redes-pc-a ping -c 5 10.0.0.3                    # 5/5, 0% loss
    docker exec clab-lab-redes-pc-a traceroute 10.0.0.3                   # 192.168.8.1, 10.0.12.1, 10.0.0.3

## Usage

    sudo containerlab deploy -t lab-redes.clab.yml
    sudo containerlab destroy -t lab-redes.clab.yml
