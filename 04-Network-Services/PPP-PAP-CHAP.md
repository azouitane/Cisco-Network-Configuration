# 🔹 **Configuration du protocole PPP avec PAP & CHAP sur un routeur Cisco**  

## 📌 **1. Introduction à PPP**  
**PPP (Point-to-Point Protocol)** est un protocole de couche 2 utilisé pour établir une connexion point à point entre deux routeurs via une liaison série ou un lien encapsulé. Il prend en charge des fonctionnalités avancées comme :
- **Authentification (PAP et CHAP)**
- **Compression des données**
- **Encapsulation multi-protocoles**
- **Détection des erreurs**

Dans cette configuration, nous verrons comment **configurer PPP avec l'authentification PAP et CHAP** sur un routeur Cisco.

---

## 📌 **2. Différences entre PAP et CHAP**  

| **Méthode** | **PAP (Password Authentication Protocol)** | **CHAP (Challenge Handshake Authentication Protocol)** |
|------------|----------------------------------|-----------------------------------|
| **Sécurité** | Mot de passe transmis en clair sur la liaison | Challenge-réponse fondé sur un secret partagé; ne fournit pas le chiffrement du lien |
| **Authentification** | Unidirectionnelle (le client s'authentifie auprès du serveur) | Bidirectionnelle (challenge-réponse mutuel) |
| **Fréquence** | Une seule fois à la connexion | Périodiquement pendant la session |
| **Vulnérabilités** | Vulnérable aux attaques d'écoute | Protégé contre le rejeu et l’interception |

---

## 📌 **3. Topologie pour la Configuration**  
Nous avons **deux routeurs** connectés via une liaison série (**Serial 0/0/0** sur chaque routeur) :

```
[R1] -------- [R2]
```

---

## 📌 **4. Configuration de PPP avec PAP**
### **🔹 Étape 1 : Définir les Identifiants sur Chaque Routeur**
Sur **R1** :
```bash
R1(config)# username R2 password <SECRET_PPP>  # Définir un utilisateur pour R2
```
Sur **R2** :
```bash
R2(config)# username R1 password <SECRET_PPP>  # Définir un utilisateur pour R1
```

---

### **🔹 Étape 2 : Configurer l'Interface Série avec PPP et PAP**
Sur **R1** :
```bash
R1(config)# interface Serial0/0/0
R1(config-if)# encapsulation ppp  # Activer PPP sur l'interface
R1(config-if)# ppp authentication pap  # Activer l'authentification PAP
R1(config-if)# ppp pap sent-username R1 password <SECRET_PPP>  # Envoyer l'identifiant et le mot de passe
R1(config-if)# no shutdown
```
Sur **R2** :
```bash
R2(config)# interface Serial0/0/0
R2(config-if)# encapsulation ppp
R2(config-if)# ppp authentication pap
R2(config-if)# ppp pap sent-username R2 password <SECRET_PPP>
R2(config-if)# no shutdown
```

---

## 📌 **5. Configuration de PPP avec CHAP**
CHAP est **plus sécurisé** car il utilise une méthode de hachage pour l’authentification.

### **🔹 Étape 1 : Définir les Identifiants sur Chaque Routeur**
Sur **R1** :
```bash
R1(config)# username R2 password <SECRET_PPP>
```
Sur **R2** :
```bash
R2(config)# username R1 password <SECRET_PPP>
```

---

### **🔹 Étape 2 : Configurer l'Interface Série avec PPP et CHAP**
Sur **R1** :
```bash
R1(config)# interface Serial0/0/0
R1(config-if)# encapsulation ppp  # Activer PPP
R1(config-if)# ppp authentication chap  # Activer CHAP
R1(config-if)# no shutdown
```
Sur **R2** :
```bash
R2(config)# interface Serial0/0/0
R2(config-if)# encapsulation ppp
R2(config-if)# ppp authentication chap
R2(config-if)# no shutdown
```

---

## 📌 **6. Vérification et Dépannage**
### **🔹 Vérifier que PPP est actif**
```bash
R1# show interfaces Serial0/0/0
```
Si PPP fonctionne correctement, la sortie devrait inclure :
```
Encapsulation PPP, LCP Open
```

---

### **🔹 Vérifier l'authentification PPP**
N’utiliser les commandes `debug` que brièvement, en fenêtre de maintenance si nécessaire; elles peuvent charger le plan de contrôle et afficher des informations sensibles. Arrêter avec `undebug all`.
```bash
R1# debug ppp authentication
```
Si l'authentification échoue, assure-toi que :
- Les **noms d'utilisateur et mots de passe** sont identiques sur les deux routeurs.
- L’option **ppp authentication pap** ou **ppp authentication chap** est bien activée.
- L’interface série est bien **up (no shutdown)**.

---

## ✅ **Résumé des Commandes Importantes**
| **Commande** | **Description** |
|--------------|-----------------|
| `encapsulation ppp` | Active PPP sur une interface |
| `ppp authentication pap` | Active l’authentification PAP |
| `ppp authentication chap` | Active l’authentification CHAP |
| `ppp pap sent-username <nom> password <mdp>` | Envoie le nom d’utilisateur et le mot de passe pour PAP |
| `username <nom> password <mdp>` | Définit un utilisateur pour l’authentification PAP/CHAP |
| `show interfaces Serial0/0/0` | Vérifie l’état de l’interface série |
| `debug ppp authentication` | Débogue l’authentification PPP |

---

## 🎯 **Conclusion**
- **PAP** est simple mais **peu sécurisé** (envoie le mot de passe en clair).
- **CHAP** est **plus sécurisé** (utilise un challenge-réponse).
- **PPP** est un protocole de **couche 2 puissant** qui permet l'authentification et la compression des données.

---

## Repères de lecture

> Statut : `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Aucune validation de laboratoire n’est documentée.

### Prérequis et vérification

Confirmer le modèle, la version logicielle, la fonctionnalité/licence disponible et le rôle de chaque interface avant d’utiliser les exemples. Utiliser les commandes `show` de l’article pour observer l’état opérationnel; une configuration présente dans `running-config` ne prouve pas à elle seule le fonctionnement attendu.

### Dépannage et sécurité

Vérifier d’abord l’état des interfaces, l’activation globale et locale du protocole, puis les deux extrémités et les compteurs. Réduire l’exposition des informations et services au strict nécessaire; désactiver la fonction sur les ports où elle n’est pas requise.

### Exercice d’étude

Représenter les équipements concernés, noter les paramètres à faire correspondre, puis définir une observation vérifiable pour le cas nominal et un symptôme de panne. L’exercice est à réaliser dans un laboratoire isolé; aucun résultat d’exécution n’est fourni ici.

### Référence officielle

- [Cisco — documentation pertinente](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/bbdsl/configuration/xe-16/bba-xe-16-book/bba-ppp-over-ethernet.html)
