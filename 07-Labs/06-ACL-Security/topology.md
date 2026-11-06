# Topologie — ACL et sécurité

```text
PC-Users VLAN10 --\
PC-Guest VLAN20 --- SW1 == trunk == R1 G0/0 (router-on-a-stick)
Web-SRV VLAN30 ---/                      ACL in sur VLAN20
```

| VLAN | Usage | Sous-réseau | Passerelle |
|---|---|---|---|
| 10 | Users | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Guest | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Servers | 192.168.30.0/24 | 192.168.30.1 |

Web-SRV est `192.168.30.10/24`, passerelle `.1`; Guest PC est `192.168.20.10/24`, passerelle `.1`. Le trunk transporte les VLANs 10,20,30. La règle filtre TCP/80 de Guest vers le serveur; les autres flux invités sont permis par cette ACL, donc adapter la politique avant usage.
