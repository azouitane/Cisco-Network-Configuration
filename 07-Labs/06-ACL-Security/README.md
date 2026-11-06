# Lab 06 — ACL IPv4 et filtrage inter-VLAN

**Difficulté : intermédiaire. Statut : `REQUIRES LAB VALIDATION`.**

## Objectifs

- Acheminer trois VLAN avec router-on-a-stick.
- Refuser le TCP destination port 80 de VLAN 20 vers le serveur VLAN 30.
- Vérifier les compteurs ACL et distinguer le flux refusé des flux autorisés.

## Matériel et compatibilité

Un routeur avec sous-interfaces 802.1Q, un switch L2, un PC invité et un serveur Web; un PC utilisateur est optionnel. ACL IPv4 nommées et sous-interfaces sont disponibles selon plateforme/image IOS/IOS XE. Aucun test Packet Tracer n’a été exécuté.

## Topologie et adressage

Voir [topology.md](topology.md) et `configs/Endpoints.txt`. Connecter SW1 G0/1 à R1 G0/0; VLAN 10 sur F0/3, VLAN 20 sur F0/1, VLAN 30 sur F0/2.

| VLAN | Rôle | Réseau | Passerelle |
|---|---|---|---|
| 10 | Utilisateurs | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Invités | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Serveurs | 192.168.30.0/24 | 192.168.30.1 |

## Mise en œuvre

1. Câbler conformément à la topologie et configurer les adresses des endpoints.
2. Appliquer `configs/SW1.txt`, puis `configs/R1.txt`.
3. Vérifier VLAN, trunk et sous-interfaces avant les tests.
4. Depuis le réseau invité, tester TCP/80 vers 192.168.30.10 et des flux de contrôle explicitement prévus par l’exercice.

## Explication des commandes

Les sous-interfaces R1 terminées par `.10`, `.20` et `.30` identifient les VLAN avec `encapsulation dot1Q`. L’ACL étendue `GUESTS` est appliquée en entrée sur la sous-interface VLAN 20. Sa première ACE refuse TCP/80 du réseau invité vers l’hôte serveur. La seconde permet le reste du trafic IP invité; cette règle permissive est un choix pédagogique de portée limitée à l’exemple, pas une politique de sécurité générique.

## Vérification et résultats attendus

Sur SW1 : `show vlan brief`, `show interfaces trunk`. Sur R1 : `show ip interface brief`, `show access-lists GUESTS`, `show ip interface GigabitEthernet0/0.20`. Attendu : le compteur de l’ACE deny augmente lors du test TCP/80 correspondant; les autres flux IP invités correspondent à l’ACE permit. La réussite de l’application Web dépend du service serveur et n’est pas présumée.

## Dépannage

- Aucune ACE ne compte : vérifier que l’ACL est attachée en entrée à `.20`, l’adresse source invitée, le protocole/port et le chemin emprunté.
- ACL bloque tout : vérifier l’ordre des ACE et la présence de la règle permit voulue avant le refus implicite final.
- VLAN inaccessible : vérifier affectation access, VLAN autorisés sur le trunk et concordance des tags dot1Q.

## Exercices avec corrigés

1. Retirer l’ACL de `.20`. **Correction :** réappliquer `ip access-group GUESTS in` dans le bon sens.
2. Changer la destination serveur de l’ACE. **Correction :** utiliser 192.168.30.10, conformément au plan.
3. Restreindre également ICMP vers le serveur. **Correction :** ajouter une ACE explicite avant le permit général, puis vérifier séparément ses compteurs et les flux permis.

## Sécurité et limites

L’ACE `permit ip ... any` autorise tous les autres flux IP du réseau invité. En contexte réel, définir une politique de moindre privilège et vérifier les effets de l’ACL sur DNS, gestion et services nécessaires avant déploiement. Aucun filtrage stateful n’est fourni.

## Références

- Théorie : [ACL](../../05-Security/ACL.md) et [routage inter-VLAN](../../02-Switching/Inter-VLAN-Routing.md).
- Cisco : [Creating an IP Access List and Applying It to an Interface, Cisco IOS XE 17.x](https://www.cisco.com/c/en/us/td/docs/routers/ios/config/17-x/sec-vpn/b-security-vpn/m_sec-create-ip-apply-0.html).
