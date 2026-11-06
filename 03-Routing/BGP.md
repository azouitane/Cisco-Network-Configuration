# 🔹 **Le Protocole BGP (Border Gateway Protocol)**  

## 📌 **1. Introduction à BGP**  
Le **Border Gateway Protocol (BGP)** est un **protocole de routage externe (EGP - Exterior Gateway Protocol)** utilisé pour échanger des informations de routage entre des **systèmes autonomes (AS - Autonomous Systems)** sur Internet. Contrairement à OSPF ou EIGRP, qui sont des protocoles de routage interne (IGP), **BGP est conçu pour le routage entre différentes organisations et fournisseurs d'accès à Internet (FAI)**.

---

## 📌 **2. Fonctionnement de BGP**  
### **📍 Principes clés de BGP :**
- **Basé sur le chemin** : Contrairement aux IGP qui utilisent des métriques comme le coût ou la bande passante, **BGP utilise un algorithme basé sur les chemins (path vector protocol)** pour prendre ses décisions de routage.
- **Échange d'informations entre AS** : BGP fonctionne sur **TCP (port 179)** et établit des connexions entre des **pairs BGP (peers ou voisins)** pour échanger des routes.
- **Évite les boucles** : Chaque routeur BGP connaît le chemin complet vers une destination grâce à l’attribut **AS_PATH**, ce qui empêche les boucles de routage.
- **Stabilité et politique de routage** : BGP permet d’appliquer des **politiques de routage** à l’aide d’attributs comme **LOCAL_PREF, MED, AS_PATH**.

---

## 📌 **3. Types de BGP**
### 🔹 **iBGP (Internal BGP)**
- Utilisé **au sein d’un même AS** pour partager des routes BGP entre routeurs internes.
- **Nécessite une connexion complète** entre tous les routeurs BGP (ou l’utilisation de Route Reflectors pour optimiser la communication).

### 🔹 **eBGP (External BGP)**
- Utilisé pour **échanger des routes entre différents AS** (ex. entre un FAI et une entreprise).
- Les routes apprises via eBGP sont redistribuées avec une **distance administrative de 20**.

---

## 📌 **4. Configuration de BGP sur un Routeur Cisco**
### **📍 Étape 1 : Activer BGP et définir l’AS**
Sur **Router1**, appartenant à l’AS 65001 :

```bash
Router1# configure terminal
Router1(config)# router bgp 65001  # AS local
Router1(config-router)# bgp router-id 1.1.1.1  # Définir l'ID BGP
```

---

### **📍 Étape 2 : Établir une relation eBGP**
Connexion avec un voisin eBGP dans l’AS **65002** :

```bash
Router1(config-router)# neighbor 192.168.1.2 remote-as 65002  # Définir un voisin eBGP
Router1(config-router)# exit
```

---

### **📍 Étape 3 : Annoncer un réseau dans BGP**
Si nous avons le réseau **10.0.0.0/24** dans notre AS, nous devons l'annoncer dans BGP :

```bash
Router1(config-router)# network 10.0.0.0 mask 255.255.255.0
```

💡 **Attention !** BGP n’annonce que les routes qui existent dans la table de routage.

---

### **📍 Étape 4 : Vérifier l'état de BGP**
Quelques commandes utiles pour diagnostiquer et vérifier BGP :

```bash
Router1# show ip bgp summary  # Vérifier l'état des voisins BGP
Router1# show ip bgp  # Afficher la table BGP
Router1# show ip bgp neighbors  # Détails des voisins BGP
Router1# show ip route bgp  # Routes apprises via BGP
```

---

## 📌 **5. Attributs BGP**
Les **attributs BGP** influencent la sélection du meilleur chemin :

| **Attribut**  | **Description** |
|--------------|-----------------|
| **Weight** | Attribut local sur Cisco, plus il est élevé, mieux c'est. |
| **LOCAL_PREF** | Priorité des routes au sein d'un AS. Plus haut = préférable. |
| **AS_PATH** | Liste des AS traversés pour atteindre une destination (plus court = préférable). |
| **MED (Multi-Exit Discriminator)** | Suggère un meilleur chemin pour entrer dans un AS. |
| **Next-Hop** | Indique l'IP du prochain saut. |

