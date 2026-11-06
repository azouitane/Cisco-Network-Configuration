# Lab 04 — OSPFv2, area 0

**Difficulté : intermédiaire. Statut : `REQUIRES LAB VALIDATION`.**

## Objectifs

- Établir des adjacences OSPFv2 entre R1, R2 et R3 dans l’area 0.
- Annoncer les LAN et liens inter-routeur avec l’activation OSPF par interface.
- Vérifier les voisins, routes apprises et connectivité de bout en bout.

## Matériel et compatibilité

Trois routeurs avec interfaces Ethernet; trois PC. La syntaxe `ip ospf <process-id> area <area-id>` est dépendante du support OSPF de l’image IOS/IOS XE. Aucun Packet Tracer n’a été exécuté.

## Topologie et adressage

Voir [topology.md](topology.md) et `configs/PCs.txt`. Liens : R1 G0/1–R2 G0/0 et R2 G0/2–R3 G0/0; LAN-A, LAN-B et LAN-C sur G0/0, G0/1 et G0/1 respectivement.

| Segment | Préfixe | Interface(s) |
|---|---|---|
| LAN-A | 192.168.10.0/24 | R1 G0/0 .1 |
| R1–R2 | 10.0.12.0/30 | R1 .1, R2 .2 |
| LAN-B | 192.168.20.0/24 | R2 G0/1 .1 |
| R2–R3 | 10.0.23.0/30 | R2 .1, R3 .2 |
| LAN-C | 192.168.30.0/24 | R3 G0/1 .1 |

## Mise en œuvre

1. Câbler le réseau et configurer les PC selon `configs/PCs.txt`.
2. Appliquer les configurations des trois routeurs; démarrer par les interfaces, puis OSPF.
3. Attendre la convergence, puis vérifier les adjacences et routes apprises.
4. Tester chaque passerelle et les pings entre les PC.

## Explication des commandes

Les interfaces LAN et WAN activent le processus OSPF 10 dans l’area 0. Les interfaces LAN sont déclarées passives sous `router ospf 10`: leurs préfixes sont annoncés, mais aucune adjacence n’est recherchée sur le LAN. Les liens /30 ne sont pas passifs et peuvent former les adjacences. Les Router ID sont configurés explicitement.

## Vérification et résultats attendus

Utiliser `show ip interface brief`, `show ip ospf neighbor`, `show ip ospf interface brief`, `show ip route ospf` et `show ip protocols`. Attendu : R1 voisin de R2, R3 voisin de R2, routes OSPF distantes présentes et connectivité entre LANs. Les états exacts dépendent du modèle et de la convergence; aucune sortie n’est présumée.

## Dépannage

- Aucun voisin sur un lien : comparer IP/masque, area, timers/authentification si configurée et état de l’interface.
- Voisin ne monte pas au-delà de l’état initial : vérifier réseau partagé, MTU, Router ID uniques et paramètres OSPF compatibles.
- Voisin établi mais LAN distant absent : vérifier `passive-interface`, activation OSPF sur l’interface LAN et présence de la route dans la RIB.

## Exercices avec corrigés

1. Rendre l’interface R2 G0/2 passive. **Correction :** retirer le caractère passif sur le lien vers R3, puis vérifier le voisin.
2. Dupliquer le Router ID de R2 sur R3. **Correction :** rétablir le Router ID unique défini dans le fichier R3.
3. Changer l’area d’une extrémité du lien /30. **Correction :** configurer area 0 des deux côtés.

## Sécurité et limites

Les interfaces où aucun voisin ne doit apparaître sont configurées passives. Les exemples n’incluent pas l’authentification OSPF; choisir les mécanismes compatibles avec la plateforme et le design avant usage réel.

## Références

- Théorie : [OSPF](../../03-Routing/OSPF.md).
- Cisco : [OSPF Configuration Guide, Cisco IOS XE 17.x](https://www.cisco.com/c/en/us/td/docs/routers/ios/config/17-x/ip-routing/b-ip-routing/m_iro-cfg-0.html).
