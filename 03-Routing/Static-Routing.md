# Routage statique IPv4 et IPv6

> Statut : `REVIEWED` · `PLATFORM DEPENDENT` · `REQUIRES LAB VALIDATION`. Les exemples sont revus statiquement et n’ont pas été exécutés.

## Introduction

Le routage statique consiste à configurer explicitement le chemin vers un réseau distant. Il convient aux petites topologies, aux routes de secours et aux routes par défaut. Les réseaux plus complexes peuvent préférer un protocole dynamique comme OSPF.

## Définition et concepts

Un routeur choisit une route selon le préfixe de destination le plus spécifique. À préfixe égal, les protocoles et leur distance administrative (AD) départagent les sources de route; une métrique intervient selon le protocole. Une route connectée apparaît lorsqu’une interface L3 est adressée et opérationnelle. Les routes statiques sont configurées manuellement; une route par défaut (`0.0.0.0/0` ou `::/0`) sert quand aucune route plus spécifique ne correspond. Une route statique flottante utilise une AD plus élevée pour servir de secours.

| Type | Usage | Exemple IPv4 |
|---|---|---|
| Connectée | Réseau directement attaché à une interface up/up | `C 192.168.10.0/24` |
| Statique | Préfixe distant connu par un next-hop | `ip route 192.168.30.0 255.255.255.0 10.0.12.2` |
| Défaut | Destination sans route plus spécifique | `ip route 0.0.0.0 0.0.0.0 10.0.12.2` |
| Flottante | Route de secours moins préférée par AD explicite | `ip route ... 10.0.12.2 200` |

L’AD compare l’origine des routes (ex. route connectée, statique, protocole); les valeurs varient selon la source et peuvent être changées. L’AD ne choisit pas le chemin le plus court à l’intérieur d’un même protocole.

## Objectifs pédagogiques

- Lire les routes connectées et statiques dans la RIB.
- Configurer routes IPv4/IPv6 par next-hop et route par défaut.
- Expliquer AD et route flottante.
- Vérifier le chemin aller et le chemin retour.

## Prérequis

Connaître l’adressage CIDR et les interfaces L3. Le next-hop doit être résolvable/routable; chaque réseau distant doit disposer d’un chemin de retour. Les commandes varient selon Cisco IOS/IOS XE et la plateforme.

## Fonctionnement et topologie

```text
LAN-A                  lien R1-R2                    LAN-B
192.168.10.0/24 -- R1 10.0.12.1/30 ===== 10.0.12.2 R2 -- 192.168.20.0/24
                    route vers LAN-B         route retour vers LAN-A
```

| Segment | Préfixe | Interface de passerelle |
|---|---|---|
| LAN-A | 192.168.10.0/24 | R1: 192.168.10.1 |
| R1-R2 | 10.0.12.0/30 | R1: .1; R2: .2 |
| LAN-B | 192.168.20.0/24 | R2: 192.168.20.1 |

## Configuration

### Route par next-hop

Sur R1, pour atteindre le LAN-B via R2 :

```text
R1(config)# ip route 192.168.20.0 255.255.255.0 10.0.12.2
R1(config)# ip route 0.0.0.0 0.0.0.0 10.0.12.2
```

Sur R2, la route retour est nécessaire :

```text
R2(config)# ip route 192.168.10.0 255.255.255.0 10.0.12.1
```

### Next-hop ou interface de sortie

Une route Ethernet avec next-hop identifie le voisin et permet au routeur de résoudre l’adresse de couche 2. Une route configurée seulement avec une interface de sortie Ethernet peut provoquer une résolution ARP par destination; préférez le next-hop ou une route entièrement spécifiée si la topologie le demande. Une interface de sortie seule est plus courante sur un lien point-à-point; sa disponibilité et son comportement dépendent de la plateforme.

### IPv6

Exemple de syntaxe IOS/IOS XE courante, sous réserve du support de l’image :

