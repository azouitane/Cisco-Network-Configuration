La configuration d'un serveur **DHCP** sur un routeur **Cisco** permet d'attribuer automatiquement des adresses IP aux clients du réseau. Voici les étapes générales pour configurer un serveur **DHCP** sur un routeur **Cisco**.

---

### 📌 **1. Accéder au mode de configuration**
Tout d'abord, connectez-vous au routeur via **console, SSH ou Telnet**, puis passez en mode de configuration globale :

```bash
 Router> enable
 Router# configure terminal
```

---

### 📌 **2. Créer un pool DHCP**
Un pool DHCP définit une plage d'adresses IP à attribuer aux clients :

```bash
 Router(config)#ip dhcp pool NOM_DU_POOL
 Router(dhcp-config)#network 192.168.1.0 255.255.255.0  # Plage d'adresses IP
 Router(dhcp-config)#default-router 192.168.1.1         # Passerelle par défaut
 Router(dhcp-config)#dns-server 8.8.8.8 8.8.4.4         # Serveurs DNS
 Router(dhcp-config)#lease 7                            # Durée du bail en jours (ici, 7 jours)
```

---

### 📌 **3. Exclure certaines adresses IP**
Si certaines adresses doivent être **réservées** (pour des serveurs, imprimantes, etc.), on peut les exclure du pool :

```bash
 Router(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.10
```
Ici, les adresses **192.168.1.1 à 192.168.1.10** ne seront **pas attribuées** par le DHCP.

---

### 📌 **4. Activer le service DHCP**
Le service DHCP est activé par défaut sur de nombreuses versions IOS. Si le service a explicitement été désactivé, `service dhcp` se configure en mode global (et non dans le sous-mode `dhcp-config`) :

```bash
 Router(config)#service dhcp
```

---

### 📌 **5. Vérification et dépannage**
Pour vérifier que le DHCP fonctionne correctement :

✅ **Afficher les baux DHCP attribués :**
```bash
 Router#show ip dhcp binding
```

✅ **Afficher l'état du serveur DHCP :**
```bash
 Router#show ip dhcp server statistics
```

✅ **Vérifier les logs en cas de problème :**
```bash
 Router#debug ip dhcp server events
```

---

### 📌 **6. Sauvegarder la configuration**
Une fois la configuration terminée et testée, enregistrez-la pour éviter de la perdre après un redémarrage :

```bash
 Router#write memory
```
ou
```bash
 Router#copy running-config startup-config
```

---

### 🔥 **Exemple Complet**
Si vous voulez un exemple concret, voici une configuration prête à l'emploi :

```bash
 Router> enable
 Router# configure terminal

 Router(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.10
 Router(config)#ip dhcp pool LAN_POOL
 Router(dhcp-config)#network 192.168.1.0 255.255.255.0
 Router(dhcp-config)#default-router 192.168.1.1
 Router(dhcp-config)#dns-server 8.8.8.8 8.8.4.4
 Router(dhcp-config)#lease 7

 Router(dhcp-config)#exit
 Router(config)#service dhcp  # Seulement si le service a été désactivé
 Router(config)#end
 Router#write memory
```

---

### 🎯 **Conclusion**
Cette configuration permet à un routeur **Cisco** de distribuer dynamiquement des adresses IP aux clients du réseau. Assurez-vous que l'interface qui connecte les clients est bien activée avec **`ip address`** et que les clients sont bien configurés en **DHCP**.

Si tu as des questions ou un scénario spécifique en tête, fais-moi signe ! 🚀

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
Client -- VLAN 10 -- SW1 -- R1 (DHCP pool, 192.168.10.1/24)
Remote VLAN needs a DHCP relay on its gateway.
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

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_dhcp/configuration/xe-16/dhcp-xe-16-book.html)


## Lab associé

Mettre ces notions en pratique : [Lab 07](../07-Labs/07-DHCP/README.md). Statut : `REQUIRES LAB VALIDATION`.
