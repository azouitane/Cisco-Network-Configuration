# Rapport de phase 2.1 — Nettoyage structurel

## Modifications

- Renommé `05-High-Availability/` en `06-High-Availability/` pour obtenir une numérotation unique et séquentielle : `01-Basics`, `02-Switching`, `03-Routing`, `04-Network-Services`, `05-Security`, `06-High-Availability`.
- Mis à jour le lien HSRP/VRRP/GLBP dans `README.md` pour viser le nouveau chemin.
- Aucune configuration Cisco ni explication technique n’a été modifiée.

## Fichier Markdown supplémentaire signalé en phase 2

Le rapport de phase 2 annonçait 24 fichiers Markdown à la validation, mais sa propre ventilation en annonçait 23. L’inventaire courant, en excluant `.git`, compte 23 fichiers : 20 documents de contenu issus du dépôt initial (le README d’origine étant conservé comme article de base), le README d’index, `AUDIT.md`, `PHASE-2-REPORT.md` et le présent rapport de phase 2.1. Cela totalise en fait 24 après ajout du présent rapport; avant sa création, il y en avait 23.

Le fichier qui pouvait être interprété comme « supplémentaire » est donc `AUDIT.md`. Il s’agit du livrable d’audit de phase 1, pas d’un doublon ni d’un article technique. Il reste à la racine, où sa fonction transversale est appropriée. Aucun fichier Markdown sans rôle n’a été trouvé. La différence de comptage antérieure venait de la formulation erronée du rapport de phase 2, qui annonçait 23 tout en comptant séparément 20 contenus, README, audit et rapport (24). Le présent rapport clarifie ce décompte.

## Structure vérifiée

```text
README.md
AUDIT.md
PHASE-2-REPORT.md
PHASE-2.1-REPORT.md
01-Basics/
  Basic-Router-Switch-Configuration.md
02-Switching/
  CDP-LLDP.md
  EtherChannel.md
  Inter-VLAN-Routing.md
  Port-Security.md
  STP.md
  VTP-DTP.md
03-Routing/
  BGP.md
  OSPF.md
  SLAAC.md
04-Network-Services/
  DHCPv4.md
  DHCPv6.md
  NAT.md
  PAT.md
  PPP-PAP-CHAP.md
  Syslog-Timestamps.md
  VoIP-CME.md
05-Security/
  ACL.md
  DHCP-Snooping.md
  VPN.md
06-High-Availability/
  HSRP-VRRP-GLBP.md
```

Les noms de fichiers sont cohérents en anglais avec casse kebab/Pascal composée cohérente par thème. Aucun fichier source en double n’a été trouvé; les pages EtherChannel et autres sujets ne sont que présentes une fois.

## Validation

- Navigation du README : tous les liens vers les catégories et articles résolvent vers des fichiers ou ancres présentes.
- Liens Markdown relatifs sur l’ensemble des fichiers Markdown : aucun lien cassé.
- Références d’images ou de fichiers locaux : aucune trouvée.
- Contenus initiaux : les 20 documents restent accessibles, y compris les 19 articles déplacés et le contenu initial du README conservé dans `01-Basics/Basic-Router-Switch-Configuration.md`.
- Fichiers Markdown actuels hors `.git` : 24, comprenant 20 contenus d’origine, README, audit, rapports phase 2 et 2.1.
- Configurations et explications Cisco : non modifiées dans cette phase.
- Changements locaux uniquement; aucun push effectué.

## Éléments restants

Le rapport de phase 2 conserve son décompte historique erroné de 23; ce rapport documente l’écart sans réécrire le livrable antérieur. Aucun travail de phase 3 n’a commencé.
