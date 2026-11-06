# Rapport de phase 5.1 — Routage statique et cohérence des labs

## Modifications

### Article théorique

Création de [Static Routing](03-Routing/Static-Routing.md), qui présente la RIB, routes connectées, statiques, défaut et flottantes, AD, choix next-hop/interface de sortie, IPv4/IPv6, vérification, dépannage, bonnes pratiques et exercices corrigés. Le guide rappelle les limites de plateforme et porte `REQUIRES LAB VALIDATION`; aucune exécution n’est revendiquée.

### Cohérence des labs

- Lab 01 : conservation des plans /24 et /30, routes aller/retour, SVI de management. Génération RSA demeure dépendante de la plateforme.
- Lab 02 : vérification des VLAN 10/20/30, ports access, trunk et sous-interfaces. Adresses PC et gateways concordent.
- Lab 03 : vérification des trois LANs et deux liens /30, routes statiques réciproques; route par défaut sur R1. Le corrigé théorique et le lien pointent maintenant vers l’article routage statique dédié.
- Lab 04 : adresses/aires OSPF concordent; interfaces LAN passives pour éviter les adjacences vers les clients.
- Lab 05 : topologie clarifiée avec SW1, routeur edge, ISP et serveur; TEST-NET explicité comme plage documentaire/labo isolé; ajout de la configuration access du switch.
- Lab 06 : topologie et table VLAN complétées; ajout du port access VLAN 10. L’ACL du routeur est attachée en entrée à la sous-interface VLAN 20 et vise TCP/80 vers le serveur.
- Lab 07 : suppression de la définition répétée de l’interface R1; pools LAN-A/LAN-B, relais sur R2 et route de retour concordent avec les préfixes.
- Lab 08 : trunks présents sur les liens SW1-SW3 et SW2-SW3; EtherChannel LACP configuré des deux côtés SW1-SW2. Port numbers remain model-dependent.
- README racine : ajout du lien Static Routing; les huit labs demeurent indexés.

Tous les labs restent `REQUIRES LAB VALIDATION`. Les fichiers de paramètres d’endpoints indiquent les valeurs à saisir dans Packet Tracer; aucun `.pkt` n’a été produit.

## Validation

- Article statique : créé et lié depuis le README racine et le Lab 03.
- Labs : huit guides `README.md`, huit fichiers `topology.md` et configurations présentes dans chaque répertoire `configs/`.
- Liens internes Markdown : vérification statique sans cible manquante.
- Liens vers chacun des huit labs : vérifiés depuis README racine.
- Plans IP/masks/routes : contrôle documentaire des configurations et topologies; aucune simulation.
- Aucune commande exécutée; aucune sortie attendue présentée comme observée.
- Tous les huit guides conservent le statut `REQUIRES LAB VALIDATION`.
- Aucun ancien article n’a été supprimé; structure générale préservée.

## Limites restantes

- Les modèles Packet Tracer peuvent ne pas offrir `G0/2`, `G0/3`, plusieurs ports gigabit, RSA 2048 ou certaines options IOS. Adapter interfaces et commandes à l’image réelle avant test.
- Le Lab 01 ne contient pas de clé privée; la clé RSA doit être générée localement sur l’équipement avec une taille compatible et approuvée.
- Le Lab 05 utilise des plages TEST-NET non routées sur Internet; le serveur externe doit être configuré via l’interface du simulateur.
- Le lab DHCP suppose que l’IOS émette/réponde aux pools relayés et que la passerelle/route retour soient opérationnelles.
- La politique ACL du Lab 06 permet le reste du trafic IP depuis les invités; elle reste un exercice, non une politique de production.
- L’AD statique IPv6 et les syntaxes particulières de routeur devront être vérifiées sur l’IOS ciblé.
- Aucun résultat Packet Tracer ou IOS n’est connu.

La phase 6 n’a pas commencé. Rien n’a été commité ni poussé.