```text
R1(config)# ipv6 unicast-routing
R1(config)# ipv6 route 2001:db8:20::/64 2001:db8:12::2
R1(config)# ipv6 route ::/0 2001:db8:12::2
R2(config)# ipv6 route 2001:db8:10::/64 2001:db8:12::1
```

`2001:db8::/32` est réservé à la documentation. Configurez les préfixes correspondants sur les interfaces avant d’utiliser ces routes.

### Route flottante

Ajouter un next-hop de secours réellement joignable et une AD supérieure à celle de la route principale :

```text
R1(config)# ip route 192.168.20.0 255.255.255.0 10.0.12.2 200
```

Cette route ne devient candidate que si la route préférée disparaît de la table. Une route de secours qui ne surveille qu’une interface peut ne pas détecter une panne plus loin sur le chemin; le suivi de disponibilité dépend de mécanismes tels que IP SLA/track, non couverts ici.

## Explication des commandes

- `ip route <préfixe> <masque> <next-hop>` ajoute une route IPv4.
- `ipv6 route <préfixe>/<longueur> <next-hop>` ajoute une route IPv6.
- `0.0.0.0/0` et `::/0` représentent les routes par défaut.
- Le dernier nombre optionnel de la route statique IPv4 est l’AD configurée; une valeur supérieure est moins préférée.
- `ipv6 unicast-routing` active le transfert IPv6 sur les plateformes qui l’exigent.

## Vérification

```text
show ip route
show ip route static
show ipv6 route
show running-config | include ^ip route|^ipv6 route
show ip interface brief
ping 192.168.20.1
traceroute 192.168.20.10
```

Attendu : réseaux connectés présents pour interfaces actives, routes statiques présentes pour les préfixes configurés, et chemin retour fonctionnel. Les sorties exactes dépendent de la plateforme et ne sont pas reproduites ici.

## Dépannage

1. Vérifier interface/masque et état up/up.
2. Vérifier que le next-hop est joignable directement ou récursivement.
3. Comparer le préfixe et le masque à la destination réelle; le routeur utilise le longest-prefix match.
4. Contrôler la route retour, les ACL et les passerelles des hôtes.
5. Pour une route flottante, vérifier l’AD et comprendre quelle panne la fait retirer.

## Exercices et corrigés

1. R1 doit joindre `192.168.30.0/24` via `10.0.12.2`. **Réponse :** `ip route 192.168.30.0 255.255.255.0 10.0.12.2`; ajouter aussi un chemin retour côté réseau distant.
2. Créer une route par défaut IPv4 vers `10.0.12.2`. **Réponse :** `ip route 0.0.0.0 0.0.0.0 10.0.12.2`.
3. Ajouter une route IPv6 vers `2001:db8:30::/64` par `2001:db8:12::2`. **Réponse :** `ipv6 route 2001:db8:30::/64 2001:db8:12::2`.

## Sécurité et bonnes pratiques

Ne pas utiliser une route par défaut pour contourner une politique de filtrage. Contrôler les routes de retour et les annonces statiques; limiter l’administration aux réseaux dédiés. Éviter les routes de sortie Ethernet seules lorsque plusieurs destinations peuvent être résolues par ARP.

## Lab pratique

Mettre en pratique ces notions dans [Lab 03 — Routage statique](../07-Labs/03-Static-Routing/README.md), qui inclut trois routeurs, routes spécifiques et défaut. Statut : `REQUIRES LAB VALIDATION`.

## Références

- Cisco, [IP Routing: Static and Default Routes Configuration Guide (IOS XE 17)](https://www.cisco.com/c/en/us/td/docs/routers/ios/config/17-x/ip-routing/b-ip-routing/m_static-route.html) — vérifier la gamme exacte.
- Cisco, [IPv6 Configuration Guide (IOS XE)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipv6/configuration/xe-16/ipv6-xe-16-book.html).
