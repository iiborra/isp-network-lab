# ISP network lab

Small service-provider network built with real network OS images (FRR) on Containerlab,
to practise OSPF, BGP, troubleshooting and network automation.

## Status

- [x] Phase 0: two FRR routers linked with Containerlab (10.0.12.0/30), lab deploys and destroys with one command
- [ ] Phase 1: OSPF area 0 (R1-R2-R3), loopbacks, client LAN

## Usage

    sudo containerlab deploy -t lab-redes.clab.yml
    sudo containerlab destroy -t lab-redes.clab.yml
