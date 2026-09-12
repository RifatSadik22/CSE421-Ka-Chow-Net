# Router configuration references

These files are transcribed from **Appendix A: Equivalent Router Configuration Commands** in the [uploaded report](../docs/project-report.pdf). They are references, not fresh `show running-config` exports from the `.pkt` file. The report's command choices are preserved.

| Router | Configuration |
| --- | --- |
| Sheriff Station | [SS.cfg](SS.cfg) |
| Ramone Body Art | [RBA.cfg](RBA.cfg) |
| Doc-Hudson Clinic | [DC.cfg](DC.cfg) |
| Flo V8 Café | [FVC.cfg](FVC.cfg) |
| Luigi Casa-Della Tires | [LCT.cfg](LCT.cfg) |
| Mater Tow Yard | [MTY.cfg](MTY.cfg) |
| Wheel Well Motel | [WWM.cfg](WWM.cfg) |

Compare each saved router using `show running-config`. If entering reference commands manually, start with `enable` and `configure terminal`, then finish with `end` and `copy running-config startup-config`.

Interface names and DCE clock settings follow the uploaded topology. Check `show controllers serial` before applying `clock rate` to a rebuilt topology. Server-PT DHCP pools, DNS records, HTTP content, email accounts, and PC settings use device interfaces and are not included here.

FVC's exit-interface routes on the shared Downtown Ethernet segment are reproduced as reported. Their operation depends on neighbor behavior, including proxy ARP. Verify these references in the supplied simulation before reusing them in another network.
