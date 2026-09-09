# Brief design

Ici t'a de quoi construire les pages Rejoindre et Événements sans toucher ma page d'accueil.

## Les fichiers partagés (ne pas dupliquer)

- `variables.css` : toutes les couleurs, tailles de texte, espacements, rayons du site... Aucune couleur ou taille ne doit jamais être écrite en dur dans une page - toujours `var(--nom-de-la-variable)`...

- `composants.css` : le style du header, du footer, des boutons, des liens d'action, des badges et des icônes... Ce sont les briques communes à toutes les pages.
- Le sprite SVG en haut de `headerFooter.html` : à recopier tel quel en haut de chaque nouvelle page.

## La règle numéro un : une seule version de chaque composant

- **Bouton d'action principal** : classe `.bouton-cta` (fond noir, texte bleu clair etc..) Bref un seul style, partout.
- **Lien secondaire** ("en savoir plus", "voir", "revivre l'événement"...) : classe `.lien-action` (texte bleu, icône flèche du sprite `#icone-fleche`). Ne pas réinventer un style de lien par carte - pas de vert, pas d'œil, pas de triangle.
- **Icônes** : uniquement le style trait fin (outline) du sprite, pas d'icône pleine ou colorée pour la navigation ou les boutons. sauf les photos et illustrations de contenu (à l'intérieur des cartes) ne sont pas concernées .
- **Badges de statut** : `.badge--actif` (vert, événement ou formation en cours) ou `.badge--passe` (gris, terminé). Pas d'autre couleur de badge.

## Comment démarrer une nouvelle page

1. Copier `headerFouter.html` exactement, renommer le fichier.
2. Ne modifier que ce qui se trouve entre `<main>` et `</main>`.
3. Créer un `nom_page.css` propre à cette page pour son propre contenu, en plus de `variables.css` et `composants.css` déjà liés dans le gabarit (genre le html reutilisable headerFooter).

Toute couleur, taille ou espacement qui n'existe pas encore dans `variables.css` : on en discute avant de l'ajouter

## Par Paskod 