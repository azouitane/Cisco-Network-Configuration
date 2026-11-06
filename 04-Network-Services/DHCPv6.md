### 🔹 **Configuration DHCPv6 sur un Routeur Cisco**  

**DHCPv6** est utilisé pour attribuer dynamiquement des adresses IPv6 et d'autres informations réseau aux clients. Il existe deux modes de fonctionnement :  

1. **DHCPv6 Stateful** : Le serveur attribue complètement l’adresse IPv6 et d’autres paramètres (comme en IPv4).  
2. **DHCPv6 Stateless** : Le client génère son adresse via **SLAAC**, mais récupère d'autres paramètres (ex: DNS) depuis le serveur DHCPv6.  

---

## **📌 1. Configuration d’un Serveur DHCPv6 (Stateful)**
Dans ce mode, le serveur DHCPv6 assigne **entièrement** une adresse IPv6 aux clients.

### **📍 Configuration du Routeur en DHCPv6 Stateful**
```bash
R1# configure terminal
R1(config)# ipv6 dhcp pool DHCPV6_STATEFUL  # Création du pool DHCPv6
R1(config-dhcp)# address prefix 2001:DB8:1::/64  # Préfixe des adresses à distribuer
R1(config-dhcp)# dns-server 2001:4860:4860::8888  # Serveur DNS (Google IPv6)
R1(config-dhcp)# domain-name exemple.local
R1(config-dhcp)# exit

R1(config)# interface GigabitEthernet0/0
R1(config-if)# ipv6 address 2001:DB8:1::1/64  # Adresse statique du routeur
R1(config-if)# ipv6 dhcp server DHCPV6_STATEFUL  # Associer le pool DHCP à l'interface
R1(config-if)# ipv6 nd managed-config-flag  # Indique aux clients d'utiliser DHCPv6 pour l'adresse
R1(config-if)# ipv6 nd other-config-flag    # DHCPv6 fournit aussi des paramètres (DNS, etc.)
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# ipv6 unicast-routing  # Activation du routage IPv6
R1(config)# end
R1# write memory
```

✅ Le serveur stateful peut attribuer des adresses et paramètres, mais les hôtes doivent prendre en charge DHCPv6 et recevoir des RA appropriés. Le bit M indique l’usage de DHCPv6 pour les adresses; le bit O indique les paramètres « autres ». Ne configurez pas le bit O comme preuve d’allocation d’adresse.

---

## **📌 2. Configuration d’un Serveur DHCPv6 (Stateless)**
Dans ce mode, les clients génèrent leur propre adresse IPv6 avec **SLAAC**, mais utilisent DHCPv6 pour d'autres informations (ex: DNS).

### **📍 Configuration du Routeur en DHCPv6 Stateless**
```bash
R2# configure terminal
R2(config)# ipv6 dhcp pool DHCPV6_STATELESS
R2(config-dhcp)# dns-server 2001:4860:4860::8888  # Serveur DNS
R2(config-dhcp)# domain-name exemple.local
R2(config-dhcp)# exit

R2(config)# interface GigabitEthernet0/1
R2(config-if)# ipv6 address 2001:DB8:2::1/64  # Adresse statique du routeur
R2(config-if)# ipv6 dhcp server DHCPV6_STATELESS  # Associer le pool DHCP à l'interface
R2(config-if)# ipv6 nd other-config-flag  # Clients utilisent DHCPv6 uniquement pour d'autres paramètres
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# ipv6 unicast-routing
R2(config)# end
R2# write memory
```

✅ **Les clients obtiendront leur adresse IPv6 via SLAAC mais utiliseront DHCPv6 pour récupérer les informations DNS.**

---

## **📌 3. Configuration d’un Client DHCPv6**
Un client (routeur ou hôte) doit être configuré pour utiliser DHCPv6.

### **📍 Configuration d’un Client DHCPv6 (Stateful)**
Si le client doit obtenir une **adresse IPv6 complète** du serveur DHCPv6 :
```bash
Client# configure terminal
Client(config)# interface GigabitEthernet0/0
Client(config-if)# ipv6 address dhcp  # Demande une adresse IPv6 au serveur DHCPv6
Client(config-if)# no shutdown
Client(config-if)# exit
Client(config)# end
Client# write memory
```

