# Security policy

## Signalement d’un problème

Pour un problème de sécurité dans un exemple, une configuration ou le dépôt, évitez de publier immédiatement les détails exploitables dans une issue publique. Utilisez le canal privé de signalement de vulnérabilité GitHub s’il est activé pour ce dépôt. Sinon, contactez les mainteneurs via les moyens disponibles sur la page GitHub du dépôt et demandez un échange privé. Ce projet ne publie pas d’adresse de contact dédiée.

Fournissez le chemin et le passage concernés, le risque, les conditions d’exploitation et une correction suggérée si vous en avez une. Ne joignez ni secret réel, ni clé privée, ni données client. Attendez une coordination raisonnable avec les mainteneurs avant toute divulgation publique; ne testez jamais sur des réseaux ou équipements tiers sans autorisation.

## Identifiants et données sensibles

Ne commitez jamais mots de passe, clés privées, tokens, configurations client, adresses de gestion sensibles ou fichiers contenant ces éléments. Utilisez des placeholders tels que `<SECRET>` dans la documentation. Si un secret a été publié, le révoquer/renouveler immédiatement; retirer le texte du commit courant ne garantit pas son effacement de l’historique.

## Configurations dangereuses

Signalez les commandes permissives, protocoles faibles, secrets d’exemple réalistes, filtres mal placés et instructions susceptibles de couper ou exposer le réseau. Incluez modèle, version IOS/IOS XE, rôle et extrait nettoyé. Les exemples éducatifs ne doivent pas être considérés comme configurations de production.

## Scope and disclosure

This policy covers this repository’s documentation and sample configurations. It does not authorize testing Cisco products, infrastructure, or third-party systems. Coordinate disclosure privately where possible and avoid publishing exploit details before maintainers have had a reasonable opportunity to assess and address the issue.