---

## 📌 **6. Configuration iBGP (BGP Interne)**
Dans un **même AS**, tous les routeurs doivent être connectés entre eux. Une alternative est d'utiliser un **Route Reflector**.

Exemple avec **deux routeurs dans AS 65001** :

```bash
Router2# configure terminal
Router2(config)# router bgp 65001
Router2(config-router)# neighbor 192.168.2.1 remote-as 65001  # iBGP avec Router1
Router2(config-router)# exit
```

---

## 📌 **7. BGP Peering et États des Voisins**
BGP passe par plusieurs **états** avant d’établir une session :

| **État** | **Description** |
|----------|----------------|
| **Idle** | Attente d'une demande de connexion TCP. |
| **Connect** | Établissement de la connexion TCP. |
| **Active** | Tentative d'établissement de connexion. |
| **OpenSent** | Échange du message "OPEN". |
| **OpenConfirm** | Validation des paramètres. |
| **Established** | Peering BGP établi et échange des routes. |

---

## 📌 **8. Filtrage des Routes avec BGP**
Tu peux **filtrer les annonces BGP** avec **des listes d'accès et des route-maps**.

### 🔹 **Filtrage avec une ACL**
```bash
Router1(config)# access-list 1 permit 192.168.10.0 0.0.0.255
Router1(config)# route-map FILTER-BGP deny 10
Router1(config-route-map)# match ip address 1
Router1(config-route-map)# exit
Router1(config)# route-map FILTER-BGP permit 20
Router1(config)# router bgp 65001
Router1(config-router)# neighbor 192.168.1.2 route-map FILTER-BGP out
```

**Attention :** l’ACL associée à `match ip address` sélectionne le réseau visé. La séquence `deny 10` de la route-map rejette ce réseau; la séquence `permit 20` permet les autres préfixes. Vérifiez toujours les annonces réellement envoyées avec `show route-map FILTER-BGP` et `show ip bgp neighbors 192.168.1.2 advertised-routes` (cette dernière commande dépend de la version/plateforme).

Le `network 10.0.0.0 mask 255.255.255.0` n’annonce le préfixe que si une route correspondante /24 existe dans la table de routage. L’exemple eBGP requiert une interface/route de connectivité vers `192.168.1.2`; le cas iBGP exige une connectivité IP et, au-delà de deux routeurs, une topologie full-mesh ou un route reflector. Les adresses d’exemple des sections ne décrivent pas une seule topologie intégrée. Vérifier `show ip bgp summary`, `show ip route`, `show ip bgp` et les routes annoncées/reçues. Une session `Established` seule ne garantit pas que les préfixes attendus soient échangés.

---

## 📌 **9. Résolution de Problèmes BGP**
Si BGP ne fonctionne pas, voici quelques pistes :

- **Vérifier les voisins** avec `show ip bgp summary`.
- **Vérifier l’état de la session** (`Established` = OK).
- **Vérifier les routes BGP** avec `show ip bgp`.
- **Vérifier les filtres et route-maps** appliqués aux voisins.

---

## ✅ **Résumé des Commandes Importantes**
| **Commande** | **Description** |
|--------------|-----------------|
| `router bgp <AS>` | Activer BGP avec l’AS local |
| `neighbor <IP> remote-as <AS>` | Ajouter un voisin BGP |
| `network <IP> mask <masque>` | Annoncer un réseau BGP |
| `show ip bgp summary` | Vérifier l'état des voisins BGP |
| `show ip bgp` | Afficher la table BGP |
| `show ip bgp neighbors` | Détails des voisins BGP |
| `show ip route bgp` | Routes apprises via BGP |

---

## 🎯 **Conclusion**
BGP est **le protocole clé du routage Internet**. Contrairement aux protocoles IGP (comme OSPF), il est basé sur **des politiques de routage et des attributs de chemin**. Sa configuration est plus complexe, mais il permet un **contrôle précis** du trafic entre les AS.

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
AS 65001 (R1) -- eBGP -- AS 65002 (R2)
Prefix advertisement requires a matching route and deliberate policy.
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

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/xe-16/irg-xe-16-book.html)
