# Lab — NAT et PAT

**Difficulté : Intermédiaire. Validation : `REQUIRES LAB VALIDATION`.**

## Objectifs
Configurer nat et pat, vérifier l’état opérationnel et diagnostiquer des erreurs courantes.

## Matériel requis
Deux PC, un switch, routeur edge R1, routeur ISP.

## Compatibilité Packet Tracer
Syntaxe IOS pédagogique, fonctions et noms d’interfaces dépendants du modèle. Aucun `.pkt` fourni; non testé dans un simulateur.

## Topologie et adressage
Voir [topology.md](topology.md). Les PC doivent recevoir une adresse dans leur réseau et utiliser la passerelle du routeur correspondante.

## Mise en œuvre
1. Construire le câblage décrit et adapter les noms d’interfaces au modèle choisi.
2. Appliquer les fichiers de `configs/` sur les appareils nommés. Configurer les PC selon le plan.
3. Vérifier l’état des interfaces avant les tests.

## Explications
Les adresses définissent les segments L3; les commandes spécifiques au lab mettent en place les VLANs, routes, adjacences, traductions ou protections demandées. Les interfaces doivent être actives et les deux extrémités d’un lien cohérentes.

## Vérification et résultat attendu
Sur R1, utiliser `show ip interface brief`, `show ip route`, `show ip nat statistics` et `show ip nat translations`. Générer du trafic depuis PC-A vers le serveur externe avant d’examiner les traductions; une table vide avant trafic est normale. Attendu : LAN joignable, route par défaut vers l’ISP, interfaces inside/outside correspondant au câblage et traduction PAT créée après trafic correspondant. Aucun résultat d’exécution n’est revendiqué. Tester la connectivité PC-A–passerelle, puis PC-A–serveur.

## Dépannage — scénarios et corrections
- **Pas de traduction PAT** : vérifier que le trafic correspond à l’ACL NAT, que l’interface LAN est `ip nat inside`, que l’interface ISP est `ip nat outside`, puis consulter les compteurs NAT.
- **PC-A atteint R1 mais pas le serveur** : vérifier la route par défaut de R1, l’état des interfaces et l’adressage/passerelle du serveur.
- **Traduction créée mais réponse absente** : vérifier le chemin retour depuis le réseau externe et que le test cible le serveur configuré; ne pas interpréter une absence de réponse ICMP comme preuve unique d’un échec NAT.

## Exercices et corrigés
1. Retirer `ip nat inside` de l’interface LAN. **Corrigé :** rétablir le rôle inside sur l’interface identifiée dans la topologie.
2. Retirer la route par défaut de R1. **Corrigé :** restaurer la route vers le next-hop ISP indiqué dans `configs/`.
3. Générer plusieurs flux et observer les traductions PAT. **Corrigé :** utiliser `show ip nat translations` et `show ip nat statistics`; comparer les interfaces inside/outside et l’ACL de correspondance sans effacer la configuration.

## Sécurité et limites
Secrets uniques si requis; ne pas exposer management ou services. Syntaxe dépendante IOS/IOS XE et du matériel; ce labo n’est pas prêt pour production et demande validation pratique.

## Références
- Théorie : [NAT](../../04-Network-Services/NAT.md) et [PAT](../../04-Network-Services/PAT.md)
