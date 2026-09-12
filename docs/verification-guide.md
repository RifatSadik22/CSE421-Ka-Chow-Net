# Packet Tracer verification guide

Run these checks in a working copy of the [saved project](../packet-tracer/Group1808_Ka-Chow_Net.pkt). The original report records successful tests; these steps let you repeat them.

## 1. Interfaces and routing

Open each router's CLI:

```text
enable
show ip interface brief
show ip route
show running-config
```

Compare addresses with the [addressing plan](addressing-plan.md) and commands with the [router references](../configs/README.md). Connected links should be up/up. SS, RBA, and DC should show RIP-learned routes; the other routers should show documented static or default routes.

On the RIP routers, run `show ip protocols`. Confirm RIP version 2, disabled automatic summarization, and the passive interfaces listed in the references.

## 2. DHCP

For every PC, open **Desktop → IP Configuration** and select DHCP. Confirm that its IP is usable in its unit's LAN, its mask and gateway match the addressing plan, and DNS is `11.10.0.2`.

On SS, run:

```text
show ip dhcp pool
show ip dhcp binding
```

For LCT and FVC, inspect **Services → DHCP** on the Server-PT device. Check local and remote pools, gateways, DNS, pool sizes, and that dynamic ranges avoid static device addresses. On RBA, DC, MTY, and WWM, compare `ip helper-address` with the addressing plan.

## 3. Connectivity and services

From SS-PC1's command prompt, test remote gateways:

```text
ping 11.10.2.1
ping 11.10.3.1
ping 11.10.4.1
ping 11.10.5.1
ping 11.10.5.129
ping 11.10.6.1
```

Repeat if the first probe is lost during ARP resolution. Test remote PCs and printers too, to verify LAN endpoints beyond router interfaces.

Inspect **Services → DNS** on SS's DNS server for the exact web hostnames. Use a PC's **Desktop → Web Browser** to visit both websites by DNS name. Test by IP address if separating HTTP reachability from DNS problems.

Using the accounts configured under **Desktop → Email**, send from `sheriff1@sheriff.rs` to `flo1@flo.rs`, receive at FVC, then reply to SS. Check SMTP/POP3 settings and DNS records if delivery fails.

## 4. FVC backup through LCT

On FVC, confirm the primary SS LAN route, then shut down its SS-facing serial interface:

```text
enable
show ip route 11.10.0.0
configure terminal
interface Serial0/1/0
 shutdown
end
show ip route 11.10.0.0
ping 11.10.0.1
traceroute 11.10.0.1
```

The `11.10.0.0/23` route should switch to `11.10.6.66` with administrative distance 5. The reported path passes through LCT and SS's Downtown interface.

Restore the interface and confirm the primary route returns:

```text
configure terminal
interface Serial0/1/0
 no shutdown
end
show ip route 11.10.0.0
```

This checks the SS LAN backup route. Other destinations require separate outage tests.

## 5. LCT backup through DC

The reported test removes the primary route while keeping shared Downtown access operational:

```text
enable
show ip route 11.10.0.0
configure terminal
no ip route 11.10.0.0 255.255.254.0 11.10.6.65
end
show ip route 11.10.0.0
ping 11.10.0.1
traceroute 11.10.0.1
```

The SS LAN route should use DC at `11.10.6.67` with administrative distance 5. Restore the primary afterward:

```text
configure terminal
ip route 11.10.0.0 255.255.254.0 11.10.6.65
end
show ip route 11.10.0.0
```

Shutting down LCT's Downtown interface also removes access to DC and cannot demonstrate this backup. Removing the primary route checks administrative-distance selection; automatic neighbor-failure detection is a separate behavior.

## 6. Record results

Record the device, destination, expected result, actual result, and screenshot for each check. Restore test changes before saving your working copy. Use **Simulation Mode** filters for DHCP, DNS, ICMP, RIP, HTTP, SMTP, and POP3 to inspect each exchange.
