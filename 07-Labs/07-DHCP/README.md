# Lab 07 — DHCPv4 et relais DHCP

**Difficulté : débutant +. Statut : `REQUIRES LAB VALIDATION`.**

## Objectifs

- Servir directement le LAN-A depuis le pool DHCP de R1.
- Relayer les requêtes DHCP du LAN-B depuis R2 vers R1.
- Vérifier l’adresse, le masque, la passerelle reçue et les bindings côté serveur.

## Matériel et compatibilité

Deux routeurs, deux switches L2 et deux PCs. Les configurations sont des exemples IOS; adapter les noms de ports et vérifier la prise en charge du service DHCP/relay dans la version du simulateur ou de l’image. Aucun `.pkt` n’est fourni et ce lab n’a pas été exécuté.

## Topologie et adressage

Voir [topology.md](topology.md). Les PC-A et PC-B sont tous deux configurés pour obtenir IPv4 via DHCP. Aucun serveur DNS n’est nécessaire pour vérifier l’attribution locale; `192.0.2.53` du pool est une adresse de documentation, pas un serveur garanti joignable.

## Configuration et mise en œuvre

1. Construire les liens d’accès L2 vers les interfaces LAN et le lien routé R1–R2.
2. Appliquer `configs/R1.txt` et `configs/R2.txt`; aucun changement de configuration n’est requis sur les switches au-delà d’une connectivité L2 de base.
3. Configurer PC-A et PC-B en DHCP depuis leur panneau réseau.
4. Attendre la procédure DHCP, puis contrôler les paramètres reçus.

R1 possède les pools LAN_A et LAN_B. Sur R2, `ip helper-address 10.0.12.1` est configuré sur l’interface du LAN-B : il relaie les broadcasts clients vers R1. L’adresse de relais giaddr permet au serveur de sélectionner le pool du LAN-B. R1 possède une route retour vers ce LAN via R2.

## Vérification et résultats attendus

Sur R1 :

```text
show ip dhcp binding
show ip dhcp server statistics
show ip route
```

Sur R2 :

```text
show ip interface brief
show running-config interface GigabitEthernet0/1
```

Sur chaque PC, consulter l’adresse IPv4, le masque et la passerelle dans l’interface IP Configuration (ou la commande équivalente du simulateur). Attendu : PC-A reçoit une adresse du pool LAN_A avec passerelle `192.168.10.1`; PC-B reçoit une adresse du pool LAN_B avec passerelle `192.168.20.1`. Aucune sortie observée n’est fournie.

## Dépannage

- **PC-A n’obtient pas de bail** : vérifier lien SW1–R1, état et adresse R1 G0/0, pool LAN_A, exclusions et réglage DHCP du PC.
- **PC-B n’obtient pas de bail alors que LAN-A fonctionne** : contrôler helper sur R2 G0/1, connectivité R2–R1, pool LAN_B et route de retour R1 vers `192.168.20.0/24`.
- **Le PC obtient une adresse mais pas la bonne passerelle** : comparer `default-router` du pool au routeur qui dessert le LAN client.
- **Le pool semble vide** : vérifier `show ip dhcp binding`, exclusions, conflits et statistiques; éviter `debug` persistant.

## Exercices avec corrigés

1. Retirer `ip helper-address` de R2 G0/1. Le client LAN-B ne reçoit pas d’offre; **corrigé :** restaurer `ip helper-address 10.0.12.1` sur l’interface LAN-B.
2. Changer le `default-router` du pool LAN_B en `192.168.10.1`. **Corrigé :** rétablir `192.168.20.1`.
3. Ajouter une adresse d’exclusion erronée couvrant le pool entier. **Corrigé :** limiter l’exclusion aux seules adresses réservées `.1` à `.20`.

## Sécurité et références

Les pools sont des exemples de laboratoire. N’activez pas de serveur DHCP non autorisé sur un réseau de production; utiliser DHCP Snooping pour les environnements qui le supportent et vérifier précisément les ports de confiance. Consultez [l’article DHCPv4](../../04-Network-Services/DHCPv4.md) et la documentation officielle Cisco correspondant au modèle.
