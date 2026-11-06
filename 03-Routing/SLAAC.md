Le **SLAAC** (Stateless Address Autoconfiguration) est un mécanisme utilisé dans **IPv6** qui permet aux hôtes (ordinateurs, routeurs, etc.) de s'auto-configurer en obtenant un préfixe IPv6 à partir du routeur, sans avoir besoin de serveur DHCPv6 pour l'attribution des adresses. Le routeur annonce un préfixe via des messages **Router Advertisement (RA)**, et les hôtes utilisent ce préfixe pour générer une adresse IPv6.

### **Composants du SLAAC**
1. **Prefix** : Le routeur envoie un préfixe IPv6, que les hôtes utilisent pour générer leur adresse locale.
2. **Suffix** : Les hôtes utilisent leur **identifiant d'interface** pour compléter l'adresse IPv6.

### **Schéma de fonctionnement de SLAAC**
1. Un **routeur** envoie des **messages RA** contenant un préfixe et des informations (ex : la durée de validité de l'adresse).
2. Le **client** génère son adresse en utilisant :
   - Le **préfixe** annoncé par le routeur.
   - Un **identifiant d'interface** basé sur son adresse MAC (ou généré aléatoirement pour des raisons de confidentialité).
3. Le client se configure avec cette adresse et l’utilise pour communiquer.

---

### 📌 **Configuration du Routeur (SLAAC)**

Voici la configuration d'un **routeur Cisco** pour activer le SLAAC et annoncer un préfixe IPv6.

#### **1. Configuration de l'Interface du Routeur**

```bash
R1# configure terminal
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ipv6 address 2001:DB8:1::1/64   # Attribuer une adresse IPv6 au routeur
R1(config-if)# ipv6 nd other-config-flag       # Indique qu'un serveur DHCPv6 peut fournir des informations supplémentaires (DNS, etc.)
R1(config-if)# ipv6 nd prefix 2001:DB8:1::/64  # Annonce du préfixe SLAAC pour le réseau
R1(config-if)# no shutdown                     # Activation de l'interface
R1(config-if)# exit
R1(config)# ipv6 unicast-routing               # Activation du routage IPv6
R1(config)# end
R1# write memory
```

### 📌 **Explication des Commandes :**
- **`ipv6 address 2001:DB8:1::1/64`** : Configure une adresse IPv6 statique sur le routeur.
- **`ipv6 nd other-config-flag`** : Informe que le client peut obtenir d'autres paramètres de configuration via un serveur DHCPv6 (comme le DNS).
- **`ipv6 nd prefix 2001:DB8:1::/64`** : Annonce le préfixe utilisé pour SLAAC. Les clients vont générer une adresse basée sur ce préfixe.
- **`ipv6 unicast-routing`** : Active le routage IPv6 sur le routeur.
- **`no shutdown`** : Active l'interface.

---

### 📌 **Configuration du Client**
Sur le client, qui peut être un autre routeur ou un hôte, il n'y a pas de configuration nécessaire pour SLAAC si l'interface est configurée pour utiliser le mécanisme SLAAC par défaut.

#### **1. Configuration d'un Client pour SLAAC**

```bash
Client# configure terminal
Client(config)# interface GigabitEthernet0/0
Client(config-if)# ipv6 address autoconfig  # Permet l'auto-configuration basée sur SLAAC
Client(config-if)# no shutdown
Client(config-if)# exit
Client(config)# end
Client# write memory
```

### 📌 **Explication des Commandes :**
- **`ipv6 address autoconfig`** : Permet au client de se configurer automatiquement avec l'adresse IPv6 en utilisant SLAAC.

---

### **Vérification**
Une fois la configuration en place, tu peux vérifier que le client a bien généré son adresse IPv6 et qu'il l’utilise pour la communication.

#### **Sur le routeur :**
Vérifie les messages **RA** envoyés par le routeur :
```bash
R1# show ipv6 neighbors
```

#### **Sur le client :**
Vérifie l’adresse IPv6 configurée :
```bash
Client# show ipv6 interface GigabitEthernet0/0
```

Tu devrais voir une adresse IPv6 générée automatiquement par le client en fonction du préfixe annoncé par le routeur.

---

### 🎯 **Résumé :**
- Le **routeur** annonce un préfixe IPv6 via **RA**.
- Le **client** génère une adresse IPv6 à partir de ce préfixe.
- **Aucun serveur DHCPv6** n’est nécessaire pour l’attribution de l'adresse IPv6 (seulement pour des informations supplémentaires comme le DNS).

- **SLAAC** est activé par défaut sur les interfaces configurées pour IPv6.
- La configuration du client est simple et ne nécessite qu'une seule commande pour activer l'
auto-configuration basée sur SLAAC.
- La vérification des configurations et des adresses IPv6 générées peut être effectué
sur le routeur et le client.
- La communication réseau fonctionne correctement avec l'adresse IPv6 générée par le client
via SLAAC.

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Prérequis et vérification

Confirmer le modèle, la version logicielle, la fonctionnalité/licence disponible et le rôle de chaque interface avant d’utiliser les exemples. Utiliser les commandes `show` de l’article pour observer l’état opérationnel; une configuration présente dans `running-config` ne prouve pas à elle seule le fonctionnement attendu.

### Dépannage et sécurité

Vérifier d’abord l’état des interfaces, l’activation globale et locale du protocole, puis les deux extrémités et les compteurs. Réduire l’exposition des informations et services au strict nécessaire; désactiver la fonction sur les ports où elle n’est pas requise.

### Exercice d’étude

Représenter les équipements concernés, noter les paramètres à faire correspondre, puis définir une observation vérifiable pour le cas nominal et un symptôme de panne. L’exercice est à réaliser dans un laboratoire isolé; aucun résultat d’exécution n’est fourni ici.

### Référence officielle

- [Cisco — documentation pertinente](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipv6/configuration/xe-16/ipv6-xe-16-book.html)
