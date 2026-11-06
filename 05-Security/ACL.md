# 🔹 **Configuration des Listes de Contrôle d’Accès (ACL) sur un routeur Cisco**  

## 📌 **1. Introduction aux ACL**  
Les **ACL (Access Control Lists)** sont utilisées sur les routeurs Cisco pour filtrer le trafic réseau en fonction d’adresses IP, de protocoles, de ports, etc. Elles permettent de :
✅ **Restreindre l’accès** aux ressources réseau  
✅ **Améliorer la sécurité** en limitant certains types de trafic  
✅ **Optimiser la gestion du trafic**  

Il existe **deux types** d’ACL sur Cisco :  
- **ACL standard** (filtre uniquement sur les adresses IP source)  
- **ACL étendue** (filtre sur l’IP source/destination, protocole, port, etc.)  

---

## 📌 **2. ACL Standard**
**Les ACL standards filtrent uniquement sur l’adresse IP source.**  

### **🔹 Exemple de topologie**
Un réseau avec **trois sous-réseaux** :  
- **192.168.1.0/24** (réseau interne)  
- **192.168.2.0/24** (réseau invité)  
- **192.168.3.0/24** (réseau administratif)  

On veut **bloquer l’accès du réseau invité (192.168.2.0/24) au réseau administratif (192.168.3.0/24)**.

### **🔹 Étape 1 : Configurer une ACL Standard**
```bash
R1(config)# access-list 10 deny 192.168.2.0 0.0.0.255
R1(config)# access-list 10 permit any
```
📌 **Explication** :
- `access-list 10 deny 192.168.2.0 0.0.0.255` → Bloque tout le trafic du réseau 192.168.2.0  
- `access-list 10 permit any` → Autorise tout le reste du trafic  

---

### **🔹 Étape 2 : Appliquer l’ACL sur une Interface**
On applique cette ACL sur **l’interface connectée au réseau administratif** en entrée :
```bash
R1(config)# interface GigabitEthernet0/1
R1(config-if)# ip access-group 10 in
R1(config-if)# exit
```
📌 **Explication** :
- `ip access-group 10 in` → Applique l’ACL 10 sur le trafic **entrant**  

---

## 📌 **3. ACL Étendue**
**Les ACL étendues filtrent sur les adresses IP source/destination, protocole et ports.**  

### **🔹 Objectif**  
Bloquer **les connexions HTTP (port 80)** venant du réseau invité **192.168.2.0/24** vers le serveur Web **192.168.3.10**.

### **🔹 Étape 1 : Configurer une ACL Étendue**
```bash
R1(config)# access-list 100 deny tcp 192.168.2.0 0.0.0.255 host 192.168.3.10 eq 80
R1(config)# access-list 100 permit ip any any
```
📌 **Explication** :
- `deny tcp 192.168.2.0 0.0.0.255 host 192.168.3.10 eq 80` → Bloque HTTP (port 80) du réseau invité vers le serveur  
- `permit ip any any` → Autorise tout le reste du trafic  

---

### **🔹 Étape 2 : Appliquer l’ACL sur l’Interface**
Appliquer sur l’interface connectée au réseau invité **en sortie** :
```bash
R1(config)# interface GigabitEthernet0/2
R1(config-if)# ip access-group 100 out
R1(config-if)# exit
```
📌 **Explication** :
- `ip access-group 100 out` → Applique l’ACL 100 sur le trafic **sortant**  

---

## 📌 **4. ACL avec Numérotation Nominale**
Au lieu d’utiliser des numéros (`10`, `100`), on peut nommer les ACL.

### **🔹 Exemple : Bloquer le ping (ICMP) vers le réseau 192.168.3.0**
```bash
R1(config)# ip access-list extended BLOCK_PING
R1(config-ext-nacl)# deny icmp any 192.168.3.0 0.0.0.255
R1(config-ext-nacl)# permit ip any any
R1(config-ext-nacl)# exit
```
Appliquer sur l’interface connectée au réseau invité :
```bash
R1(config)# interface GigabitEthernet0/2
R1(config-if)# ip access-group BLOCK_PING in
```

