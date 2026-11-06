### 🔹 **Le Protocole OSPF (Open Shortest Path First)**  

**OSPF** (Open Shortest Path First) est un protocole de routage dynamique à **état de lien** utilisé dans les réseaux IP. Il permet aux routeurs d'échanger des informations de routage et de déterminer les meilleurs chemins vers les destinations. OSPF est un protocole de routage **interne** (IGP - Interior Gateway Protocol) et est largement utilisé dans les réseaux d'entreprise.

---

## **📌 1. Fonctionnement de OSPF**
- **Protocole à état de lien** : Chaque routeur partage l'état de ses liens (LSA - Link State Advertisement) avec les autres routeurs pour créer une carte complète du réseau.
- **Algorithme SPF (Shortest Path First)** : L'algorithme de Dijkstra est utilisé pour calculer les plus courts chemins vers toutes les destinations à partir de chaque routeur.
- **Hiérarchie en zones** : OSPF peut être divisé en plusieurs **zones** (inclus la zone 0, ou zone backbone), pour optimiser les performances et la gestion.
- **Support des réseaux IP multi-accès** : OSPF fonctionne efficacement sur des réseaux comme Ethernet, où plusieurs routeurs sont connectés à un même réseau.

---

## **📌 2. Configuration de base de OSPF sur un Routeur Cisco**
### **📍 Étape 1 : Activer OSPF**
Dans cette étape, nous allons configurer un routeur avec l’ID OSPF et un réseau.

```bash
Router1# configure terminal
Router1(config)# router ospf 1  # Activer OSPF et définir l'ID de processus OSPF
Router1(config-router)# router-id 1.1.1.1  # Définir un Router ID unique pour OSPF
```

---

### **📍 Étape 2 : Ajouter les interfaces OSPF**
Les interfaces qui doivent participer à OSPF doivent être associées à un **réseau OSPF** spécifique.

```bash
Router1(config-router)# network 192.168.1.0 0.0.0.255 area 0  # Déclarer un réseau dans la zone 0
Router1(config-router)# network 192.168.2.0 0.0.0.255 area 0  # Déclarer un autre réseau dans la zone 0
```

Les commandes `network` sélectionnent les interfaces dont l’adresse correspond au masque générique; elles ne créent pas de routes ni n’affectent directement un préfixe arbitraire. Les réseaux `192.168.1.0/24` et `192.168.2.0/24` doivent être réellement configurés sur des interfaces opérationnelles. Pour éviter une adjacence sur un LAN utilisateur, configurez les interfaces concernées en passive selon le besoin. Les voisins doivent partager le segment ou avoir un chemin unicast valide et des paramètres Hello/dead, area, authentification et MTU compatibles. Vérifier `show ip ospf neighbor`, `show ip ospf interface brief`, `show ip route ospf`; une table de voisins vide indique souvent absence d’interface correspondante ou mismatch. IOS/IOS XE peuvent aussi permettre `ip ospf <process> area <area>` par interface, mais l’exemple reste en syntaxe classique.

#### Explication :
- **`network`** : Le réseau à annoncer dans OSPF.
- **`0.0.0.255`** : C'est le **masque inverse**. Cela permet de spécifier quel réseau d'adresses IP sera pris en compte.
- **`area 0`** : Déclare les réseaux dans la **zone 0** (backbone OSPF).

---

### **📍 Étape 3 : Vérifier la configuration**
Pour vérifier que OSPF fonctionne correctement et pour voir les voisins OSPF :

```bash
Router1# show ip ospf neighbor  # Affiche les voisins OSPF
Router1# show ip ospf interface  # Affiche l'état des interfaces OSPF
Router1# show ip route ospf  # Affiche les routes apprises via OSPF
```

---

## **📌 3. Concepts Clés d’OSPF**
### **📍 Router ID (ID de routeur)**
Le **Router ID** est un identifiant unique pour chaque routeur OSPF dans le réseau. Il est nécessaire pour identifier chaque routeur dans le processus OSPF.

- **Calcul automatique** : OSPF attribue le Router ID en fonction de la plus grande adresse IP sur une interface Loopback, sinon la plus grande adresse IP physique.
- **Manuel** : Tu peux définir un Router ID manuellement avec la commande `router-id`.

---

