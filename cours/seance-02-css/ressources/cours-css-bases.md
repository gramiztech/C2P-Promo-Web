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

```
┌─────────────────────────────┐
│           margin             │  espace extérieur, hors de la boîte
│  ┌─────────────────────────┐ │
│  │         border           │ │  le contour visible
│  │  ┌─────────────────────┐ │ │
│  │  │       padding        │ │ │  espace intérieur, avant le contenu
│  │  │  ┌─────────────────┐ │ │ │
│  │  │  │     content      │ │ │ │  le texte ou l'image
│  │  │  └─────────────────┘ │ │ │
│  │  └─────────────────────┘ │ │
│  └─────────────────────────┘ │
└─────────────────────────────┘
```

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

---

## 13. Bonnes pratiques à prendre dès le début

- **Nommer les classes par leur rôle, pas par leur apparence.** `.carte` plutôt que `.boite-blanche` - si un jour la carte change de couleur, le nom reste juste.
- **Organiser le fichier par sections**, avec des commentaires (`/* ... */`) qui annoncent chaque partie.
- **Préférer les classes aux `id`** pour le style, et garder les `id` pour les cas où on a vraiment besoin d'un identifiant unique.
- **Un seul fichier CSS pour commencer.** On sépare en plusieurs fichiers seulement quand un projet devient trop gros pour s'y retrouver - pas avant.

---

## 14. Pour aller plus loin

- MDN (Mozilla) - la référence la plus complète et la plus fiable sur CSS, en français : developer.mozilla.org
- web.dev/learn/css - un parcours structuré, par Google, gratuit
- CSS Zen Garden (csszengarden.com) - pour voir la même page HTML habillée de centaines de façons différentes
