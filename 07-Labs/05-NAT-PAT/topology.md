# Topologie — NAT/PAT

```text
PC-A -- SW1 -- R1 G0/0 (inside)
                 R1 G0/1 (outside) -- ISP G0/0 -- ISP G0/1 -- Server
```

| Segment | Prefix | Addresses |
|---|---|---|
| LAN | 192.168.10.0/24 | R1 .1, PC-A .10 |
| R1-ISP | 203.0.113.0/30 | ISP .1, R1 .2 |
| External test LAN | 198.51.100.0/24 | ISP .1, server .10 |

PC-A gateway is `192.168.10.1`; server gateway is `198.51.100.1`. These TEST-NET ranges are for documentation and isolated labs only.
