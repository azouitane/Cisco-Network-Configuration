Le **protocole STP** (Spanning Tree Protocol) est un protocole réseau utilisé pour éviter les boucles dans les réseaux commutés (switchés). Ces boucles peuvent se produire lorsqu'il existe des chemins redondants entre des commutateurs, ce qui est souvent une pratique courante pour assurer la tolérance aux pannes. Cependant, elles peuvent entraîner des problèmes comme des tempêtes de broadcast, des duplications de paquets, et une surcharge du réseau.

### Fonctionnement du STP :
STP est défini dans la norme **IEEE 802.1D** et fonctionne en créant une **arborescence logique sans boucle** (Spanning Tree) pour désactiver les chemins redondants tout en permettant leur réactivation en cas de défaillance.

#### Étapes principales :
1. **Élection du Root Bridge :**
   - Le commutateur avec l'identifiant Bridge ID (BID) le plus bas devient le **Root Bridge**.
   - Le BID est une combinaison de deux valeurs : une priorité (par défaut 32768) et l'adresse MAC du commutateur.

2. **Détermination des Root Ports :**
   - Chaque commutateur identifie le port qui mène au Root Bridge avec le chemin de coût le plus bas. Ce port est appelé **Root Port**.

3. **Élection des Designated Ports :**
   - Parmi les liens connectés à chaque segment réseau, un seul port est élu comme **Designated Port** (celui avec le coût le plus faible vers le Root Bridge).

4. **Blocage des ports redondants :**
   - Les ports restants sont mis en état de **blocage** pour éviter les boucles.

#### États des ports dans STP :
- **Blocking** : Ne transmet ni ne reçoit de données, écoute uniquement les BPDU.
- **Listening** : Écoute les BPDU pour déterminer si une boucle existe.
- **Learning** : Apprend les adresses MAC mais ne transmet pas encore de trafic.
- **Forwarding** : Transmet et reçoit les données normalement.
- **Disabled** : État administrativement désactivé.

#### Les BPDU (Bridge Protocol Data Units) :
- STP utilise des messages appelés **BPDU** pour échanger des informations entre les commutateurs.
- Ils contiennent les informations nécessaires à l'élection du Root Bridge et à la détermination des rôles des ports.

---

### Types de STP :
1. **STP classique (IEEE 802.1D)** : La version originale, mais lente à converger (~30 à 50 secondes).
2. **RSTP (Rapid Spanning Tree Protocol, IEEE 802.1w)** : Une version améliorée qui réduit le temps de convergence à quelques secondes.
3. **MSTP (Multiple Spanning Tree Protocol, IEEE 802.1s)** : Permet la gestion de plusieurs instances d'arborescence pour différents VLAN.
4. **PVST+/RPVST+** : Protocoles propriétaires de Cisco qui créent une instance STP par VLAN.

---

### Avantages :
- Évite les boucles dans les réseaux commutés.
- Permet une redondance avec des chemins de secours.
- Maintient la stabilité du réseau.

### Inconvénients :
- STP classique est lent à converger.
- La configuration manuelle peut être complexe dans les grands réseaux.
- RSTP ou MSTP sont préférables pour de meilleures performances.

---

Voici une configuration typique du protocole **STP** sur un switch, en prenant comme exemple un équipement **Cisco** avec l'IOS (Interface de ligne de commande Cisco). Nous allons couvrir la configuration de base, puis des ajustements pour optimiser le comportement du STP.

---

### 1. **Activer le protocole STP** (activé par défaut)
Par défaut, STP est activé sur tous les switches Cisco. Si vous voulez vérifier son état :

```bash
Switch> enable
Switch# show spanning-tree
```

Si STP est désactivé, vous pouvez l'activer sur un VLAN spécifique ou globalement :

```bash
Switch(config)# spanning-tree vlan [vlan-id]
```

---

### 2. **Configuration de la priorité pour le Root Bridge**
Le commutateur avec la plus **faible priorité** devient le **Root Bridge**. Vous pouvez forcer un commutateur à devenir Root Bridge en ajustant sa priorité :

