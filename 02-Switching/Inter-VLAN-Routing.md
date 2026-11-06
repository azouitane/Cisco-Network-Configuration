# L'Inter-VLAN (Routage Inter-VLAN)

L'inter-VLAN, ou routage inter-VLAN, permet aux périphériques situés dans des VLANs différents de communiquer entre eux. Les VLANs (Virtual Local Area Networks) segmentent logiquement un réseau en domaines de diffusion distincts, améliorant la sécurité et les performances réseau. Cependant, pour que des périphériques sur des VLANs différents puissent interagir, un routeur ou un switch de couche 3 est nécessaire.

---

## **Principaux Composants du Routage Inter-VLAN**

### 1. **Router-on-a-Stick**
- **Description** : Cette méthode repose sur un routeur utilisant une interface physique unique configurée en mode trunk. Cette interface gère le trafic de plusieurs VLANs via des sous-interfaces.
- **Fonctionnement** : Chaque sous-interface est associée à un VLAN spécifique, avec une adresse IP qui sert de passerelle par défaut pour ce VLAN.
- **Avantage** : Idéal pour des réseaux de petite taille ou à faible trafic.
- **Limitation** : La bande passante est limitée à l'interface unique du routeur.

### 2. **Switch de Couche 3**
- **Description** : Un switch de couche 3 combine les fonctions de routage et de commutation, permettant un routage inter-VLAN sans nécessiter un routeur externe.
- **Fonctionnement** : Le switch crée des interfaces virtuelles (SVI - Switch Virtual Interface) pour chaque VLAN. Ces SVIs agissent comme passerelles par défaut.
- **Avantage** : Performances accrues grâce au routage matériel et réduction de la latence.
- **Limitation** : Généralement plus coûteux que les solutions basées sur un routeur.

### 3. **Ports Trunks et Adressage**
- Les **ports trunks** sur les switches transportent le trafic de plusieurs VLANs simultanément.
- Chaque VLAN possède une adresse IP de passerelle par défaut configurée sur le routeur ou le switch de couche 3.

---

## **Étapes de Configuration : Router-on-a-Stick**

### Étape 1 : **Créer les VLANs sur le switch**
```bash
Switch(config)# vlan 10
Switch(config-vlan)# name Vente
Switch(config)# vlan 20
Switch(config-vlan)# name Marketing
```

### Étape 2 : **Configurer le port trunk sur le switch**
```bash
Switch(config)# interface gig0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
```

### Étape 3 : **Configurer les sous-interfaces sur le routeur**
```bash
Router(config)# interface gig0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0

Router(config)# interface gig0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config)# interface gig0/0
Router(config-if)# no shutdown
```

Prérequis : VLAN 10 et 20 existent, le trunk relie `gig0/1` au routeur `gig0/0`, mêmes VLANs autorisés aux deux extrémités, sous-réseaux non chevauchants, hôtes configurés avec les passerelles `.1`. Les noms abrégés d’interfaces et la nécessité éventuelle de préciser l’encapsulation du trunk dépendent du modèle; plusieurs switches ne prennent en charge que 802.1Q. Vérifier `show vlan brief`, `show interfaces trunk`, `show ip interface brief` et `show ip route`. En cas d’échec, vérifier l’état physique, VLAN autorisés, correspondance VLAN/ID de sous-interface et passerelles.

### Étape 4 : **Tester la Connectivité**
- Vérifiez que les périphériques dans chaque VLAN peuvent pinger leur passerelle par défaut.
- Assurez-vous que les périphériques dans des VLANs différents peuvent communiquer.

---

## **Résumé des Avantages**
- **Sécurité** : Les VLANs isolent les segments réseau sensibles.
- **Performance** : Réduction du trafic inutile grâce à la segmentation.
- **Flexibilité** : Le routage inter-VLAN permet une communication contrôlée entre segments.

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
PC VLAN 10 -- SW1 ==trunk== R1
PC VLAN 20 --/             .10 = 192.168.10.1/24
                            .20 = 192.168.20.1/24
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

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst9300/software/release/17-6/configuration_guide/vlan/b_176_vlan_9300_cg.html)


## Lab associé

Mettre ces notions en pratique : [Lab 02](../07-Labs/02-VLAN-InterVLAN/README.md). Statut : `REQUIRES LAB VALIDATION`.
