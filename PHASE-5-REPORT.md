# Rapport de phase 5 — Labs pratiques

## Labs créés

Huit labs progressifs ont été ajoutés dans `07-Labs/`, chacun avec guide, topologie et répertoire de configurations par équipement. Tous portent le statut `REQUIRES LAB VALIDATION`; aucun Packet Tracer n’a été exécuté et aucun fichier `.pkt` ou résultat simulé n’a été créé.

| Lab | Équipements principaux | Contenu des configs |
|---|---|---|
| 01 Réseau de base | 2 routeurs, 2 switches, PC | R1, R2, SW1, SW2; accès SSH proposé sous réserve compatibilité RSA |
| 02 VLAN/Inter-VLAN | 1 routeur, 1 switch, PC en VLAN 10/20/30 | R1, SW1, paramètres PC |
| 03 Routage statique | 3 routeurs, 3 LAN et PC | R1, R2, R3, paramètres PC; routes spécifiques, défaut sur R1 |
| 04 OSPF | 3 routeurs, 3 LAN et PC | R1, R2, R3, paramètres PC; OSPFv2 area 0 |
| 05 NAT/PAT | Routeur edge, routeur ISP, PC, serveur | R1, ISP, paramètres des extrémités |
| 06 ACL/Sécurité | Router-on-a-stick, switch, VLAN 10/20/30, hôtes | R1, SW1, paramètres endpoints; filtrage TCP/80 invité vers serveur |
| 07 DHCP | 2 routeurs, switchs et clients | R1 serveur DHCP, R2 relay, paramètres clients |
| 08 STP/EtherChannel | 3 switches, LACP et liens redondants | SW1, SW2, SW3, plan de raccordement |

## Documentation et intégration

Chaque guide couvre objectifs, difficulté, prérequis, compatibilité Packet Tracer, topologie, adressage, mise en œuvre, explication, commandes de contrôle, résultats attendus, tests, trois scénarios génériques de dépannage, exercices et corrigés. Les liens vers les articles théoriques existants ont été ajoutés. Le README racine inclut les niveaux et le parcours des labs.

## Compatibilité et validation

Les configs emploient une syntaxe IOS courante de type Catalyst/router, mais les noms d’interfaces, versions, commandes RSA, fonctions L2 et comportements du simulateur dépendent du modèle. Elles ont été relues statiquement uniquement. Les commandes de génération RSA sont annotées comme dépendantes de l’image; valider avant saisie. Les préfixes TEST-NET du lab NAT servent uniquement à la documentation/labo isolé. Aucun déploiement réel n’est recommandé.

## Limites restantes

- Le lab de base montre deux routeurs pour démontrer le routage de bout en bout, en supplément du besoin minimal.
- Le routage statique n’a pas d’article théorique autonome; le lab pointe vers les notions de base qui contiennent les routes statiques.
- Les scénarios STP/EtherChannel dépendent du nombre/nom des ports; vérifier que les quatre ports du port-channel sont disponibles et compatibles.
- Les guides donnent des résultats fonctionnels attendus, pas des sorties CLI observées.
- DHCP utilise un serveur IOS R1 avec un pool distant servi par relay; selon la plateforme, le relais doit être accepté et le routage retour correctement établi.
- ACL de lab illustre une politique étroite, mais les accès des autres VLANs et les services du serveur sont à adapter au scénario voulu.
- Aucun lab DHCPv6, BGP, VPN, FHRP, VoIP ou Port Security distinct n’est créé dans cette phase; ces thèmes ne faisaient pas partie des huit scénarios demandés.

## Validation finale

Les liens Markdown relatifs sont vérifiés statiquement; aucune cible interne manquante n’est connue. Les configs sont séparées par appareil et aucune dépendance logicielle externe n’a été ajoutée. Aucun des 21 articles théoriques n’a été supprimé. Aucun commit ou push n’a été effectué. La phase 6 n’a pas commencé.
