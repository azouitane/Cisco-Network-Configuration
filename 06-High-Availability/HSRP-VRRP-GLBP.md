Voici les propriétés, les avantages et les commandes des protocoles FHRP (First Hop Redundancy Protocols) : **VRRP** (Virtual Router Redundancy Protocol), **HSRP** (Hot Standby Router Protocol) et **GLBP** (Gateway Load Balancing Protocol).

### 1. **VRRP (Virtual Router Redundancy Protocol)**
#### Propriétés
- **Normalisé** : VRRP est un protocole standardisé par l'IETF (RFC 5798).
- **Adressage IP Virtuel** : Il permet à plusieurs routeurs de partager une adresse IP virtuelle, où l'un des routeurs est élu comme maître.

#### Avantages
- **Interopérabilité** : Puisqu'il est standardisé, il fonctionne avec différents équipements de différents fabricants.
- **Basé sur les priorités** : La redondance est déterminée par la priorité, et le routeur ayant la plus haute priorité devient le maître.

#### Commandes de base (Cisco IOS)
```bash
interface GigabitEthernet0/1
  vrrp 1 ip 192.168.1.1
  vrrp 1 priority 120
  vrrp 1 preempt
```

### 2. **HSRP (Hot Standby Router Protocol)**
#### Propriétés
- **Propriétaire** : HSRP est un protocole propriétaire de Cisco.
- **État de veille actif/secondaire** : Un routeur est actif, tandis que l'autre reste en mode veille, prêt à prendre la relève si nécessaire.

#### Avantages
- **Facile à configurer** : Idéal pour les réseaux simples où une redondance basique est nécessaire.
- **Suivi d'interface** : Possibilité de suivre les interfaces pour détecter les pannes de routeur et basculer automatiquement vers le routeur de secours.

#### Commandes de base (Cisco IOS)
```bash
interface GigabitEthernet0/1
  standby 1 ip 192.168.1.1
  standby 1 priority 110
  standby 1 preempt
```

### 3. **GLBP (Gateway Load Balancing Protocol)**
#### Propriétés
- **Propriétaire** : GLBP est également un protocole propriétaire de Cisco.
- **Équilibrage de charge** : GLBP permet l'équilibrage de charge entre plusieurs routeurs, en répartissant le trafic sur plusieurs passerelles.

#### Avantages
- **Distribution du trafic** : Capacité à utiliser plusieurs passerelles de manière simultanée, améliorant ainsi la répartition du trafic.
- **Gestion de l'état de veille** : Comme HSRP, GLBP prend en charge la bascule en cas de panne, tout en permettant l'équilibrage de charge.

#### Commandes de base (Cisco IOS)
```bash
interface GigabitEthernet0/1
  glbp 1 ip 192.168.1.1
  glbp 1 priority 100
  glbp 1 preempt
```

Ces protocoles permettent tous d'assurer la redondance et la haute disponibilité dans les réseaux, mais ils diffèrent par leur fonctionnement, leurs avantages et leurs options de configuration.

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
             virtual gateway 192.168.1.1
             /                     \
       R1 active/master         R2 standby/backup
             \_____ shared LAN _____/
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

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipapp_fhrp/configuration/xe-16/fhp-xe-16-book/fhp-hsrp.html)
