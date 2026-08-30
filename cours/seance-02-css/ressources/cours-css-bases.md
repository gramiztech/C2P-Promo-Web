# Les bases du CSS - support de séance C2P

Ce document couvre la théorie de la séance. On avance notion par notion, avec juste assez de code pour illustrer chaque idée - la pratique complète, c'est pour l'atelier.

---

## 0. Pourquoi le CSS existe

En 2003, un designer nommé Dave Shea a lancé un site appelé **CSS Zen Garden**. Le principe : une seule page HTML, jamais modifiée, et des centaines de designers du monde entier qui proposaient chacun une feuille de style différente pour l'habiller. Résultat : la même page pouvait ressembler à un magazine, à une affiche de cinéma ou à un carnet de croquis, juste en changeant le fichier CSS relié.

C'est la meilleure démonstration de ce qu'on répète depuis la séance 1 : le HTML porte le contenu, le CSS porte l'apparence. Ce sont deux métiers différents, dans deux fichiers différents.

Le CSS lui-même est né en 1996, deux ans avant que les navigateurs ne commencent à bien le prendre en charge. Avant lui, les développeurs empilaient des tableaux HTML et des images invisibles juste pour positionner des éléments sur la page - une technique qui a existé, mais qui n'a plus sa place aujourd'hui.

---

## 1. Relier une feuille CSS à une page HTML

Il y a trois façons de faire, mais une seule qu'on utilise en pratique.

**Le style en ligne**, directement sur une balise :
```html
<p style="color: blue;">Texte</p>
```
On l'évite : il faut le répéter sur chaque balise, et il est presque impossible à maintenir sur un vrai site.

**Le style interne**, dans une balise `<style>` en haut de la page :
```html
<style>
  p { color: blue; }
</style>
```
Un peu mieux, mais toujours limité à une seule page.

**Le style externe**, celui qu'on utilise sur le site de C2P :
```html
<link rel="stylesheet" href="/style.css">
```
Un seul fichier `.css`, relié depuis toutes les pages du site. On change une couleur une fois, elle change partout. C'est aussi ce qui permet au navigateur de garder ce fichier en mémoire (le cache) au lieu de le retélécharger à chaque page.

---

## 2. Anatomie d'une règle CSS

Voici la plus petite unité de CSS qui existe :
```css
p {
  color: blue;
}
```
On la lit ainsi :
- `p` : le **sélecteur** - à qui s'adresse cette règle (ici, tous les paragraphes).
- `{ }` : le **bloc de déclaration** - ce qui est entre les accolades.
- `color` : la **propriété** - ce qu'on modifie.
- `blue` : la **valeur** - comment on le modifie.
- `color: blue;` dans son ensemble : une **déclaration**. On peut en empiler plusieurs dans un même bloc, chacune terminée par un point-virgule.

Retenir ce vocabulaire précis aide énormément une fois qu'on cherche de l'aide en ligne : la documentation et les messages d'erreur utilisent ces mots.

---

## 3. Les sélecteurs de base

Le sélecteur, c'est la question qu'on pose : "à qui je parle ?"

**Sélecteur d'élément** - tous les éléments d'un type :
```css
p {
  color: black;
}
```

**Sélecteur de classe** - un groupe qu'on choisit soi-même, avec un point :
```css
.carte {
  background-color: white;
}
```
```html
<div class="carte">...</div>
```
C'est le sélecteur le plus utilisé en pratique, parce qu'on peut le poser sur autant d'éléments qu'on veut, même de types différents.

**Sélecteur d'identifiant** - un seul élément précis, avec un dièse :
```css
#header-principal {
  background-color: navy;
}
```
```html
<header id="header-principal">...</header>
```
Un `id` ne doit apparaître qu'une seule fois par page (contrairement à une classe). On le réserve pour un usage bien particulier - souvent pas pour le style, mais pour être ciblé par du JavaScript ou par un lien d'ancre.

**Sélecteur universel** - absolument tout :
```css
* {
  box-sizing: border-box;
}
```

**Sélecteur descendant** - un élément à l'intérieur d'un autre :
```css
header a {
  color: blue;
}
```
Ici, ça ne cible que les liens `<a>` qui sont dans un `<header>`, pas tous les liens de la page.