---

## 📌 **5. Vérification et Dépannage**
### **🔹 Afficher les ACL configurées**
```bash
R1# show access-lists
```

### **🔹 Vérifier les ACL appliquées sur une interface**
```bash
R1# show ip interface GigabitEthernet0/1
```

### **🔹 Désactiver une ACL d’une interface**
```bash
R1(config)# interface GigabitEthernet0/1
R1(config-if)# no ip access-group 10 in
```

### **🔹 Supprimer une ACL**
```bash
R1(config)# no access-list 10
```

---

## ✅ **Résumé des Commandes Importantes**
| **Commande** | **Description** |
|--------------|-----------------|
| `access-list <numéro> permit/deny <protocole> <source> <masque> <destination> <port>` | Crée une ACL standard ou étendue |
| `ip access-group <numéro/nom> in/out` | Applique une ACL sur une interface |
| `ip access-list extended <nom>` | Crée une ACL nommée |
| `show access-lists` | Affiche les ACL configurées |
| `show ip interface <interface>` | Vérifie les ACL appliquées |
| `no access-list <numéro>` | Supprime une ACL |

---

## 🎯 **Conclusion**
- **ACL standard** : Filtre uniquement **sur l’IP source**  
- **ACL étendue** : Filtre **IP source/destination, protocole et ports**  
- **Appliquer les ACL au bon endroit** (trafic entrant ou sortant)  
- Une ACL IPv4 possède un refus implicite final. N’ajoutez une règle `permit` finale que si elle correspond explicitement à la politique voulue.

## Correction de portée et de placement

Une ACL standard ne peut pas distinguer la destination : la règle `deny 192.168.2.0 0.0.0.255` bloque le réseau invité vers toutes les destinations lorsqu’elle est appliquée. Pour ne bloquer que HTTP vers le serveur, utilisez une ACL étendue près de la source. Exemple de référence (adaptez l’interface et la politique) :

```text
R1(config)# ip access-list extended GUEST_FILTER
R1(config-ext-nacl)# deny tcp 192.168.2.0 0.0.0.255 host 192.168.3.10 eq 80
R1(config-ext-nacl)# permit ip 192.168.2.0 0.0.0.255 any
R1(config-ext-nacl)# exit
R1(config)# interface GigabitEthernet0/2
R1(config-if)# ip access-group GUEST_FILTER in
```

Ici `GigabitEthernet0/2` doit être l’interface par laquelle le trafic du réseau invité entre dans R1. La règle permit limite le reste du trafic aux sources invitées; le refus implicite bloque les autres sources arrivant sur cette interface. Ne copiez pas cet exemple sans vérifier le sens, les autres usages de l’interface et la politique de sécurité.

Prérequis : adressage et routage fonctionnels, interface identifiée, politique de flux décidée. Les ACL IPv4 classiques sont disponibles sur de nombreuses versions IOS/IOS XE, mais les options et capacités varient. Vérifier avec `show access-lists` et `show ip interface GigabitEthernet0/2`; tester les flux autorisés et refusés. En cas d’interruption, vérifier compteurs, ordre des ACE et sens `in`/`out` avant d’élargir l’autorisation.

---

## Repères de lecture

> Statut : `REVIEWED` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
Guest 192.168.2.0/24 --> [R1 ACL in] --> Server 192.168.3.10
Example policy blocks TCP/80 only; validate direction and other flows.
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

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/sec_data_acl/configuration/xe-16/sec-data-acl-xe-16-book.html)


## Lab associé

Mettre ces notions en pratique : [Lab 06](../07-Labs/06-ACL-Security/README.md). Statut : `REQUIRES LAB VALIDATION`.
