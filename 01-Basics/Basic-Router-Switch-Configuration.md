**commandes de base** pour la **configuration d’un switch et d’un routeur Cisco** en ligne de commande (CLI), utiles dans un contexte d’apprentissage, de laboratoire ou d’administration réseau :

---

### 🔌 1. Configuration d’un **Switch Cisco**

#### ➤ Accès au switch
```bash
Switch> enable
Switch# configure terminal
```

#### ➤ Renommer le switch
```bash
Switch(config)# hostname Switch1
```

#### ➤ Désactiver la recherche DNS (évite les lenteurs si commande erronée)
```bash
Switch1(config)# no ip domain-lookup
```

#### ➤ Configuration d’un mot de passe pour l’accès en mode privilégié
```bash
Switch1(config)# enable secret <SECRET_ADMIN>
```

#### ➤ Configuration de la ligne console
```bash
Switch1(config)# line console 0
Switch1(config-line)# password <SECRET_CONSOLE>
Switch1(config-line)# login
Switch1(config-line)# exit
```

#### ➤ Configuration SSH des lignes VTY (pas de Telnet)
```bash
Switch1(config)# line vty 0 4
Switch1(config-line)# login local
Switch1(config-line)# transport input ssh
Switch1(config-line)# exit
```

### 🔐 **Configuration de l’accès SSH sur un switch  Cisco**

#### 1. 🌍 Définir un nom d’hôte et un nom de domaine
```bash
Switch(config)# hostname Switch1
Switch1(config)# ip domain-name monreseau.local
```

#### 2. 👤 Créer un utilisateur avec mot de passe
```bash
Switch1(config)# username admin privilege 15 secret <SECRET_ADMIN>
```

#### 3. 🔑 Générer les clés RSA
```bash
Switch1(config)# crypto key generate rsa
# Si demandé, entrer une taille (taille à choisir selon les recommandations et capacités de la plateforme; éviter les clés faibles)
How many bits in the modulus [512]: <TAILLE_ADAPTEE_A_LA_PLATEFORME>
```

#### 4. 🧑‍💻 Activer SSH sur les lignes VTY
```bash
Switch1(config)# line vty 0 4
Switch1(config-line)# transport input ssh
Switch1(config-line)# login local
Switch1(config-line)# exit
```

#### 5. 🛡️ (Optionnel mais recommandé) Limiter la version SSH à la version 2
```bash
Switch1(config)# ip ssh version 2
```

#### 6. 💾 Enregistrer la configuration
```bash
Switch1# copy running-config startup-config
```

---

### ✅ Vérification

- Pour voir si SSH est activé :
```bash
Switch1# show ip ssh
```

- Pour tester la connexion SSH depuis un autre hôte :
```bash
ssh admin@192.168.1.2
```

#### ➤ Configuration d’une adresse IP sur une interface VLAN
```bash
Switch1(config)# interface vlan 1
Switch1(config-if)# ip address 192.168.1.2 255.255.255.0
Switch1(config-if)# no shutdown
Switch1(config-if)# exit
```

#### ➤ Définir la passerelle par défaut (switch de couche 2)
```bash
Switch1(config)# ip default-gateway 192.168.1.1
```

#### ➤ Enregistrer la configuration
```bash
Switch1# write memory
# ou
Switch1# copy running-config startup-config
```

---

### 🌐 2. Configuration d’un **Routeur Cisco**

#### ➤ Accès au routeur
```bash
Router> enable
Router# configure terminal
```

#### ➤ Renommer le routeur
```bash
Router(config)# hostname R1
```

**configuration complète, claire et sécurisée** pour activer l’**accès SSH sur un routeur Cisco**. C’est très similaire à un switch, avec quelques ajustements.

## 🔧 **Configuration complète de l’accès SSH sur un routeur Cisco**

---

### 1️⃣ **Configurer le nom d’hôte et le nom de domaine**
```bash
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# ip domain-name monreseau.local
```

---

### 2️⃣ **Créer un utilisateur avec mot de passe sécurisé**
```bash
R1(config)# username admin privilege 15 secret <SECRET_ADMIN>
```

---

### 3️⃣ **Générer les clés RSA pour SSH**
```bash
R1(config)# crypto key generate rsa
How many bits in the modulus [512]: <TAILLE_ADAPTEE_A_LA_PLATEFORME>
```

> ⚠️ La taille de clé doit respecter les recommandations de sécurité et les capacités de la plateforme; ne pas reprendre une valeur faible par défaut.

---

### 4️⃣ **Activer SSH sur les lignes VTY**
```bash
R1(config)# line vty 0 4
R1(config-line)# login local
R1(config-line)# transport input ssh
R1(config-line)# exit
```

---

### 5️⃣ **Forcer l’utilisation de SSH version 2**
```bash
R1(config)# ip ssh version 2
```

---

### 6️⃣ **Configurer une adresse IP sur une interface (ex: G0/0)**
```bash
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
```

---

### 7️⃣ **(Optionnel) Configurer la passerelle par défaut**
```bash
R1(config)# ip default-gateway 192.168.1.254
```

---

### 8️⃣ **Sauvegarder la configuration**
```bash
R1# copy running-config startup-config
```

---

## ✅ Vérification

- Vérifier que SSH est actif :
```bash
R1# show ip ssh
```

- Vérifier l’état des interfaces :
```bash
R1# show ip interface brief
```

---

## 🔐 Connexion SSH depuis un poste client :

```bash
ssh admin@192.168.1.1
```

#### ➤ Configuration de l’interface (exemple : GigabitEthernet 0/0)
```bash
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
```

#### ➤ Configuration d’une autre interface (ex : réseau WAN)
```bash
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 10.0.0.1 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit
```

#### ➤ Ajouter une route statique
```bash
R1(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

#### ➤ Configuration de NAT/PAT (selon contexte)
```bash
# Exemple simplifié
R1(config)# ip nat inside source list 1 interface gigabitEthernet 0/1 overload
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip nat inside
R1(config-if)# exit
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip nat outside
```

#### ➤ Enregistrer la configuration
```bash
R1# write memory
# ou
R1# copy running-config startup-config
```

---

### 🛠️ Astuce
Pour **vérifier** les configurations :
```bash
# Voir les interfaces
show ip interface brief

# Voir la configuration en cours
show running-config

# Voir la table de routage
show ip route
```

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Topologie de référence

```text
[PC d’administration] -- [SW1, SVI de gestion] -- [R1, passerelle]
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

- [Cisco — documentation de configuration pertinente](https://www.cisco.com/c/en/us/support/docs/security-vpn/secure-shell-ssh/4145-ssh.html)


## Lab associé

Mettre ces notions en pratique : [Lab 01](../07-Labs/01-Basic-Network/README.md). Statut : `REQUIRES LAB VALIDATION`.
