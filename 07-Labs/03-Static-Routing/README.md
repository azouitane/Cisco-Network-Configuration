# Lab 03 — Routage statique IPv4

**Difficulté : intermédiaire. Statut : `REQUIRES LAB VALIDATION`.**

## Objectifs

- Configurer des routes statiques entre trois routeurs et trois LAN.
- Installer une route par défaut sur R1.
- Vérifier les chemins aller-retour et la table de routage.

## Matériel et compatibilité

Trois routeurs avec au moins trois interfaces Ethernet sur R2, trois PC. Les noms d’interfaces varient selon le modèle. Les commandes IPv4 sont courantes sur IOS/IOS XE, mais aucune simulation n’a été exécutée.

## Topologie et adressage

Voir [topology.md](topology.md) et `configs/PCs.txt`. Câbler R1 G0/1–R2 G0/0, R2 G0/2–R3 G0/0; connecter chaque PC à son LAN.

| Segment | Préfixe | Adresse de passerelle / interface |
|---|---|---|
| LAN-A | 192.168.10.0/24 | R1 192.168.10.1 |
| R1–R2 | 10.0.12.0/30 | R1 .1, R2 .2 |
| LAN-B | 192.168.20.0/24 | R2 192.168.20.1 |
| R2–R3 | 10.0.23.0/30 | R2 .1, R3 .2 |
| LAN-C | 192.168.30.0/24 | R3 192.168.30.1 |

## Mise en œuvre

1. Configurer les PC selon `configs/PCs.txt`.
2. Appliquer les fichiers `R1.txt`, `R2.txt`, `R3.txt` depuis les consoles; activer les interfaces.
3. Vérifier les routes connectées et la portée des next-hop avant les routes distantes.
4. Tester les pings entre passerelles, puis entre PC-A, PC-B et PC-C.

## Explication des commandes

Chaque `ip route <réseau> <masque> <next-hop>` installe un préfixe distant. R1 a deux routes spécifiques vers R2 et une route par défaut vers le même next-hop. R2 et R3 ont des routes de retour spécifiques. Les routes statiques n’annoncent pas les préfixes à un protocole dynamique.

## Vérification et résultats attendus

Sur chaque routeur : `show ip interface brief`, `show ip route` et `show ip route <adresse-destination>`. Vérifier que les liens sont actifs, que les routes distantes apparaissent avec le next-hop prévu et que le trafic de retour dispose lui aussi d’une route. Les tests ICMP de bout en bout sont attendus si les configurations sont appliquées correctement; aucune sortie n’est revendiquée.

## Dépannage

- Route absente : vérifier réseau/masque de la commande, next-hop et état du lien directement connecté.
- PC joint sa passerelle mais pas les autres LAN : contrôler la route sur chaque routeur, y compris le chemin retour.
- Next-hop injoignable : vérifier les adresses /30 et l’interface du lien adjacent.

## Exercices avec corrigés

1. Retirer la route R3 vers LAN-A. **Correction :** restaurer la route vers 192.168.10.0/24 via 10.0.23.1.
2. Supprimer la route par défaut de R1. **Correction :** rétablir la route par défaut via 10.0.12.2; les routes spécifiques restent prioritaires par correspondance de préfixe.
3. Mettre R2 G0/2 en /24 par erreur. **Correction :** utiliser 255.255.255.252 aux deux extrémités du lien 10.0.23.0/30.

## Sécurité et limites

Les routes sont des exemples de laboratoire. Avant un déploiement, confirmer les chemins de retour, la redondance et la politique de filtrage. Une route statique n’est pas un mécanisme de sécurité.

## Références

- Théorie : [Routage statique](../../03-Routing/Static-Routing.md).
- Cisco : [IP Routing Configuration Guide](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_pi/configuration/xe-17/iri-xe-17-book.html).
