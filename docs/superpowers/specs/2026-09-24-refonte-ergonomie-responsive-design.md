# Refonte ergonomique et responsive de l'application QCM

Date : 24 septembre 2026
Statut : validé en brainstorming, en attente de relecture

## Objectif

Rendre l'application plus intuitive et confortable sur téléphone, où les candidats révisent surtout, sans changer son identité visuelle ni ses fonctionnalités.

## Décisions prises

| Sujet | Décision |
|---|---|
| Appareil prioritaire | Téléphone (conception « mobile d'abord ») ; l'ordinateur reste soigné. |
| Ampleur | Design et parcours revus ; fonctionnalités et sauvegarde inchangées. |
| Identité visuelle | Conservée et améliorée : jetons de couleur actuels, Atkinson Hyperlegible, bulles a/b/c/d, marge rouge, surligneur, mode sombre. |
| Approche technique | Tout reste dans `index.html` (CSS, HTML et JS vanilla). Pas de découpage en fichiers, pas de build. |
| Réglages de préparation | Mémorisés d'une visite à l'autre. |
| Raccourci par thème | Bouton « S'entraîner » par thème dans Progression. |

## Contraintes

- `index.html` doit toujours s'ouvrir hors ligne (double-clic, `file://`) et sur GitHub Pages.
- Le bloc `<script id="qcm-data">` reste identique à l'octet près. `questions.json` n'est pas modifié.
- La clé de sauvegarde `qcm-concours-v1` et la forme de `store.stats` ne changent pas ; les sauvegardes existantes restent lisibles.
- Les confirmations restent des `confirm()` natifs.
- Aucun défilement horizontal à 360 px de large.

## Points de rupture

- **Mobile** : moins de 48rem (768 px). Barre d'onglets fixée en bas, barres d'action collantes.
- **Étroit** : moins de 40rem (640 px). La feuille (`.sheet`) passe bord à bord (sans marge latérale ni arrondi) et la marge rouge se rétrécit.
- **Ordinateur** : 48rem et plus. Onglets en haut comme aujourd'hui, pas de barre en bas, boutons d'action dans le flux, aide clavier visible.

## 1. Structure et navigation

- **En-tête mobile** : logo et nom seulement, non collant.
- **Espace entre l'en-tête et le contenu** : l'espacement supérieur de `main` passe de 1.25rem à 2rem (32 px) sur mobile et à 2.5rem (40 px) à partir de 48rem. Sur mobile, cet espace est conservé même quand la feuille passe bord à bord, pour que l'en-tête reste nettement séparé du contenu.
- **Onglets** : le même élément `nav.tabs` est placé par CSS en haut sur ordinateur et fixé en bas sur mobile.
  - Chaque onglet reçoit une icône SVG inline, visible sur mobile seulement, en plus de son libellé.
  - L'onglet actif garde `aria-current="page"`.
  - Le padding inférieur de la barre inclut `env(safe-area-inset-bottom)`.
  - Le `body` réserve en bas la hauteur de la barre.
- **Mode concentration** : sur mobile, la barre d'onglets est masquée pendant une série. Le JS pose une classe sur `body` quand la vue `quiz` est affichée. Sur ordinateur, les onglets restent visibles, avec la confirmation actuelle pour quitter.
- **Barre d'action collante** : sur mobile, le dernier bloc d'actions d'une section est en `position: sticky; bottom: <hauteur de la barre d'onglets>`. En mode concentration, cette hauteur vaut 0. Sur ordinateur, les actions redeviennent statiques.
- **Cibles tactiles** : au moins 48 px de haut pour les onglets, options, boutons, puces et segments.
- **Aide clavier** : visible seulement sous `@media (hover: hover) and (pointer: fine)`.

## 2. Préparer une série

Ordre des éléments :

1. Titre « Préparer une série » et résumé : « 303 questions prêtes · 7 à revoir ». La partie « à revoir » est omise s'il n'y a rien à revoir.
2. **Mode**
   - Les trois `input[type=radio][name=mode]` sont conservés, mais présentés en contrôle segmenté de trois parts égales : Entraînement, Examen blanc, Mes erreurs (avec compteur).
   - Sous le contrôle, une ligne décrit le mode choisi.
   - « Mes erreurs » est désactivé quand le compteur vaut 0 ; le repli sur « Entraînement » existe déjà.
3. **Nombre de questions** : le segment actuel (10, 20, 50, Toutes) est conservé.
4. **Temps par question** : la liste actuelle est conservée, visible seulement en examen blanc.
5. **Personnaliser** : un `<details>` fermé par défaut.
   - Son `<summary>` affiche « Personnaliser » et une ligne d'état, par exemple « Tous les sujets · tous les thèmes · réponses mélangées » ou « 2 sujets · 5 thèmes · réponses dans l'ordre ».
   - Contenu :
     - **Sujets** : les puces actuelles, avec les boutons « Tout » et « Aucun ».
     - **Thèmes** : les puces actuelles, avec les boutons « Tout » et « Aucun ». Chaque compteur donne le nombre de questions du thème qui ont une réponse retenue, dans les sujets sélectionnés. Un thème à 0 est grisé et désactivé, mais sa sélection est conservée.
     - La case « Mélanger l'ordre des réponses ».
6. La phrase existante sur les questions signalées, en petit.
7. **Bouton Commencer** : libellé dynamique « Commencer · 20 questions », « Commencer · 1 question » ou « Aucune question ne correspond à ces filtres » (bouton désactivé), dans la barre collante.

**Mémorisation** dans `store.prefs` :

- Champs : `{mode, count, pace, shuffle, series: [ids], themes: [noms]}`.
- La sauvegarde a lieu à chaque changement.
- Au chargement, les identifiants inconnus sont ignorés. Si un ensemble devient vide après ce filtrage, il repart de « tout sélectionné ». `mode: "errors"` sans erreur retombe sur « Entraînement ».

## 3. Écran de question

- **Barre haute** (`.quiz-head`), de gauche à droite :
  - bouton ✕, qui remplace le bouton actuel « Arrêter la série » avec le même comportement et `aria-label="Arrêter la série"` ;
  - barre de progression ;
  - compteur « 4 / 20 » ;
  - en examen, le minuteur (rouge dans la dernière minute, comme aujourd'hui) et un bouton « Grille ».
- **Marge rouge** : sous 40rem, le retrait passe de 3.4rem à environ 2.4rem, avec un numéro plus petit. Au-delà, rien ne change.
- **Options** : hauteur minimale de 48 px.
  - Après correction, une marque ✓ (bonne réponse) ou ✗ (réponse choisie fausse) est ajoutée en fin d'option, en `aria-hidden`.
  - L'`aria-label` du bouton est complété par « , bonne réponse » ou « , votre réponse, fausse ».
  - Le barré, le surligneur et les bulles colorées sont conservés.
- **Entraînement** : après une réponse, le bloc de correction est amené dans la vue avec `scrollIntoView({block: 'nearest'})`.
  - Le défilement est doux, sauf si `prefers-reduced-motion`.
  - Un `scroll-margin-bottom` égal à la hauteur de la barre collante évite que celle-ci le masque.
  - Le focus passe à « Question suivante », comme aujourd'hui.
- **Barre collante du bas** :
  - en entraînement, « Question suivante » ou « Voir le résultat » en pleine largeur ;
  - en examen, « ‹ Précédente » et « Suivante › » ou « Terminer l'examen ».
- **Grille d'examen** :
  - Élément `<dialog>` ouvert avec `showModal()`. Si `showModal` n'existe pas, on pose l'attribut `open`.
  - Titre « Questions », puis une grille de boutons numérotés.
  - Trois états visibles, et pas seulement par la couleur : question courante (contour encre), répondue (fond encre pâle et point), sans réponse (neutre).
  - Toucher un numéro va à la question et ferme la grille.
  - En bas, le bouton « Terminer l'examen » suit le même chemin que le bouton de fin, confirmation des questions sans réponse comprise.
  - Fermeture par ✕, Échap ou clic sur le fond.
  - Focus : il va sur le numéro courant à l'ouverture, puis revient au bouton « Grille » à la fermeture.
- Les raccourcis clavier actuels sont conservés.

## 4. Résultats

- Le bloc de score est inchangé.
- **Barre d'actions collante** sur mobile :
  - « Refaire les questions ratées » (principal) et « Nouvelle série » (secondaire), côte à côte ;
  - si toutes les réponses sont justes, seul « Nouvelle série » est affiché, comme bouton principal.
- **Par thème** : barres triées du plus faible taux de réussite au plus fort. En cas d'égalité, ordre alphabétique.
- **Correction** : la réponse choisie est barrée avec ✗, la bonne réponse surlignée avec ✓. L'explication reste visible. La case « Afficher aussi les bonnes réponses » est conservée.
- Les onglets réapparaissent.

## 5. Banque

- **Zone de recherche collante** en haut sur mobile : le champ et le compteur de résultats. Le filtrage par texte est différé de 150 ms ; les listes filtrent immédiatement.
- **Filtres** :
  - nouveau filtre **Sujet** (« Tous les sujets » et les 4 séries, par leur nom court) ;
  - Thème et Statut existants ;
  - les listes passent à la ligne sur mobile.
- **Chaque question** :
  - énoncé, options avec la bonne réponse surlignée et ✓, badge de statut visible ;
  - explication, note et source dans un `<details>` fermé intitulé « Voir l'explication ».
- Toutes les questions filtrées restent rendues, sans pagination.

## 6. Progression

- **Trois statistiques** sur une seule ligne, y compris sur mobile, en format compact.
- **Maîtrise par thème** : barres triées de la plus faible maîtrise à la plus forte (égalité : ordre alphabétique).
- **Bouton « S'entraîner »** sur chaque ligne :
  - il sélectionne tous les sujets, seulement ce thème et le mode Entraînement ;
  - il enregistre ces réglages ;
  - il ouvre la préparation avec « Personnaliser » déplié.
- « Effacer ma progression » est inchangé.

## 7. Transverse

- Tous les nouveaux éléments utilisent les jetons de couleur existants, en clair comme en sombre.
- Focus visible sur tout élément interactif.
- Les animations sont désactivées sous `prefers-reduced-motion`.
- Aucune dépendance nouvelle. Seule la police Google existante est chargée, avec une pile de repli système.

## Fichiers touchés

- `index.html` :
  - le CSS dans `<style>` ;
  - le HTML des vues ;
  - le JS de l'application.
  - Le bloc `qcm-data` n'est pas touché.
- `README.md` :
  - la section « Fonctionnement » est mise à jour (grille d'examen, réglages mémorisés, filtre Sujet, raccourci par thème) ;
  - le chemin `data/questions.json` est corrigé en `questions.json`.

## Critères d'acceptation

1. Le JSON du bloc `qcm-data` est identique à l'octet près avant et après.
2. À 360, 390, 768 et 1280 px, en clair et en sombre, chaque vue s'affiche sans défilement horizontal, vérifié par une sonde automatique.
3. Sur mobile, le bouton principal de la préparation, de la question et des résultats est visible sans défiler.
4. Une série d'entraînement complète fonctionne : réponse, correction, question suivante, résultat.
5. Un examen blanc fonctionne :
   - le minuteur tourne ;
   - la grille permet de sauter d'une question à l'autre ;
   - on peut terminer avec des questions sans réponse, après confirmation.
6. Le mode « Mes erreurs », la recherche et les filtres de la banque, la progression, sa remise à zéro et le raccourci par thème fonctionnent.
7. Les raccourcis clavier fonctionnent sur ordinateur.
8. Après rechargement, les réglages de préparation sont retrouvés.
9. Une sauvegarde existante (`stats` sans `prefs`) se charge sans erreur.
10. Aucune erreur JavaScript dans la console.

## Hors périmètre

- Nouvelle identité visuelle.
- Nouvelles fonctionnalités (révision espacée, favoris, objectifs).
- Découpage en fichiers, PWA ou service worker.
- Toute modification des questions.
- Remplacement des `confirm()` natifs.
