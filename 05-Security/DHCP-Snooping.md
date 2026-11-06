### 🔹 **Configuration de DHCP Snooping sur un Switch Cisco**  

**DHCP Snooping** est une fonctionnalité de sécurité des switches Cisco qui empêche les attaques DHCP, comme le **DHCP Spoofing** (quand un attaquant installe un faux serveur DHCP pour tromper les clients et intercepter leur trafic).  

---

## **📌 1. Fonctionnement de DHCP Snooping**
- **Filtrage des réponses DHCP** : Seuls les ports de confiance peuvent envoyer des réponses DHCP (serveurs DHCP valides).
- **Base de données de liaison DHCP** : Le switch garde une liste des adresses MAC-IP associées aux ports.
- **Protection contre le DHCP Spoofing** : Empêche les faux serveurs DHCP de distribuer des adresses.

---

## **📌 2. Configuration de DHCP Snooping sur un Switch Cisco**
Voici la configuration sur un **switch de niveau 2** connecté à un routeur DHCP.

### **📍 Étape 1 : Activer DHCP Snooping sur le switch**
```bash
Switch# configure terminal
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10
```
✅ **Explication :**
- **`ip dhcp snooping`** → Active la fonctionnalité DHCP Snooping.
- **`ip dhcp snooping vlan 10`** → Active DHCP Snooping uniquement sur le VLAN 10.

---

### **📍 Étape 2 : Définir les ports de confiance**
- Les ports connectés aux **serveurs DHCP** doivent être configurés comme **trusted**.
- Tous les autres ports restent **untrusted** par défaut.

#### ✅ **Configurer le port où est connecté le serveur DHCP**
```bash
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# ip dhcp snooping trust
Switch(config-if)# exit
```

#### ✅ **Configurer les ports où sont connectés les clients**
```bash
Switch(config)# interface range GigabitEthernet0/2 - 10
Switch(config-if)# ip dhcp snooping limit rate 10
Switch(config-if)# exit
```
✅ **Explication :**
- **`ip dhcp snooping trust`** → Définit le port **G0/1** comme **de confiance** (DHCP valide).
- **`ip dhcp snooping limit rate 10`** → Limite le nombre de paquets DHCP à 10 par seconde (protection contre les attaques DHCP Starvation).

---

### **📍 Étape 3 : Vérifier la Configuration**
Après la configuration, on peut vérifier que **DHCP Snooping fonctionne correctement**.

#### ✅ **Vérifier que DHCP Snooping est activé**
```bash
Switch# show ip dhcp snooping
```

#### ✅ **Vérifier la base de données DHCP Snooping (associations MAC-IP)**
```bash
Switch# show ip dhcp snooping binding
```
Tu verras une table contenant :
- L’adresse IP assignée
- L’adresse MAC du client
- Le VLAN et le port où le client est connecté

---

## **📌 3. Protection contre les attaques supplémentaires**
DHCP Snooping est souvent utilisé avec **DAI (Dynamic ARP Inspection)** et **IP Source Guard**.

### ✅ **Activer Dynamic ARP Inspection (DAI)**
Empêche les attaques **ARP Spoofing** en validant les paquets ARP :
```bash
Switch(config)# ip arp inspection vlan 10
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# ip arp inspection trust
Switch(config-if)# exit
```

### ✅ **Activer IP Source Guard**
Empêche un utilisateur de changer d’adresse IP après avoir obtenu une adresse DHCP :
```bash
Switch(config)# interface GigabitEthernet0/2
Switch(config-if)# ip verify source
Switch(config-if)# exit
```

---

## **📌 4. Vérification Complète**
Une fois tout configuré, voici comment tester :
1. **Vérifie que les clients obtiennent bien une adresse IPv4** :
   ```bash
   Switch# show ip dhcp snooping binding
   ```
2. **Teste avec un faux serveur DHCP (sur un port non trusted)** :
   - Il ne doit pas pouvoir envoyer des offres DHCP.

---

## ✅ **Résumé**
| **Fonction** | **Commande** |
|-------------|-------------|
| Activer DHCP Snooping | `ip dhcp snooping` |
| Appliquer DHCP Snooping à un VLAN | `ip dhcp snooping vlan <VLAN>` |
| Définir un port de confiance (DHCP valide) | `ip dhcp snooping trust` |
| Limiter le trafic DHCP sur un port client | `ip dhcp snooping limit rate 10` |
| Vérifier les bindings DHCP | `show ip dhcp snooping binding` |
| Protéger contre ARP Spoofing | `ip arp inspection vlan <VLAN>` |
| Activer IP Source Guard | `ip verify source` |

---

### 🎯 **Conclusion**
Avec **DHCP Snooping**, tu sécurises ton réseau contre les attaques **DHCP Spoofing** et **DHCP Starvation**. En combinant avec **DAI** et **IP Source Guard**, tu obtiens une protection complète contre les attaques de falsification d’IP et ARP.

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
DHCP server -- trusted uplink -- SW1 -- untrusted access ports -- clients
Trust only the verified DHCP server path.
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

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/td/docs/switches/lan/c9000/sec-crypto/fhs-sisf/fhs-and-sisf-configuration-guide/dhcp-snooping.html)