**Sélecteur d'attribut** - un élément qui possède un attribut précis :
```css
a[href*="inscription"] {
  color: green;
}
```
Ça cible tous les liens dont l'attribut `href` contient le mot "inscription", peu importe le texte affiché. C'est ce qui permet de cibler un lien précis dans le menu du site de C2P sans avoir besoin d'ajouter une classe dessus.

**Les combinateurs**, pour préciser une relation entre deux éléments :

| Écriture | Nom | Ce qu'il cible |
|---|---|---|
| `A B` | descendant | tout `B` à l'intérieur de `A`, peu importe la profondeur |
| `A > B` | enfant direct | un `B` juste à l'intérieur de `A`, pas plus loin |
| `A + B` | frère adjacent | le `B` juste après `A`, même parent |
| `A ~ B` | frères suivants | tous les `B` qui suivent `A`, même parent |

```css
.carte + .carte {
  margin-top: 20px;
}
```
Cette règle ajoute un espace uniquement entre deux cartes qui se suivent - jamais avant la toute première.

---

## 4. La cascade : pourquoi "cascading" style sheets

Si deux règles CSS visent le même élément avec la même propriété, laquelle gagne ? C'est ce qu'on appelle **la cascade**, et elle suit un ordre précis :

1. **L'importance** : une règle avec `!important` gagne presque toujours (à éviter - si on en a besoin, c'est souvent le signe que la structure du CSS a un problème ailleurs).
2. **La spécificité** : voir section suivante.
3. **L'ordre d'écriture** : à spécificité égale, la règle écrite en dernier dans le fichier gagne.

**L'héritage** est un mécanisme voisin mais différent : certaines propriétés, comme `color` ou `font-family`, se transmettent automatiquement d'un élément à ses enfants, sauf si on les redéfinit. D'autres, comme `border` ou `padding`, ne s'héritent jamais - chaque élément doit les recevoir explicitement.

---

## 5. La spécificité, avec un calcul simple

Chaque sélecteur a un "poids". Pour comparer deux règles, on additionne les points de chaque sélecteur :

| Type de sélecteur | Points |
|---|---|
| élément (`p`, `header`), pseudo-élément (`::before`) | 1 |
| classe (`.carte`), attribut (`[href]`), pseudo-classe (`:hover`) | 10 |
| identifiant (`#header-principal`) | 100 |
| style en ligne (`style="..."`) | 1000 |

Exemple concret :
```css
p { color: black; }          /* poids : 1 */
.intro { color: blue; }      /* poids : 10 */
#texte-accueil { color: red; } /* poids : 100 */
```
Si les trois règles ciblent le même paragraphe, c'est `#texte-accueil` qui gagne : 100 points contre 10 et 1, peu importe l'ordre d'écriture.

Le piège classique pour un débutant : utiliser des `id` partout "pour être sûr que ça s'applique". Résultat : le CSS devient très difficile à corriger plus tard, parce que rien ne peut plus battre un `id` sans en ajouter un autre. La bonne pratique, c'est de garder la spécificité aussi basse que possible, et de s'appuyer sur les classes en premier réflexe.

---

## 6. Le modèle de boîte (box model)

En CSS, **chaque élément HTML est une boîte rectangulaire**, même s'il ne le montre pas visuellement. Cette boîte a quatre couches, de l'intérieur vers l'extérieur :

![Le modèle de boîte : content au centre, entouré du padding, puis de la bordure, puis du margin](box-model.svg)

```css
.carte {
  padding: 20px;
  border: 1px solid black;
  margin: 16px;
}
```
- `padding` : l'air entre le texte et le bord de la boîte.
- `border` : le trait qui délimite la boîte.
- `margin` : l'espace entre cette boîte et ses voisines.

**Le piège à connaître absolument** : par défaut, `width` ne fixe que le `content`. Si on ajoute du `padding` ou une `border`, la boîte devient plus large que le `width` qu'on a écrit - ça surprend tout le monde une fois.

La solution, une seule ligne qu'on pose presque toujours en haut de son fichier :
```css
* {
  box-sizing: border-box;
}
```
Avec `border-box`, `padding` et `border` sont comptés **à l'intérieur** du `width` donné, plus jamais ajoutés par-dessus. C'est le comportement que presque tout le monde préfère, et c'est pour ça qu'on le retrouve dans le style.css de C2P dès les premières lignes.

