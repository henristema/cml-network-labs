# EIGRP ↔ OSPF Redistribution Lab (CML)

## Goal
Connect an EIGRP AS 100 domain (R1–R3) and an OSPF Area 0 domain (R3–R5)
through mutual redistribution on R3, and verify full reachability.

## Topology
![topology](topology.png)
Links: 192.168.12.0/24, 192.168.23.0/24, 192.168.34.0/24, 192.168.45.0/24.
Loopbacks: R1–R5 use 1.1.1.1–5.5.5.5 /32.

## Design decisions
- Single redistribution point (R3) to avoid loops.
- Loopbacks brought in with `redistribute connected` on [R1, R2, R3].
- Seed metrics: OSPF→EIGRP delay 1, connected→EIGRP delay 10 [reason].

## Verification
- Before: [outputs]. Each side only knows its own routes.
- After: R1 sees D EX routes for 3.3.3.3–5.5.5.5; R5 sees O E2 routes for 1.1.1.1, 2.2.2.2.
- Pings with `source loopback0` between R1/R5 succeed.

## Problems and lessons
1. 3.3.3.3 was missing on R1 after redistribution. Cause: `redistribute ospf`
   only takes routes installed by OSPF; R3's loopback is a connected route.
   Fix: add `redistribute connected` under EIGRP.
2. Metric difference of 2304 between routes explained by seed delay (9 × 256).

## Configs
See `configs/`.