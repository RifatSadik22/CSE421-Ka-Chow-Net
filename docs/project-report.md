# Ka-Chow Net: The Radiator Springs Network

**CSE421 Computer Networks · Group 1808 · Section 18**

This edited version corrects section numbering and prose and moves IP tables into a readable [addressing plan](addressing-plan.md). The [original PDF](project-report.pdf) retains the screenshots and evidence. Results below describe that report's findings.

## 1. Introduction

Ka-Chow Net connects Sheriff Station (SS), Luigi Casa-Della Tires (LCT), Doc-Hudson Clinic (DC), Flo V8 Café (FVC), Ramone Body Art (RBA), Mater Tow Yard (MTY), and Wheel Well Motel (WWM).

It uses VLSM, RIPv2, static routing, recursive and floating backup routes, DHCP and relay, DNS, HTTP, email, and network printers to support communication between units.

## 2. Network topology

![Complete network topology](images/network-topology.jpg)

Each unit has one router, one LAN switch, two PCs, and one printer. A separate Downtown switch connects SS, LCT, DC, FVC, and MTY. SS, RBA, and DC form the serial Gossip Loop. Additional serial links connect SS to FVC and FVC to WWM.

## 3. VLSM and IP addressing

The first group member's ID, `22201110`, produces `11.10.0.0/16` by splitting its last four digits into `11` and `10`.

LANs follow the required allocation order SS, DC, FVC, LCT, RBA, MTY, and WWM. SS uses `/23`; DC, FVC, and LCT use `/24`; RBA and MTY use `/25`; WWM uses `/26`. Downtown uses `/29`, and the five serial links use `/30`.

The [addressing plan](addressing-plan.md) includes complete subnet, usable-range, broadcast, capacity, interface, server, and gateway tables. The uploaded diagram shows allocation order rather than subnet containment.

## 4. Routing

SS, RBA, and DC run RIPv2 with automatic summarization disabled and LAN interfaces passive. SS and DC also keep Downtown passive; SS keeps the FVC serial interface passive. SS redistributes static routes with RIP metric 1 so that the Gossip Loop learns routes outside the loop.

LCT and MTY use specific next-hop routes. FVC combines exit-interface routes with a higher-distance next-hop backup for SS. WWM uses a default route through FVC at `11.10.6.89`. The [router references](../configs/README.md) reproduce Appendix A's commands.

## 5. DHCP and DHCP relay

| Client LANs | DHCP provider | Relay targets |
| --- | --- | --- |
| SS, RBA, DC | SS router | RBA: `11.10.6.73`; DC: `11.10.6.81` |
| LCT, MTY | LCT server `11.10.4.2` | MTY: `11.10.4.2` |
| FVC, WWM | FVC server `11.10.3.2` | WWM: `11.10.3.2` |

PCs obtain IP settings through DHCP, including DNS `11.10.0.2`. Servers, printers, and router interfaces use static addresses. The report includes DHCP screenshots for SS, MTY, and WWM.

## 6. DNS, web, and email services

SS hosts central DNS at `11.10.0.2`. Records identify the two web servers and seven local mail servers. SS's web server is `11.10.0.3`; FVC's is `11.10.3.3`. HTTP is enabled on both web servers, and SMTP/POP3 are enabled on the local mail servers.

The report documents access to FVC's website by DNS name. Read exact DNS hostnames from the DNS server's Services panel in the saved project.

## 7. Cross-domain email

SS uses `sheriff.rs`, and FVC uses `flo.rs`. The report shows a message from `sheriff1@sheriff.rs` received by `flo1@flo.rs` and a reply received at SS. It explains that an older visible mail-delivery failure came from an earlier configuration attempt.

## 8. FVC recursive backup route

FVC normally reaches the SS LAN through `Serial0/1/0` with administrative distance 1. Its backup next-hop route uses LCT at `11.10.6.66` with administrative distance 5.

The report describes shutting down the direct FVC–SS link, observing the backup route activate, and checking ping and traceroute. The recorded path passes through LCT (`11.10.6.66`) and SS's Downtown interface (`11.10.6.65`). This demonstrates backup connectivity to the SS LAN; other destinations need separate outage tests.

## 9. LCT floating static route

LCT's primary SS LAN route uses `11.10.6.65` with administrative distance 1. The floating backup uses DC at `11.10.6.67` with administrative distance 5.

The reported test temporarily removes the primary static route. The backup becomes active and a ping to the SS gateway succeeds. The primary route is then restored. Route removal tests route selection; it does not demonstrate automatic neighbor-failure detection on shared Ethernet.

## 10. Final testing and conclusion

The report includes successful SS-PC1 pings to DC, FVC, LCT, RBA, MTY, and WWM, plus DHCP, DNS/web, cross-domain email, and backup-path evidence.

The project demonstrates inter-unit communication, automatic client addressing, central name resolution, application services, and two documented backup routes. Follow the [verification guide](verification-guide.md) to repeat the checks. Runtime results were not independently reproduced during repository cleanup.

## Appendix: Original materials

- [Original PDF report and screenshots](project-report.pdf)
- [Complete addressing tables](addressing-plan.md)
- [Router configuration references](../configs/README.md)
- [VLSM allocation diagram](images/vlsm-allocation-tree.png)
- [Packet Tracer project](../packet-tracer/Group1808_Ka-Chow_Net.pkt)