---

## 7. Les unités : avec quoi on mesure

| Unité | Ce qu'elle mesure | Exemple d'usage |
|---|---|---|
| `px` | Un pixel fixe, toujours la même taille | bordures fines, détails précis |
| `%` | Un pourcentage du parent | largeurs fluides |
| `em` | Relatif à la taille de police de l'élément parent | espacements liés au texte local |
| `rem` | Relatif à la taille de police de la racine (`html`) | tailles de police cohérentes sur toute la page |
| `vw` / `vh` | Pourcentage de la largeur / hauteur de la fenêtre | éléments qui suivent la taille de l'écran |

Règle simple à retenir : pour la taille du texte, `rem` est le choix par défaut le plus sûr - si quelqu'un agrandit le texte dans son navigateur (pour des raisons d'accessibilité), tout reste cohérent, parce que tout part de la même référence.

---

## 8. Les couleurs

Trois façons courantes d'écrire une couleur :
```css
color: tomato;                 /* mot-clé, environ 150 disponibles */
color: #1683D0;                /* hexadécimal : rouge-vert-bleu en base 16 */
color: rgb(22, 131, 208);      /* rouge, vert, bleu, de 0 à 255 chacun */
```
Le format hexadécimal (`#1683D0`) est le plus utilisé en pratique - c'est celui que produit n'importe quel outil de sélection de couleur, et c'est celui qu'on retrouve dans les variables du site de C2P.

On peut ajouter une transparence avec `rgba()`, un quatrième nombre entre 0 (invisible) et 1 (opaque) :
```css
background-color: rgba(0, 0, 0, 0.5); /* noir à 50% de transparence */
```

---

## 9. La typographie de base

```css
p {
  font-family: system-ui, sans-serif;
  font-size: 1rem;
  font-weight: 400;
  line-height: 1.6;
  text-align: left;
}
```
- `font-family` : on donne toujours plusieurs polices, séparées par des virgules - si la première n'est pas installée sur l'appareil de la personne, le navigateur essaie la suivante.
- `font-weight` : l'épaisseur du trait (`400` normal, `700` gras - on peut aussi écrire `normal` et `bold`).
- `line-height` : l'espace entre les lignes. Sans lui, un paragraphe de plusieurs lignes est difficile à lire - une valeur autour de `1.5` à `1.6` convient à la plupart des textes.

---

## 10. `display` et `position`, en survol

`display` décide comment un élément se comporte dans la page :
- `block` : prend toute la largeur disponible, saute une ligne avant et après (`<div>`, `<p>`, `<h1>`).
- `inline` : ne prend que la largeur de son contenu, reste dans le fil du texte (`<a>`, `<span>`).
- `inline-block` : reste dans le fil du texte, mais accepte `width`/`height`/`margin` comme un `block`.
- `flex` et `grid` : des modes de mise en page pour organiser plusieurs éléments ensemble (on les creuse dans une séance dédiée - le header et les cartes du site de C2P les utilisent déjà).

`position` décide comment un élément se place par rapport au reste de la page :
- `static` : le comportement par défaut, aucun positionnement spécial.
- `relative` : l'élément reste à sa place, mais on peut le décaler avec `top`/`left` sans que les autres éléments ne bougent.
- `absolute` : l'élément sort du flux normal et se positionne par rapport à son parent le plus proche qui a `position: relative` (ou `absolute`).
- `fixed` : l'élément reste à la même position même quand on fait défiler la page.

---

## 11. Pseudo-classes et pseudo-éléments

Une **pseudo-classe** cible un état particulier d'un élément qui existe déjà :
```css
a:hover {
  color: green;
}
a:focus-visible {
  outline: 2px dashed green;
}
```
`:hover` s'applique pendant que la souris survole le lien. `:focus-visible` s'applique quand on y arrive au clavier (touche Tab) - c'est ce qui permet à quelqu'un qui n'utilise pas de souris de voir où il se trouve sur la page.