```bash
Switch(config)# spanning-tree vlan [vlan-id] priority [valeur]
```

> ⚠️ La priorité par défaut est **32768**, et elle doit être un multiple de **4096**.

Pour simplifier, Cisco propose aussi des commandes prédéfinies :

- **Forcer comme Root Primary** (priorité la plus basse) :
  ```bash
  Switch(config)# spanning-tree vlan [vlan-id] root primary
  ```
- **Configurer un Root secondaire** (priorité légèrement plus haute) :
  ```bash
  Switch(config)# spanning-tree vlan [vlan-id] root secondary
  ```

---

### 3. **Activer Rapid Spanning Tree (RSTP)**
RSTP (802.1w) est plus rapide que STP classique. Vous pouvez l'activer avec cette commande :

```bash
Switch(config)# spanning-tree mode rapid-pvst
```

---

### 4. **Configurer un port comme Edge (PortFast)**
Les ports connectés à des terminaux (PC, serveurs) ne doivent pas passer par tous les états du STP (Blocking, Listening, Learning). Vous pouvez activer **PortFast** pour ces ports :

```bash
Switch(config)# interface [interface-id]
Switch(config-if)# spanning-tree portfast
```

Pour l’appliquer à plusieurs interfaces simultanément :

```bash
Switch(config)# interface range [interface-id début] - [interface-id fin]
Switch(config-if-range)# spanning-tree portfast
```

> ⚠️ Ne jamais activer PortFast sur des ports connectés à d’autres switches !

---

### 5. **Protection contre les erreurs STP**
Pour sécuriser le réseau contre des problèmes liés à STP (comme des Root Bridges non autorisés), configurez des mécanismes de protection :

#### a. **BPDU Guard** (désactive un port recevant des BPDUs)
```bash
Switch(config)# interface [interface-id]
Switch(config-if)# spanning-tree bpduguard enable
```

#### b. **Root Guard** (empêche un port de devenir le Root Bridge)
```bash
Switch(config)# interface [interface-id]
Switch(config-if)# spanning-tree guard root
```

#### c. **Loop Guard** (protège contre les boucles dans des cas spécifiques)
```bash
Switch(config)# spanning-tree loopguard default
```

---

### 6. **Vérifications après configuration**
Pour confirmer que tout est bien configuré, utilisez les commandes suivantes :

- Afficher l'état du STP :
  ```bash
  Switch# show spanning-tree
  ```

- Vérifier les Root Ports et les Designated Ports :
  ```bash
  Switch# show spanning-tree detail
  ```

- Vérifier les ports avec PortFast activé :
  ```bash
  Switch# show spanning-tree interface [interface-id] portfast
  ```

- Vérifier les protections comme BPDU Guard :
  ```bash
  Switch# show spanning-tree summary
  ```

---

### Exemple complet
Voici une configuration complète sur un switch :
```bash
Switch> enable
Switch# configure terminal

! Activer RSTP
Switch(config)# spanning-tree mode rapid-pvst

! Définir le switch comme Root Bridge pour le VLAN 10
Switch(config)# spanning-tree vlan 10 root primary

! Activer PortFast sur les ports connectés aux terminaux
Switch(config)# interface range GigabitEthernet0/1 - 4
Switch(config-if-range)# spanning-tree portfast
Switch(config-if-range)# spanning-tree bpduguard enable

! Sécuriser le réseau avec Root Guard
Switch(config)# interface GigabitEthernet0/5
Switch(config-if)# spanning-tree guard root

! Sauvegarder la configuration
Switch(config)# end
Switch# write memory
```

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
        SW1 (root bridge VLAN 10)
        /                         \
      SW2 -------- redundant ----- SW3
      (STP blocks one redundant path)
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

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst9300/software/release/17-6/configuration_guide/lyr2/b_176_lyr2_9300_cg/configuring_spanning_tree_protocol.html)


## Lab associé

Mettre ces notions en pratique : [Lab 08](../07-Labs/08-STP-EtherChannel/README.md). Statut : `REQUIRES LAB VALIDATION`.
