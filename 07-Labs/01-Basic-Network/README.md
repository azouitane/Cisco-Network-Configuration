# Lab 01 — Réseau de base

**Niveau : débutant. Statut : `REQUIRES LAB VALIDATION`.**

## Objectifs
Configurer interfaces, passerelles, routes statiques et accès SSH au routeur R1; diagnostiquer une connectivité de bout en bout.

## Matériel et compatibilité
Deux routeurs, deux switches L2, deux PC. Packet Tracer : choisir routeurs avec interfaces Ethernet et image acceptant SSH/RSA; le nombre de VTY varie.

## Topologie et adressage
Voir [topology.md](topology.md). Configurer sur les PC les adresses .10/24 et les passerelles .1.

## Mise en œuvre
1. Câbler les équipements selon la topologie.
2. Appliquer les fichiers R1/R2 et SW1/SW2 depuis la console; remplacer placeholders SSH et générer la clé selon la capacité annoncée par l’image. Dans les configurations fournies, SSH est configuré sur R1 uniquement; les SVI de management des switches ne constituent pas une configuration SSH.
3. Configurer les IP statiques des PC.
4. Tester chaque passerelle, puis le ping PC-A vers PC-B.

## Explication et vérification
Les interfaces routeur séparent les LAN et le lien /30. Les routes statiques donnent à chaque routeur le chemin vers le LAN distant. Sur R1, SSH requiert hostname, domaine, compte local, clé RSA et VTY limitant l’entrée à SSH. Le lab ne configure pas l’accès SSH aux switches.

Commandes sur les routeurs : `show ip interface brief`, `show ip route`, `show ip ssh`, `show users`; test SSH vers R1 et `ping` depuis les PC. Attendu : interfaces up/up, routes distantes présentes et ping de bout en bout réussi si les configs et le modèle sont compatibles; aucune sortie n’est présumée.

## Dépannage
- Ping passerelle échoue : vérifier câble, IP/masque, interface `no shutdown`.
- LAN local fonctionne mais distant échoue : vérifier route statique et next-hop.
- SSH échoue : vérifier IP de gestion, compte local, clé RSA, VTY et compatibilité crypto du modèle.

## Exercices et corrigé
1. Retirer la route R1 vers LAN B. Symptôme : ping intersite échoue. **Correction :** restaurer `ip route 192.168.20.0 255.255.255.0 10.0.0.2`.
2. Mettre PC-B en /16. **Correction :** rétablir /24 pour correspondre au plan.

## Références
Théorie : [configuration de base](../../01-Basics/Basic-Router-Switch-Configuration.md). Cisco : [SSH sur routeurs](https://www.cisco.com/c/en/us/support/docs/security-vpn/secure-shell-ssh/4145-ssh.html).
