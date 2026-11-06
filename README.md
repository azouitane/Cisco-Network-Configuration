# Cisco Networking Knowledge Base

**Base de connaissances Cisco Networking / Cisco Networking Knowledge Base**

Ressources bilingues de formation et de référence sur Cisco IOS/IOS XE, la commutation, le routage, les services et la sécurité. Les articles principaux sont rédigés en français; les commandes et termes normalisés restent en anglais.

This repository provides learning notes and practical, text-based labs for Cisco networking topics. The main articles are in French; command syntax and standard technical terms remain in English.

## Table des matières / Contents

- [Objectifs et contenu / Goals and scope](#objectifs-et-contenu)
- [Parcours d’apprentissage / Learning path](#parcours-dapprentissage)
- [Documentation théorique / Theory articles](#documentation-théorique)
- [Labs pratiques / Practical labs](#labs-pratiques)
- [Structure du dépôt / Repository structure](#structure-du-dépôt)
- [Installation et utilisation / Setup and usage](#installation-et-utilisation)
- [Contribuer / Contributing](#contribuer)
- [Statut de validation / Validation status](#statut-de-validation)
- [Sécurité et licence / Security and license](#sécurité-et-licence)
- [Références officielles / Official references](#références-officielles)

## Objectifs et contenu

*Goals and scope*

Le projet vise à aider les étudiants réseau, candidats CCNA/CCNP et administrateurs à comprendre les concepts, lire des exemples IOS et pratiquer dans un environnement isolé. Il couvre les bases, VLAN et trunks, STP, EtherChannel, routage statique, OSPF, BGP, DHCP, IPv6, NAT/PAT, ACL, VPN, découverte de voisins, Syslog, PPP, VoIP/CME et haute disponibilité.

The project helps networking students, CCNA/CCNP candidates, and administrators understand concepts, inspect IOS examples, and practice in an isolated environment. It covers switching, routing, services, security, and high availability.

## Parcours d’apprentissage

*Learning path*

1. Lire les [bases](01-Basics/Basic-Router-Switch-Configuration.md).
2. Étudier les [articles de commutation](#commutation), puis pratiquer [Lab 02](07-Labs/02-VLAN-InterVLAN/README.md) et [Lab 08](07-Labs/08-STP-EtherChannel/README.md).
3. Comparer [routage statique](03-Routing/Static-Routing.md), [OSPF](03-Routing/OSPF.md) et [BGP](03-Routing/BGP.md); suivre les Labs [03](07-Labs/03-Static-Routing/README.md) et [04](07-Labs/04-OSPF/README.md).
4. Étudier les [services réseau](#services-réseau) et les Labs [05](07-Labs/05-NAT-PAT/README.md) et [07](07-Labs/07-DHCP/README.md).
5. Terminer par la [sécurité](#sécurité) et [Lab 06](07-Labs/06-ACL-Security/README.md).

## Documentation théorique

*Theory articles*

### Bases

- [Configuration de base routeur et switch / Basic router and switch configuration](01-Basics/Basic-Router-Switch-Configuration.md)

### Commutation

- [Inter-VLAN Routing](02-Switching/Inter-VLAN-Routing.md)
- [STP](02-Switching/STP.md)
- [EtherChannel](02-Switching/EtherChannel.md)
- [VTP et DTP](02-Switching/VTP-DTP.md)
- [Port Security](02-Switching/Port-Security.md)
- [CDP et LLDP](02-Switching/CDP-LLDP.md)

### Routage

- [Routage statique IPv4/IPv6 / Static routing](03-Routing/Static-Routing.md)
- [OSPF](03-Routing/OSPF.md)
- [BGP](03-Routing/BGP.md)
- [SLAAC](03-Routing/SLAAC.md)

### Services réseau

- [DHCPv4](04-Network-Services/DHCPv4.md)
- [DHCPv6](04-Network-Services/DHCPv6.md)
- [NAT](04-Network-Services/NAT.md)
- [PAT](04-Network-Services/PAT.md)
- [Syslog et horodatage / Syslog and timestamps](04-Network-Services/Syslog-Timestamps.md)
- [PPP, PAP et CHAP](04-Network-Services/PPP-PAP-CHAP.md)
- [VoIP et CME](04-Network-Services/VoIP-CME.md)

### Sécurité

- [ACL](05-Security/ACL.md)
- [DHCP Snooping](05-Security/DHCP-Snooping.md)
- [VPN](05-Security/VPN.md)

### Haute disponibilité

- [HSRP, VRRP et GLBP](06-High-Availability/HSRP-VRRP-GLBP.md)

## Labs pratiques

*Practical labs*

Les labs sont progressifs et fournissent topologies et configurations texte par équipement. Commencez par [l’index des labs](07-Labs/README.md).

| Lab | Niveau | Thème |
|---|---|---|
| [01 — Réseau de base](07-Labs/01-Basic-Network/README.md) | Débutant | Adressage, routes, SSH |
| [02 — VLAN et Inter-VLAN](07-Labs/02-VLAN-InterVLAN/README.md) | Débutant + | VLAN, trunk, router-on-a-stick |
| [03 — Routage statique](07-Labs/03-Static-Routing/README.md) | Intermédiaire | Routes spécifiques et défaut |
| [04 — OSPF](07-Labs/04-OSPF/README.md) | Intermédiaire | OSPFv2 area 0 |
| [05 — NAT/PAT](07-Labs/05-NAT-PAT/README.md) | Intermédiaire | Réseau externe simulé |
| [06 — ACL et sécurité](07-Labs/06-ACL-Security/README.md) | Intermédiaire | Filtrage inter-VLAN |
| [07 — DHCP](07-Labs/07-DHCP/README.md) | Débutant + | Pools et relay |
| [08 — STP/EtherChannel](07-Labs/08-STP-EtherChannel/README.md) | Intermédiaire | Redondance L2 et LACP |

## Structure du dépôt

*Repository structure*

```text
01-Basics/                 Basic-Router-Switch-Configuration.md
02-Switching/              CDP-LLDP, EtherChannel, Inter-VLAN-Routing,
                            Port-Security, STP, VTP-DTP
03-Routing/                BGP, OSPF, SLAAC, Static-Routing
04-Network-Services/       DHCPv4, DHCPv6, NAT, PAT, PPP-PAP-CHAP,
                            Syslog-Timestamps, VoIP-CME
05-Security/               ACL, DHCP-Snooping, VPN
06-High-Availability/      HSRP-VRRP-GLBP
07-Labs/                   README + 8 labs (README, topology, configs)
.github/ISSUE_TEMPLATE/    4 modèles d’issue
.github/PULL_REQUEST_TEMPLATE.md
AUDIT.md                   Audit initial
PHASE-*-REPORT.md          Rapports des phases
CONTRIBUTING.md            Guide de contribution
SECURITY.md                Signalement de sécurité
CODE_OF_CONDUCT.md         Règles communautaires
CHANGELOG.md               Historique des évolutions
```

## Installation et utilisation

*Setup and usage*

Aucune dépendance logicielle n’est requise pour lire le dépôt. Clonez ou téléchargez le dépôt depuis GitHub, ouvrez les fichiers Markdown, puis choisissez un simulateur ou équipement compatible pour les labs. Dans Packet Tracer, construisez la topologie indiquée, sélectionnez des modèles dont les interfaces/fonctions correspondent aux fichiers de configuration et saisissez les configurations par console. Configurez les PC via leur interface Desktop selon les fichiers `Endpoints.txt` ou `PCs.txt`.

No software dependencies are required to read the repository. Open the Markdown files locally or on GitHub. To practice, build the documented topology in a compatible simulator, map the sample interfaces to the selected device models, and configure end hosts from the endpoint notes.

## Contribuer

*Contributing*

Les corrections techniques, explications et nouveaux sujets sont bienvenus. Consultez [CONTRIBUTING.md](CONTRIBUTING.md), choisissez le modèle approprié dans [GitHub Issues](.github/ISSUE_TEMPLATE/) ou ouvrez une Pull Request. English: see the same contribution guide and submit changes through a GitHub issue or pull request.

## Statut de validation

*Validation status*

Les pages et configurations ont fait l’objet de revues statiques à des degrés divers. Les labs portent `REQUIRES LAB VALIDATION`; aucun n’est marqué `LAB VALIDATED`. Aucune exécution IOS/IOS XE ou Packet Tracer n’est revendiquée. Les commandes peuvent dépendre du modèle, des licences et de la version logicielle; vérifiez les guides officiels adaptés à votre plateforme.

Documentation and configurations have undergone static review to varying degrees. Every practical lab requires hands-on validation. No IOS/IOS XE or Packet Tracer execution is claimed. Commands and features may vary by hardware, license, and software release.

## Sécurité et licence

*Security and license*

Utilisez les exemples uniquement dans un environnement de test isolé. Ne committez jamais de mots de passe, clés privées, adresses de gestion sensibles ou configurations client. Remplacez tous les placeholders localement et supprimez les secrets des extraits avant de les partager. Signalez une vulnérabilité selon [SECURITY.md](SECURITY.md).

No `LICENSE` file is present. The repository does not grant an assumed reuse license; public visibility alone does not authorize unrestricted reuse. Contact the repository owner for licensing permission. Cisco and protocol references remain subject to their respective owners’ terms.

## Références officielles

*Official references*

- [Cisco IOS and IOS XE configuration guides](https://www.cisco.com/c/en/us/support/ios-nx-os-software/ios/series.html)
- [Cisco command references](https://www.cisco.com/c/en/us/support/ios-nx-os-software/ios/products-command-reference-list.html)
- [Cisco Learning Network](https://learningnetwork.cisco.com/)
- Standards references are linked from relevant articles where available.
