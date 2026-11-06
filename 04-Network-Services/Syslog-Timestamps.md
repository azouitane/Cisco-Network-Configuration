# 🕒 **Configuration de l’horodatage Syslog sur un routeur Cisco**  

L’horodatage Syslog permet d’ajouter des **timestamps** précis aux logs des équipements réseau pour mieux analyser et corréler les événements. Par défaut, les logs sur un routeur ou switch Cisco peuvent **ne pas afficher l’heure exacte**, ce qui complique le diagnostic des problèmes.

---

## ✅ **1. Activer l’horodatage des logs**  

Par défaut, les logs sont générés sans indication de date/heure. Pour activer l’horodatage avec la date et l’heure précises :  

```bash
R1(config)# service timestamps log datetime msec
```
📌 **Explication** :  
- `service timestamps log datetime` → Ajoute la date et l’heure aux logs.  
- `msec` → Ajoute les millisecondes pour plus de précision.  

---

## ✅ **2. Synchroniser l’horloge avec NTP** (Optionnel mais recommandé)  

Pour garantir des timestamps précis, synchroniser l’heure du routeur avec un serveur **NTP (Network Time Protocol)** est essentiel.  

### **🔹 Configurer NTP sur le routeur**
```bash
R1(config)# ntp server 192.168.1.1
```
📌 **Explication** : 192.168.1.1 est l’IP du serveur NTP interne. Si tu veux utiliser un serveur NTP public :  
```bash
R1(config)# ntp server 0.pool.ntp.org
```

### **🔹 Vérifier la synchronisation NTP**
```bash
R1# show ntp status
```

---

## ✅ **3. Configurer le fuseau horaire**  

Si le routeur affiche une heure incorrecte, il faut définir le bon fuseau horaire :  

### **🔹 Exemple pour Paris (UTC+1, avec heure d’été)**
```bash
R1(config)# clock timezone CET 1
R1(config)# clock summer-time CEST recurring
```
📌 **Explication** :  
- `clock timezone CET 1` → Définit le fuseau horaire en UTC+1.  
- `clock summer-time CEST recurring` → Active l’heure d’été automatiquement.  

---

## ✅ **4. Configurer le logging vers un serveur Syslog distant**  

Si tu veux envoyer les logs vers un **serveur Syslog centralisé**, configure ceci :  

```bash
R1(config)# logging 192.168.1.100
R1(config)# logging trap informational
R1(config)# logging on
```
📌 **Explication** :  
- `logging 192.168.1.100` → Définit l’adresse IP du serveur Syslog.  
- `logging trap informational` → Envoie uniquement les logs de niveau "informational" et plus graves.  
- `logging on` → Active l’envoi des logs.  

### **🔹 Vérifier les logs envoyés**
```bash
R1# show logging
```

---

## 📌 **Résumé des commandes**
| **Commande** | **Description** |
|--------------|-----------------|
| `service timestamps log datetime msec` | Active l’horodatage des logs avec millisecondes |
| `ntp server <IP/Nom>` | Configure un serveur NTP |
| `clock timezone <Nom> <Décalage UTC>` | Définit le fuseau horaire |
| `clock summer-time <Nom> recurring` | Active l’heure d’été automatiquement |
| `logging <IP>` | Envoie les logs vers un serveur Syslog |
| `logging trap <niveau>` | Définit le niveau de log à envoyer |
| `show logging` | Vérifie les logs |

---

## 🎯 **Conclusion**
Avec cette configuration, ton routeur **affichera des logs horodatés avec précision** et les enverra vers un serveur Syslog distant si besoin. **C’est essentiel pour le troubleshooting et la sécurité du réseau !** 🚀

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

- [Cisco — documentation pertinente](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/fundamentals/configuration/xe-16/fundamentals-xe-16-book/sys-mgmt.html)
