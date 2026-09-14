# Publipostage Grist

*🇬🇧 An English version of this document is available [at the bottom of this page](#publipostage-for-grist).*

Rédigez un modèle de courrier une seule fois, avec de vraies variables piochées dans vos tables Grist,
et générez un PDF personnalisé pour chaque ligne, en un clic ou par centaines d'un coup. Pas de champs
de fusion Word à bricoler, pas d'export CSV vers un autre outil : tout se passe directement dans Grist,
dans un éditeur de texte riche aussi confortable qu'un traitement de texte classique, et le PDF produit
a un texte réellement sélectionnable, pas une capture d'écran de votre mise en page.

## Aperçu

| Mode édition | Mode lecture |
|---|---|
| ![Édition d'un modèle avec des variables sous forme de badges](screenshots/edition-variables.png) | ![Le même document en lecture, variables résolues avec la ligne sélectionnée](screenshots/lecture-resolue.png) |

## Sommaire

- [Fonctionnalités](#fonctionnalités)
- [Installation dans Grist](#installation-dans-grist)
- [Configuration](#configuration)
- [Sécurité et permissions](#sécurité-et-permissions)
- [Dépendances](#dépendances)
- [Licence](#licence)

Moteur d'édition : [TipTap](https://tiptap.dev/)/ProseMirror. Le widget est une simple page statique
hébergée sur GitHub Pages, sans backend ni étape de build.

## Fonctionnalités

**Éditeur de texte riche** : gras, italique, souligné, barré, couleur de texte et de surlignage, tailles
réelles en points et choix de police (5 polices web embarquées dans le PDF), alignement, titres H1 à H6
avec numérotation automatique et sommaire généré à la volée. Listes à puces, numérotées ou cases à
cocher, tableaux redimensionnables, zones 2 colonnes, images en habillage ou en calque devant/derrière
le texte, sauts de page, notes de bas de page, en-têtes et pieds de page avec numérotation. Interface
disponible en français et en anglais.

**Variables** : tapez `#` pour insérer une variable issue d'une colonne de votre table, avec
autocomplétion. Une variable peut aussi venir d'une autre table que celle liée au widget : la première
fois, une petite configuration vous demande comment relier les deux tables, puis c'est mémorisé pour la
suite.

**Deux modes** : en édition, les variables restent affichées comme des badges. En lecture, elles sont
remplacées par les vraies valeurs de la ligne actuellement sélectionnée dans Grist, et se mettent à jour
automatiquement quand vous changez de ligne.

**Modèles** : autant de modèles que nécessaire, enregistrés directement dans votre document Grist. Une
galerie de modèles prêts à l'emploi (contrat de prestation, facture...) permet de démarrer en un clic,
avec la table de données correspondante créée automatiquement si besoin.

**Export PDF** : en qualité vectorielle (texte sélectionnable, fidèle à la mise en page), en impression
navigateur, ou en raster basse/haute qualité. Le nom du fichier peut lui-même contenir des variables.
Export en lot possible : un PDF par ligne de la table, regroupés dans un zip téléchargé en une fois.

## Installation dans Grist

1. Dans une page Grist, ajoutez un widget personnalisé et collez cette URL :
   **`https://grist-factory.github.io/Publipostage-Plus/`**
2. Dans le panneau de configuration du widget, réglez **Select by** sur la table dont les lignes
   serviront de source de données. C'est ce réglage qui permet au widget de suivre la ligne
   sélectionnée : sans lui, le mode lecture n'a pas de ligne à résoudre.
3. Grist demande une autorisation d'accès au chargement du widget (voir
   [Sécurité et permissions](#sécurité-et-permissions) plus bas) : elle est nécessaire au bon
   fonctionnement du widget.

## Configuration

- **Créer ou éditer un modèle** : bouton "Nouveau modèle", ou sélecteur de modèle en haut de
  l'éditeur. Chaque modèle est stocké dans une table Grist interne créée automatiquement (préfixée
  `Publipostage_`), qui n'apparaît pas dans les sélecteurs de table habituels.
- **Variables d'une autre table** : le panneau `#` propose un bouton dédié pour configurer une fois
  pour toutes la correspondance entre deux tables.
- **En-tête et pied de page** : cliquez sur une marge en haut ou en bas de la page pour entrer dans
  l'édition dédiée.
- **Réglages** (icône en haut à droite) : langue de l'interface, touche déclenchant le panneau `#`.
- **Nom du fichier PDF** : champ dédié, qui accepte lui aussi des variables.

## Sécurité et permissions

Le widget demande le niveau d'accès `requiredAccess: 'full'`, c'est-à-dire un accès à l'ensemble du
document Grist et pas seulement à la table à laquelle il est lié. Ce niveau est nécessaire dans
l'architecture actuelle : la résolution de variables vers une autre table, l'autocomplétion sur tout le
document et la gestion des tables internes du widget en ont besoin dès le chargement. L'API des widgets
personnalisés Grist ne propose pas de niveau intermédiaire entre l'accès à une seule table et l'accès
complet.

Cet accès s'applique au widget, pas directement à chaque utilisateur : les Règles d'accès natives de
Grist, configurées sur le document par son propriétaire, continuent de s'appliquer normalement. Un
utilisateur restreint à certaines tables ou colonnes le reste en utilisant le widget. C'est donc au
niveau du document Grist lui-même que doit se faire une éventuelle restriction fine des données, pas
dans la configuration du widget.

Le widget charge deux bibliothèques tierces à l'exécution, depuis `esm.sh` (moteur d'édition
TipTap/ProseMirror) et `cdnjs.cloudflare.com` (génération de PDF). Les fichiers venant de `cdnjs` sont
protégés par une intégrité SRI, qui empêche le navigateur d'exécuter un fichier altéré ; ce n'est
techniquement pas possible pour l'import map `esm.sh`.

Aucune donnée n'est stockée hors de Grist. Les seules informations conservées dans le navigateur
(`localStorage`) sont des préférences d'interface comme la langue, jamais de donnée métier.

Pour signaler une vulnérabilité, merci de ne pas ouvrir d'issue publique et de contacter directement
l'équipe de maintenance de ce dépôt.

## Dépendances

Aucune étape de build : tous les fichiers sont servis tels quels. Les bibliothèques tierces sont
chargées à l'exécution, à des versions toujours figées :

| Bibliothèque | Usage | Origine |
|---|---|---|
| `grist-plugin-api.js` | API du widget Grist (obligatoire) | `docs.getgrist.com` |
| TipTap 3.31.3 + ProseMirror (~20 paquets) + `@floating-ui/dom` | Moteur d'édition riche | `esm.sh` |
| pdfmake 0.2.7 + `vfs_fonts` | Export PDF vectoriel | `cdnjs.cloudflare.com` |
| html2pdf.js 0.10.1 | Export PDF qualité raster | `cdnjs.cloudflare.com` |
| JSZip 3.10.1 | Export en lot (archive zip) | `cdnjs.cloudflare.com` |

## Licence

Ce projet est distribué sous licence [GNU General Public License v3.0](LICENSE) (GPLv3).

Copyright (C) 2026 lombre33

---
---

# Publipostage for Grist

*🇫🇷 Une version française de ce document est disponible [en haut de cette page](#publipostage-grist).*

Write a letter template once, with real variables pulled straight from your Grist tables, and generate
a personalized PDF for each row, one at a time or by the hundred in one go. No mail-merge fields to
wrangle in Word, no CSV export to some other tool: it all happens inside Grist, in a rich text editor
as comfortable as a regular word processor, and the resulting PDF has genuinely selectable text, not a
screenshot of your layout.

## Preview

| Editing mode | Reading mode |
|---|---|
| ![Editing a template with variables shown as badges](screenshots/edition-variables.png) | ![The same document in reading mode, variables resolved with the selected row](screenshots/lecture-resolue.png) |

## Table of contents

- [Features](#features)
- [Installing in Grist](#installing-in-grist)
- [Configuration](#configuration-1)
- [Security and permissions](#security-and-permissions)
- [Dependencies](#dependencies)
- [License](#license)

Editing engine: [TipTap](https://tiptap.dev/)/ProseMirror. The widget is a plain static page hosted on
GitHub Pages, with no backend and no build step.

## Features

**Rich text editor**: bold, italic, underline, strikethrough, text and highlight color, real point sizes
and font choice (5 web fonts embedded in the PDF), alignment, H1 to H6 headings with automatic numbering
and an on-the-fly table of contents. Bullet, numbered or checkbox lists, resizable tables, two-column
zones, images with text wrap or layered in front of/behind the text, page breaks, footnotes, headers and
footers with page numbering. Interface available in French and English.

**Variables**: type `#` to insert a variable from one of your table's columns, with autocomplete. A
variable can also come from a table other than the one linked to the widget: the first time, a short
setup step asks how to relate the two tables, then it's remembered for next time.

**Two modes**: in editing mode, variables stay shown as badges. In reading mode, they're replaced with
the real values of whichever row is currently selected in Grist, and update automatically as you move
between rows.

**Templates**: as many templates as you need, stored directly in your Grist document. A gallery of
ready-made templates (service agreement, invoice...) lets you get started in one click, creating the
matching data table automatically if it doesn't exist yet.

**PDF export**: vector quality (selectable text, faithful to the editor's layout), browser print, or
low/high quality raster. The file name itself can contain variables. Batch export is also available:
one PDF per row of the table, bundled into a single downloaded zip.

## Installing in Grist

1. In a Grist page, add a custom widget and paste this URL:
   **`https://grist-factory.github.io/Publipostage-Plus/`**
2. In the widget's configuration panel, set **Select by** to the table whose rows will be the data
   source. This is what lets the widget follow the selected row: without it, reading mode has no row to
   resolve against.
3. Grist will ask for an access permission when the widget loads (see
   [Security and permissions](#security-and-permissions) below): granting it is required for the widget
   to work.

## Configuration

- **Create or edit a template**: the "New template" button, or the template picker at the top of the
  editor. Each template is stored in an internal Grist table created automatically (prefixed
  `Publipostage_`), which doesn't show up in the usual table pickers.
- **Variables from another table**: the `#` panel has a dedicated button to set up the relationship
  between two tables once, for good.
- **Header and footer**: click a margin at the top or bottom of the page to enter dedicated editing.
- **Settings** (icon top right): interface language, and the key that triggers the `#` panel.
- **PDF file name**: its own field, which also accepts variables.

## Security and permissions

The widget requests `requiredAccess: 'full'`, meaning access to the whole Grist document rather than
just the table it's linked to. This is needed by the current architecture: resolving variables from
another table, autocompleting across the whole document, and managing the widget's own internal tables
all need it from the moment the widget loads. Grist's custom widget API doesn't offer a middle ground
between single-table access and full access.

This access applies to the widget, not directly to each user: Grist's native Access Rules, set on the
document by its owner, still apply as normal. A user restricted to certain tables or columns stays
restricted while using the widget. Any fine-grained restriction of the data should therefore happen at
the Grist document level, not in the widget's configuration.

The widget loads two third-party libraries at runtime, from `esm.sh` (the TipTap/ProseMirror editing
engine) and `cdnjs.cloudflare.com` (PDF generation). Files from `cdnjs` are protected by SRI integrity
hashes, which stop the browser from running a tampered file; this isn't technically possible for the
`esm.sh` import map.

No data is ever stored outside of Grist. The only thing kept in the browser (`localStorage`) is
interface preferences such as the language, never any business data.

To report a vulnerability, please don't open a public issue: contact this repository's maintainers
directly instead.

## Dependencies

No build step: every file is served as-is. Third-party libraries are loaded at runtime, always at
pinned versions:

| Library | Purpose | Source |
|---|---|---|
| `grist-plugin-api.js` | Grist widget API (required) | `docs.getgrist.com` |
| TipTap 3.31.3 + ProseMirror (~20 packages) + `@floating-ui/dom` | Rich text editing engine | `esm.sh` |
| pdfmake 0.2.7 + `vfs_fonts` | Vector PDF export | `cdnjs.cloudflare.com` |
| html2pdf.js 0.10.1 | Raster quality PDF export | `cdnjs.cloudflare.com` |
| JSZip 3.10.1 | Batch export (zip archive) | `cdnjs.cloudflare.com` |

## License

This project is distributed under the [GNU General Public License v3.0](LICENSE) (GPLv3).

Copyright (C) 2026 lombre33
