# Topologie — Lab 01

```text
PC-A -- SW1 -- R1 ===== R2 -- SW2 -- PC-B
        LAN A /24     WAN /30       LAN B /24
```

| Segment | Réseau | Passerelle / adresse |
|---|---|---|
| LAN A | 192.168.10.0/24 | R1 G0/0 .1; PC-A .10 |
| WAN | 10.0.0.0/30 | R1 G0/1 .1; R2 G0/1 .2 |
| LAN B | 192.168.20.0/24 | R2 G0/0 .1; PC-B .10 |

PC-A connecte SW1 F0/1, SW1 F0/24 à R1 G0/0; PC-B et SW2 idem. R1/R2 sont liés par G0/1. Les interfaces peuvent porter d’autres noms selon le modèle.