### **📍 Configuration d’un Client DHCPv6 (Stateless)**
Si le client utilise **SLAAC pour l'adresse** mais récupère d'autres paramètres via DHCPv6 :
```bash
Client# configure terminal
Client(config)# interface GigabitEthernet0/1
Client(config-if)# ipv6 address autoconfig  # Utilise SLAAC
Client(config-if)# ipv6 dhcp client information request  # Demande les infos DHCPv6 (DNS, etc.)
Client(config-if)# no shutdown
Client(config-if)# exit
Client(config)# end
Client# write memory
```

---

## **📌 4. Vérifications**
Une fois la configuration terminée, on peut vérifier que le DHCPv6 fonctionne correctement.

### **📍 Vérification sur le Serveur DHCPv6**
```bash
R1# show ipv6 dhcp binding  # Vérifier les clients ayant reçu une adresse DHCPv6
```

### **📍 Vérification sur le Client**
```bash
Client# show ipv6 interface GigabitEthernet0/0
```
- En mode **Stateful**, tu verras une **adresse IPv6 assignée** par le serveur DHCPv6.
- En mode **Stateless**, tu verras une adresse IPv6 générée par **SLAAC**, mais avec des **serveurs DNS DHCPv6** configurés.

---

## ✅ **Résumé**
| **Méthode**  | **Adresse IPv6 attribuée par** | **Paramètres DNS/Domaine** | **Configuration du Client** |
|-------------|--------------------------------|----------------------------|-----------------------------|
| **DHCPv6 Stateful** | Serveur DHCPv6 | Oui (par DHCPv6) | `ipv6 address dhcp` |
| **DHCPv6 Stateless** | SLAAC (client génère son adresse) | Oui (par DHCPv6) | `ipv6 address autoconfig` + `ipv6 dhcp client information request` |

---

### 🔥 **Conclusion**
- Utilise **SLAAC** si tu veux un mode simple où les clients auto-configurent leurs adresses.
- Utilise **DHCPv6 Stateful** si tu veux un contrôle total sur l’attribution des adresses.
- Utilise **DHCPv6 Stateless** si tu veux que les clients génèrent leur adresse avec **SLAAC**, mais reçoivent d'autres infos via DHCPv6.

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
IPv6 client -- LAN /64 -- R1 (Router Advertisement + optional DHCPv6)
M flag: DHCPv6 address; O flag: other configuration.
```

Les noms d’interfaces et fonctionnalités de l’article sont des exemples. Avant toute saisie, relever le modèle, la version IOS/IOS XE, les ports réellement utilisés et les adresses déjà en service. Les configurations de cette page sont des supports pédagogiques, pas des configurations universelles prêtes à déployer.

### Vérification, dépannage et sécurité

1. Confirmer l’état physique et logique des interfaces, puis vérifier la configuration opérationnelle avec les commandes `show` déjà citées dans l’article.
2. Comparer les paramètres des deux extrémités lorsque le protocole le demande; contrôler les adresses, VLANs, masques, routes et filtres attendus.
3. Isoler une seule variable à la fois et vérifier les compteurs/états. Ne pas activer `debug` durablement sur un équipement de production.
4. Appliquer le moindre privilège, limiter les services et ports exposés, et ne jamais réutiliser un secret d’exemple. Pour les protocoles de découverte et de couche 2, limiter leur portée aux interfaces nécessaires.

### Exercice d’étude

Dessiner la topologie réelle du scénario, annoter les interfaces et les sous-réseaux, puis expliquer pour chaque commande de configuration quel état ou trafic elle doit modifier. Avant l’exécution, établir les commandes de vérification et le résultat fonctionnel attendu; ne pas conclure à la réussite sans observation dans un labo.

### Référence officielle

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_dhcp/configuration/xe-16/dhcp-xe-16-book/dhcpv6.html)
