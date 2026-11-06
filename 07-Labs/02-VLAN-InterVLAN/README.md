# Lab 02 — VLAN et routage inter-VLAN

**Difficulté : débutant. Statut : `REQUIRES LAB VALIDATION`.**

## Objectifs

- Créer les VLAN 10, 20 et 30 et affecter les ports access.
- Configurer un trunk 802.1Q entre SW1 et R1.
- Activer le routage inter-VLAN avec des sous-interfaces sur R1.

## Matériel et compatibilité

Un routeur avec une interface Ethernet compatible 802.1Q, un switch L2 et trois PC. Les noms d’interfaces et la prise en charge du trunk dépendent du modèle et de l’image IOS/IOS XE. Aucun fichier Packet Tracer n’est fourni et le scénario n’a pas été exécuté.

## Topologie et adressage

Le câblage et le plan sont dans [topology.md](topology.md); les IP statiques des PC sont dans `configs/PCs.txt`. Relier PC10/20/30 respectivement à SW1 F0/1/2/3, et SW1 G0/1 à R1 G0/0.

| VLAN | Poste | Sous-réseau | Passerelle |
|---|---|---|---|
| 10 | PC10 | 192.168.10.0/24 | 192.168.10.1 |
| 20 | PC20 | 192.168.20.0/24 | 192.168.20.1 |
| 30 | PC30 | 192.168.30.0/24 | 192.168.30.1 |

## Mise en œuvre

1. Câbler les ports ci-dessus et appliquer `configs/SW1.txt` à SW1.
2. Appliquer `configs/R1.txt` à R1. L’interface physique G0/0 sert de parent aux sous-interfaces.
3. Entrer les adresses des PC depuis `configs/PCs.txt`.
4. Tester chaque passerelle, puis les pings entre VLANs.

## Explication des commandes

Les ports F0/1–F0/3 sont des ports access associés chacun à un VLAN. Le trunk transporte les VLAN 10, 20 et 30 vers le routeur. Sur R1, chaque sous-interface associe `encapsulation dot1Q <vlan-id>` à la passerelle IP du VLAN.

## Vérification et résultats attendus

Sur SW1 : `show vlan brief`, `show interfaces trunk` et `show interfaces status`. Sur R1 : `show ip interface brief` et `show ip route connected`. Attendu : VLAN présents, trunk opérationnel avec VLAN 10/20/30 autorisés et routes connectées pour les trois sous-réseaux. Les pings entre PC doivent être possibles si les configurations et le modèle sont compatibles; aucune sortie n’est présumée.

## Dépannage

- PC ne joint pas sa passerelle : vérifier VLAN d’accès, IP/masque, câblage et état du port.
- Sous-interface inactive : vérifier l’interface physique G0/0 (`no shutdown`) et le lien au switch.
- Un seul VLAN ne passe pas : comparer l’ID dot1Q à l’ID VLAN et contrôler la liste autorisée du trunk.

## Exercices avec corrigés

1. Affecter F0/2 au VLAN 10 par erreur. **Correction :** remettre PC20 dans le VLAN 20 et vérifier `show vlan brief`.
2. Retirer le VLAN 30 de la liste autorisée du trunk. **Correction :** autoriser 10,20,30 sur SW1 G0/1.
3. Configurer la sous-interface VLAN 20 avec le mauvais tag. **Correction :** utiliser l’ID 20 et vérifier l’adresse passerelle 192.168.20.1/24.

## Sécurité et limites

Les ports access ne doivent pas être laissés en négociation dynamique sur un réseau réel; définir explicitement leur rôle et appliquer les contrôles adaptés à l’environnement. Cette configuration pédagogique n’est pas une référence de production.

## Références

- Théorie : [VLAN et routage inter-VLAN](../../02-Switching/Inter-VLAN-Routing.md).
- Cisco : [Inter-VLAN Routing Configuration Guide](https://www.cisco.com/c/en/us/support/docs/lan-switching/inter-vlan-routing/14976-50.html).