Un **pseudo-élément** crée quelque chose qui n'existe pas dans le HTML :
```css
.carte::before {
  content: "";
  display: block;
  width: 20px;
  height: 20px;
}
```
`::before` et `::after` ajoutent un élément juste avant ou juste après le contenu réel, uniquement visuel - rien de tout ça n'apparaît si on lit le code source HTML. C'est la technique derrière les icônes et la ligne de contrôle du site de C2P.

Quelques pseudo-classes utiles à connaître, au-delà de `:hover` et `:focus-visible` :

| Pseudo-classe | Ce qu'elle cible |
|---|---|
| `:first-child` | un élément qui est le premier de sa fratrie |
| `:last-child` | un élément qui est le dernier de sa fratrie |
| `:nth-child(2)` | le deuxième élément d'une fratrie (marche aussi avec `odd`, `even`, ou une formule) |
| `:not(.carte)` | tout élément qui NE correspond PAS à ce sélecteur |
| `:disabled` | un champ de formulaire désactivé |

```css
.carte:nth-child(2) {
  transform: translateY(16px);
}
```

**Les commentaires**, pour laisser des notes dans le code sans qu'elles s'affichent :
```css
/* ceci est un commentaire, le navigateur l'ignore complètement */
.carte {
  color: black; /* on peut aussi commenter en bout de ligne */
}
```
Contrairement au HTML (`<!-- -->`), le CSS utilise `/* */`. Un fichier CSS bien commenté, découpé en sections, est beaucoup plus facile à reprendre trois mois plus tard - le tien en est un exemple.

---

## 12. Le responsive, en une idée

Une **media query** applique des règles CSS seulement si une condition sur l'écran est vraie :
```css
@media (max-width: 640px) {
  header {
    flex-wrap: wrap;
  }
}
```
Ici, ces règles ne s'activent que si la largeur de la fenêtre est de 640 pixels ou moins - typiquement, un téléphone. En dehors de cette condition, elles sont simplement ignorées. C'est ce qui permet à une même page de s'afficher correctement sur un ordinateur et sur un téléphone, sans créer deux sites différents.

## 13. Les propriétés raccourcies

Certaines propriétés regroupent plusieurs réglages en une seule ligne. `margin` en est une :
```css
/* Ces deux écritures font exactement la même chose */
margin-top: 10px;
margin-right: 20px;
margin-bottom: 10px;
margin-left: 20px;

margin: 10px 20px;
```
Avec deux valeurs, la première s'applique en haut/bas, la seconde à gauche/droite. Avec quatre valeurs, l'ordre suit le sens d'une horloge : haut, droite, bas, gauche. `padding`, `border`, `background` et `font` fonctionnent sur le même principe : plusieurs propriétés détaillées, condensées en une seule.

## 14. Les variables CSS (propriétés personnalisées)

On peut donner un nom à une valeur, et la réutiliser partout dans le fichier :
```css
:root {
  --plan-bleu: #1683D0;
}

a {
  color: var(--plan-bleu);
}
```
`:root` désigne le sommet du document - l'endroit habituel où déclarer ses variables pour qu'elles soient valables sur toute la page. Le double tiret (`--plan-bleu`) fait partie du nom, ce n'est pas une faute de frappe. On récupère ensuite la valeur avec `var(--plan-bleu)`.

L'intérêt : si la couleur de la marque change un jour, on la modifie à un seul endroit, et tout le site suit. C'est exactement comme ça que le fichier `style.css` de C2P est construit - toutes les couleurs sont des variables déclarées tout en haut.

---

## 15. `transform` : déplacer, tourner, sans casser la mise en page

`transform` change l'apparence visuelle d'un élément sans toucher à la place qu'il occupe dans la page - les éléments autour ne bougent pas, même si celui-ci pivote ou grandit.
```css
.carte {
  transform: rotate(-1.4deg);
}
.carte:hover {
  transform: translateY(-4px);
}
```
- `rotate(-1.4deg)` : incline l'élément.
- `translateY(-4px)` : le déplace verticalement, sans changer sa position dans le flux normal (contrairement à `margin-top`, qui pousserait aussi les voisins).
- Il existe aussi `translateX()`, `scale()` (agrandir/rétrécir), et on peut cumuler plusieurs transformations dans une seule déclaration : `transform: rotate(2deg) translateY(10px)`.

