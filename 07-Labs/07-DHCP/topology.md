# Topologie — DHCPv4 avec relais

```text
PC-A -- SW1 -- R1 G0/0       R1 G0/1 -------- R2 G0/0       R2 G0/1 -- SW2 -- PC-B
  LAN-A 192.168.10.0/24       lien 10.0.12.0/30              LAN-B 192.168.20.0/24
                               serveur DHCP R1             relais DHCP R2
```

| Segment | Réseau | Interface/passerelle |
|---|---|---|
| LAN-A | 192.168.10.0/24 | R1 G0/0: 192.168.10.1 |
| R1-R2 | 10.0.12.0/30 | R1 G0/1: .1; R2 G0/0: .2 |
| LAN-B | 192.168.20.0/24 | R2 G0/1: 192.168.20.1 |

PC-A se connecte à SW1 puis R1 G0/0; PC-B se connecte à SW2 puis R2 G0/1. Les switches sont des segments L2 d’accès (VLAN 1 par défaut dans ce scénario). Configurer les deux PCs en DHCP. Les pools excluent `.1` à `.20`; les clients doivent recevoir une adresse dans `.21` à `.254`, un masque /24 et la passerelle locale. L’adresse attribuée exacte dépend du serveur et ne doit pas être présumée.