### **📍 Zones OSPF**
Les zones permettent de diviser un réseau OSPF pour une gestion plus efficace. Il y a **la zone 0 (backbone)** et d’autres zones peuvent être ajoutées pour segmenter le réseau.

- **Zone 0** : C’est la zone backbone, et tous les autres réseaux doivent être connectés à elle.
- **Zones non-backbone** : Ces zones doivent être connectées à la zone 0.

---

### **📍 LSA (Link State Advertisement)**
Les **LSA** sont des messages échangés entre les routeurs pour partager des informations sur les liens réseau. Il existe plusieurs types d’LSA, chacun ayant un rôle spécifique dans le processus OSPF :
- **Type 1** : Router LSA (partagé par chaque routeur)
- **Type 2** : Network LSA (généré par le routeur désigné sur un réseau multi-accès)
- **Type 3** : Summary LSA (partagé entre les zones)
- **Type 4** : ASBR Summary LSA (indique la présence de routeurs ASBR - Autonomous System Boundary Routers)

---

### **📍 États des Routeurs OSPF**
Un routeur OSPF passe par plusieurs **états** lors de l'établissement de la relation avec ses voisins :
1. **Down** : Pas de paquet Hello reçu.
2. **Attempt** : Le routeur envoie des paquets Hello.
3. **Init** : Un paquet Hello a été reçu, mais pas encore de bidirectionnalité.
4. **Two-way** : Les voisins se reconnaissent mutuellement.
5. **ExStart** : Échange de base de données.
6. **Exchange** : Les routeurs échangent des LSR (Link State Request).
7. **Loading** : Le routeur charge les informations de la base de données.
8. **Full** : La relation de voisin est entièrement établie.

---

## **📌 4. Configuration Avancée**
### **📍 Configurer OSPF sur plusieurs zones**
Si tu veux diviser ton réseau en plusieurs zones :

```bash
Router2# configure terminal
Router2(config)# router ospf 1
Router2(config-router)# network 10.0.0.0 0.0.0.255 area 0  # Zone 0 (backbone)
Router2(config-router)# network 10.0.1.0 0.0.0.255 area 1  # Zone 1
Router2(config-router)# network 10.0.2.0 0.0.0.255 area 1  # Zone 1
Router2(config-router)# exit
```

### **📍 Configurer une zone NSSA (Not So Stubby Area)**
Une **NSSA** est une zone qui permet d’introduire des routes externes dans OSPF mais avec des limitations par rapport à une zone normale. 

```bash
Router2(config-router)# area 1 nssa
```

### **📍 Configurer une Route Externe OSPF**
Les routes externes peuvent être importées dans OSPF via un routeur ASBR (Autonomous System Boundary Router).

```bash
Router3# configure terminal
Router3(config)# router ospf 1
Router3(config-router)# redistribute static subnets  # Redistribuer les routes statiques dans OSPF
```

---

## **📌 5. Vérification et Dépannage**
- **Vérifier les voisins OSPF** : `show ip ospf neighbor`
- **Vérifier les routes OSPF** : `show ip route ospf`
- **Vérifier les bases de données OSPF** : `show ip ospf database`
- **Vérifier l'état des interfaces OSPF** : `show ip ospf interface`

---

## ✅ **Résumé des Commandes Importantes**
| **Commande** | **Description** |
|--------------|-----------------|
| `router ospf <ID>` | Activer OSPF avec l’ID de processus |
| `router-id <ID>` | Définir un Router ID spécifique |
| `network <réseau> <masque inverse> area <zone>` | Ajouter un réseau à une zone OSPF |
| `show ip ospf neighbor` | Vérifier les voisins OSPF |
| `show ip route ospf` | Vérifier les routes apprises via OSPF |
| `show ip ospf database` | Vérifier la base de données OSPF |

---

### 🎯 **Conclusion**
OSPF est un protocole de routage puissant et flexible, adapté aux grands réseaux. En utilisant les concepts de **zones**, **LSA** et **algorithme SPF**, il permet de déterminer efficacement les meilleurs chemins dans un réseau.

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
LAN-A -- R1 ===== OSPF area 0 ===== R2 -- LAN-B
        R1/R2 must share a routed link and compatible OSPF parameters.
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

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_ospf/configuration/xe-16/iro-xe-16-book.html)


## Lab associé

Mettre ces notions en pratique : [Lab 04](../07-Labs/04-OSPF/README.md). Statut : `REQUIRES LAB VALIDATION`.