**`transition`** anime le passage d'une valeur à une autre, au lieu d'un changement brutal :
```css
.carte {
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}
```
On lit : "quand `transform` ou `box-shadow` changent, prends 0.2 seconde pour y arriver, avec une accélération douce (`ease`)." Sans cette ligne, le survol de la fiche du site de C2P changerait de position d'un coup sec, sans mouvement fluide.

## 16. `clip-path` : découper une forme

Par défaut, tout élément HTML est un rectangle. `clip-path` permet de le découper selon une forme qu'on décrit soi-même :
```css
.carte {
  clip-path: polygon(0% 0%, 100% 0%, 100% 100%, 0% 100%);
}
```
Cette ligne, à elle seule, redessine un simple rectangle - quatre coins, dans l'ordre. En donnant plus de points, avec des hauteurs légèrement différentes, on obtient un bord irrégulier, comme une feuille déchirée : c'est exactement la technique derrière les fiches "papier" du site de C2P. Chaque paire de nombres est un point `x% y%` du contour à suivre, dans l'ordre où on les écrit.

## 17. Les dégradés

Un dégradé est une valeur qu'on donne à `background-image`, pas une propriété à part :
```css
.hero {
  background-image: linear-gradient(180deg, #1683D0, #020F1D);
}
```
`linear-gradient` va d'une couleur à l'autre en ligne droite - ici de haut en bas (`180deg`). `radial-gradient` part du centre et s'étend en cercle :
```css
.pastille {
  background-image: radial-gradient(circle, #ffffff, #0bd42d 65%);
}
```
On peut empiler plusieurs dégradés (séparés par des virgules) sur un même élément - c'est ce qui donne la texture du fond de page sur le site de C2P : plusieurs dégradés discrets, superposés.

## 18. Les compteurs CSS

Un compteur numérote automatiquement une liste d'éléments, sans écrire les numéros dans le HTML :
```css
.liste-seances {
  counter-reset: seance;
}
.liste-seances li {
  counter-increment: seance;
}
.liste-seances li::after {
  content: "N°" counter(seance);
}
```
- `counter-reset: seance` : crée un compteur nommé "seance", remis à zéro à cet endroit.
- `counter-increment: seance` : ajoute 1 à chaque `<li>` rencontré.
- `counter(seance)` : affiche la valeur actuelle, utilisable uniquement dans une propriété `content`.

Si on ajoute un septième `<li>` dans le HTML plus tard, il reçoit automatiquement le numéro 7 - rien à recalculer à la main.

## 19. `clamp()` et `repeat(auto-fit, minmax())`, expliqués pour de vrai

Ces deux techniques étaient citées en commentaire dans le style.css de C2P, sans être détaillées jusqu'ici - les voici.

**`clamp(minimum, valeur préférée, maximum)`** donne une valeur qui suit l'écran, sans jamais sortir d'une fourchette :
```css
h1 {
  font-size: clamp(1.8rem, 4vw + 1rem, 3rem);
}
```
La taille réelle du titre est `4vw + 1rem`, mais elle ne descend jamais sous `1.8rem` ni ne dépasse `3rem`. Une seule ligne remplace ce qu'il aurait fallu écrire avec plusieurs `@media`.

**`repeat(auto-fit, minmax(240px, 1fr))`**, utilisée avec `display: grid`, crée autant de colonnes de 240px minimum que la largeur le permet, et les étire pour combler l'espace :
```css
.approche {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
}
```
Sur un grand écran, ça donne trois colonnes. Sur un téléphone, une seule - sans écrire une seule media query pour ça. `auto-fit` compte combien de colonnes de 240px tiennent dans la largeur disponible, et `1fr` les étire ensuite pour remplir l'espace qui reste.

## 20. `object-fit`, pour les images qui ne rentrent jamais bien

Quand on force une image dans une taille précise, elle s'étire souvent de travers. `object-fit` règle ça :
```css
img {
  width: 100%;
  height: 240px;
  object-fit: cover;
}
```
`cover` remplit tout l'espace donné, recadre ce qui dépasse, sans jamais déformer l'image - le même principe que le recadrage automatique d'une photo de profil. `contain` fait l'inverse : toute l'image reste visible, quitte à laisser un espace vide autour.

---



