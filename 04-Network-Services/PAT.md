# 🔹 **Configuration du PAT (Port Address Translation) sur un routeur Cisco**  

## 📌 **1. Introduction au PAT**  
Le **PAT (Port Address Translation)** est une forme de **NAT dynamique** qui permet à plusieurs adresses IP privées d'utiliser une seule adresse IP publique en différenciant les connexions à l'aide des numéros de port.  

Le **PAT est couramment utilisé** pour :  
✅ Permettre à plusieurs hôtes internes d’accéder à Internet avec une seule adresse IP publique.  
✅ Économiser les adresses IPv4 publiques.  
✅ Offrir un niveau supplémentaire de sécurité en masquant les adresses internes.  

---

## 📌 **2. Exemple de topologie**  
- **Réseau interne** : **192.168.1.0/24**  
- **Interface publique** : **GigabitEthernet0/1** avec l'IP publique **200.200.200.10**  

---

## 📌 **3. Configuration du PAT avec une seule IP publique**  
### **🔹 Étape 1 : Configurer une liste d'accès (ACL) pour les adresses internes**
On commence par créer une **ACL** qui identifie les adresses IP privées autorisées à utiliser le NAT.  
```bash
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255
```
📌 **Explication** : Cette ACL permet à tout hôte de **192.168.1.0/24** d'utiliser le PAT.

---

### **🔹 Étape 2 : Configurer le PAT avec une interface externe**
```bash
R1(config)# ip nat inside source list 1 interface GigabitEthernet0/1 overload
```
📌 **Explication** :  
- `source list 1` → Utilise l'ACL 1 pour définir les adresses privées à traduire.  
- `interface GigabitEthernet0/1` → Utilise l'adresse IP de cette interface comme adresse publique.  
- `overload` → Permet à plusieurs adresses privées de partager une seule IP publique en utilisant différents **numéros de port**.

---

### **🔹 Étape 3 : Définir les interfaces NAT**
```bash
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip nat inside
R1(config-if)# exit

R1(config)# interface GigabitEthernet0/1
R1(config-if)# ip nat outside
R1(config-if)# exit
```
📌 **Explication** :  
- L'interface **GigabitEthernet0/0** est définie comme interface **interne (inside)**.  
- L'interface **GigabitEthernet0/1** est définie comme interface **externe (outside)**.

---

## 📌 **4. Configuration du PAT avec un Pool d'IP Publiques**  
Si plusieurs adresses IP publiques sont disponibles, on peut utiliser un **pool d'adresses** plutôt qu'une seule IP.

### **🔹 Étape 1 : Définir un Pool d'IP Publiques**
```bash
R1(config)# ip nat pool PUBLIC_POOL 200.200.200.10 200.200.200.20 netmask 255.255.255.0
```
📌 **Explication** : Crée un **pool** d'adresses IP publiques de **200.200.200.10 à 200.200.200.20**.

### **🔹 Étape 2 : Appliquer le NAT avec Overload**
```bash
R1(config)# ip nat inside source list 1 pool PUBLIC_POOL overload
```
📌 **Explication** :  
- `pool PUBLIC_POOL` → Utilise le pool défini pour l'adressage.  
- `overload` → Active le **PAT** pour permettre le partage des IP avec des ports différents.

---

## 📌 **5. Vérification et Dépannage du PAT**
### **🔹 Vérifier les traductions NAT actives**
```bash
R1# show ip nat translations
```
📌 Affiche les traductions NAT en cours, y compris les ports utilisés.

### **🔹 Vérifier les statistiques NAT**
```bash
R1# show ip nat statistics
```
📌 Montre le nombre de traductions NAT en cours et le type de NAT utilisé.

### **🔹 Vérifier la configuration des interfaces**
```bash
R1# show running-config | include ip nat
```
📌 Vérifie si les interfaces **inside/outside** et les règles NAT sont bien configurées.

---

## ✅ **Résumé des Commandes Importantes**
| **Commande** | **Description** |
|--------------|-----------------|
| `access-list 1 permit 192.168.1.0 0.0.0.255` | Définit les adresses internes à NATer |
| `ip nat inside source list 1 interface GigabitEthernet0/1 overload` | Active le PAT avec une seule IP publique |
| `ip nat pool PUBLIC_POOL <IP_start> <IP_end> netmask <masque>` | Définit un pool d'IP publiques |
| `ip nat inside source list 1 pool PUBLIC_POOL overload` | Configure le PAT avec un pool d'adresses IP publiques |
| `show ip nat translations` | Vérifie les traductions NAT en cours |
| `show ip nat statistics` | Affiche les statistiques du NAT |

---

## 🎯 **Conclusion**
- Le **PAT** est une solution efficace pour permettre à **plusieurs hôtes internes** d'accéder à Internet avec une seule **adresse IP publique**.
- Il fonctionne en attribuant un **port unique** à chaque session pour différencier les connexions.
- Le **PAT avec pool** est utilisé lorsqu'on dispose de **plusieurs IP publiques**.

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
Hosts 192.168.1.0/24 -- inside R1 outside -- WAN address
PAT multiplexes flows using transport identifiers.
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

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_nat/configuration/xe-16/nat-xe-16-book.html)


## Lab associé

Mettre ces notions en pratique : [Lab 05](../07-Labs/05-NAT-PAT/README.md). Statut : `REQUIRES LAB VALIDATION`.
