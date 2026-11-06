# Contribuer / Contributing

Les contributions doivent améliorer la précision, la lisibilité ou la valeur pédagogique de la base. Les échanges peuvent se faire en français ou en anglais; les articles techniques restent principalement en français.

## Proposer une contribution

1. Recherchez les articles et issues existants pour éviter les doublons.
2. Pour une erreur, utilisez le modèle **Incorrect Cisco Configuration** et fournissez les éléments de reproduction sans secret.
3. Pour un nouveau sujet ou une amélioration générale, utilisez le modèle d’issue adapté.
4. Soumettez une Pull Request petite et ciblée, avec liens vers les articles concernés.

## Normes de documentation

- Utiliser un titre clair, des sections cohérentes, tableaux lisibles et blocs de code avec balises de langue (`text`, `bash` ou format pertinent).
- Écrire en français clair; conserver en anglais les commandes IOS et les termes techniques normalisés.
- Décrire objectifs, prérequis, topologie/adressage si pertinent, explications, vérifications, dépannage, erreurs courantes, sécurité, exercice et références.
- Utiliser des liens relatifs pour naviguer dans le dépôt et des liens directs vers la documentation officielle Cisco ou les normes.
- Marquer les contenus `REQUIRES LAB VALIDATION`, `PLATFORM DEPENDENT`, `REVIEWED` ou `LAB VALIDATED` selon la preuve disponible. `LAB VALIDATED` exige le modèle exact, version IOS/IOS XE ou simulateur, procédure et observations vérifiables.
- Ne pas créer de sorties CLI fictives, de captures factices ou de faux fichiers `.pkt`.

## Exigences pour les configurations Cisco

- Indiquer appareil, interfaces, version/gamme supportée ou dépendances connues.
- Expliquer l’ordre et le rôle des commandes importantes; distinguer configuration et vérification.
- Remplacer secrets, clés et données client par des placeholders. Ne jamais inclure de credential réel, même dans une issue.
- Vérifier cohérence des interfaces, adresses, masques, routes et paramètres des deux extrémités.
- Identifier les commandes obsolètes, dépendantes de plateforme ou nécessitant un labo; ne pas affirmer qu’une config fonctionne sans test réel.
- Favoriser les pratiques d’administration sécurisées et signaler les limites des exemples pédagogiques.

## Pull Requests et revue

Décrire le besoin, les fichiers modifiés, la justification technique, les plateformes visées, les contrôles effectués et le statut de validation. La revue vérifie la logique technique, les risques de sécurité, la cohérence pédagogique, les liens Markdown et l’absence de secrets. Les changements dépendant d’une plateforme peuvent demander une preuve de labo ou rester explicitement `REQUIRES LAB VALIDATION`.

See `.github/PULL_REQUEST_TEMPLATE.md` for the requested PR details. Maintainers may request changes or clarification before merging. No contribution is considered tested solely because it is documented.