| Mot | Ce que ça veut dire |
|---|---|
| **règle** | un sélecteur + son bloc de déclarations |
| **sélecteur** | ce qui désigne à quel(s) élément(s) une règle s'applique |
| **déclaration** | une paire propriété/valeur, terminée par `;` |
| **propriété** | ce qu'on modifie (`color`, `padding`...) |
| **valeur** | ce qu'on donne à la propriété (`blue`, `20px`...) |
| **classe** | un sélecteur réutilisable, avec un point (`.carte`) |
| **id** | un sélecteur unique par page, avec un dièse (`#header-principal`) |
| **cascade** | l'ensemble des règles qui décident quelle déclaration l'emporte en cas de conflit |
| **spécificité** | le "poids" d'un sélecteur, utilisé par la cascade |
| **héritage** | la transmission automatique de certaines valeurs des parents vers les enfants |
| **combinateur** | un symbole (` `, `>`, `+`, `~`) qui relie deux sélecteurs |
| **pseudo-classe** | cible un état (`:hover`) - commence par un deux-points |
| **pseudo-élément** | crée un élément visuel qui n'existe pas dans le HTML (`::before`) - commence par deux deux-points |
| **box model** | les quatre couches de toute boîte : content, padding, border, margin |
| **box-sizing** | décide si `padding`/`border` sont comptés dans le `width` ou ajoutés par-dessus |
| **media query** | une règle CSS activée seulement sous certaines conditions d'écran |
| **propriété raccourcie** | une propriété qui en regroupe plusieurs (`margin` pour les quatre côtés) |
| **variable CSS** (propriété personnalisée) | une valeur nommée, réutilisable avec `var()` |
| **at-rule** | une règle qui commence par `@` (`@media`, `@import`...) |
| **transform** | déplace, tourne ou redimensionne un élément sans affecter les autres |
| **transition** | anime un changement de valeur au lieu de le rendre brutal |
| **clip-path** | découpe un élément selon une forme qu'on décrit par des points |
| **dégradé** (`gradient`) | une transition progressive entre plusieurs couleurs, utilisée comme fond |
| **compteur CSS** | un numéro calculé automatiquement (`counter-reset`, `counter-increment`, `counter()`) |
| **`clamp()`** | une valeur qui suit l'écran, entre un minimum et un maximum fixés |
| **`object-fit`** | décide comment une image se recadre dans un espace de taille fixe |

---

## 21. Bonnes pratiques à prendre dès le début

- **Nommer les classes par leur rôle, pas par leur apparence.** `.carte` plutôt que `.boite-blanche` - si un jour la carte change de couleur, le nom reste juste.
- **Organiser le fichier par sections**, avec des commentaires (`/* ... */`) qui annoncent chaque partie.
- **Préférer les classes aux `id`** pour le style, et garder les `id` pour les cas où on a vraiment besoin d'un identifiant unique.
- **Un seul fichier CSS pour commencer.** On sépare en plusieurs fichiers seulement quand un projet devient trop gros pour s'y retrouver - pas avant.

---

## 22. Pour aller plus loin

- MDN, module "Bases de la présentation CSS" - developer.mozilla.org/fr/docs/Learn_web_development/Core/Styling_basics - le parcours officiel de Mozilla, en français, pensé pour les débutants complets.
- Grafikart (grafikart.fr) - tutoriels vidéo gratuits, en français, du niveau débutant à avancé.
- CSS Zen Garden (csszengarden.com) - pour voir la même page HTML habillée de centaines de façons différentes.

Ce document couvre bien plus que ce qu'on peut voir en direct en deux heures. Les sections 13 et 14 (propriétés raccourcies, variables CSS), le glossaire complet, et cette liste de liens sont volontairement plus riches que la séance elle-même - ils restent disponibles comme référence à consulter après, pas à dérouler en entier ce soir.

Les sections 15 à 20 (`transform`, `transition`, `clip-path`, dégradés, compteurs, `clamp()`, `object-fit`) sont différentes : ce ne sont pas des notions à lire en survol, ce sont les techniques utilisées dans l'atelier pratique de ce soir, sur le header, les cartes, les listes et le footer. Voir `creer-composants-css.md` pour le déroulé, ligne par ligne.