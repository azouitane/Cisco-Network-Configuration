# Lab — STP et EtherChannel

**Difficulté : Intermédiaire. Validation : `REQUIRES LAB VALIDATION`.**

## Objectifs
- Choisir SW1 comme root bridge pour VLAN 1 avec Rapid-PVST+.
- Former un EtherChannel LACP entre SW1 et SW2 sur les deux liens parallèles.
- Observer STP bloquer un chemin redondant du triangle afin d’éviter une boucle.

## Matériel requis
Trois switchs L2 disposant chacun d’au moins trois interfaces Ethernet; deux liens parallèles entre SW1 et SW2 et un lien de chaque switch vers SW3. Les PC ne sont pas nécessaires pour l’objectif de couche 2.

## Compatibilité Packet Tracer
Syntaxe IOS pédagogique, fonctions et noms d’interfaces dépendants du modèle. Aucun `.pkt` fourni; non testé dans un simulateur.

## Topologie et adressage
Voir [topology.md](topology.md) et `configs/Connections.txt`. Ce scénario est limité à la couche 2; il ne comporte ni plan IP, ni routeur, ni passerelle.

## Mise en œuvre
1. Construire le câblage décrit et adapter les noms d’interfaces au modèle choisi.
2. Appliquer le fichier de configuration de chaque switch depuis la console.
3. Vérifier les liens physiques, l’état LACP et l’élection STP.

## Explications
Les deux liens SW1–SW2 sont agrégés dans Port-channel1 avec LACP actif des deux côtés. Le lien logique devient un port trunk. Rapid-PVST+ calcule une topologie sans boucle et SW1 est explicitement sélectionné comme root pour VLAN 1. Les ports d’un EtherChannel doivent avoir des paramètres compatibles aux deux extrémités; les ports membre et le Port-channel sont configurés en trunk dans les exemples.

## Vérification et résultat attendu
Sur chaque switch, utiliser `show etherchannel summary`, `show interfaces port-channel 1`, `show interfaces trunk`, `show spanning-tree vlan 1` et `show spanning-tree summary`. Attendu : Port-channel1 et ses membres sont opérationnels selon l’état affiché par la plateforme; SW1 est root pour VLAN 1; STP place le chemin redondant dans un état non-transmetteur pour éviter une boucle. La sélection exacte du port bloqué dépend des coûts et identifiants de bridge. Aucun résultat d’exécution n’est revendiqué; ce lab reste à valider sur le modèle choisi.

## Dépannage — scénarios et corrections
- **Port-channel absent ou membres suspendus** : comparer les ports membres, le numéro de groupe et le mode LACP aux deux extrémités; contrôler `show etherchannel summary` et les journaux.
- **Paramètres incompatibles sur le bundle** : comparer mode trunk, VLAN natif et VLAN autorisés sur tous les membres et sur Port-channel1; rendre les paramètres cohérents.
- **Boucle ou root inattendu** : vérifier `show spanning-tree vlan 1`, la priorité/root affichée et l’état des liens; confirmer que SW1 est le root attendu et que le lien redondant est traité par STP.

## Exercices et corrigés
1. Passer un des membres SW1–SW2 en mode LACP `passive` des deux côtés. **Corrigé :** LACP ne démarre pas si les deux côtés sont passifs; remettre au moins un côté en `active`.
2. Rendre le VLAN natif différent sur un membre du bundle. **Corrigé :** rétablir les mêmes paramètres trunk sur les membres et le Port-channel.
3. Retirer `spanning-tree vlan 1 root primary` de SW1. **Corrigé :** restaurer la configuration de priorité prévue et vérifier le root avec `show spanning-tree vlan 1`.

## Sécurité et limites
Secrets uniques si requis; ne pas exposer management ou services. Syntaxe dépendante IOS/IOS XE et du matériel; ce labo n’est pas prêt pour production et demande validation pratique.

## Références
- Théorie : [STP](../../02-Switching/STP.md) et [EtherChannel](../../02-Switching/EtherChannel.md)
