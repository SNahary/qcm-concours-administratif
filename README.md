# QCM concours – entraînement en ligne

Page d'entraînement aux QCM de culture générale, construite à partir des photos de sujets fournies.

Tout tient dans un seul fichier, `index.html` : questions, réponses, explications et application. Il fonctionne aussi hors ligne, en l'ouvrant simplement dans un navigateur.

## Mettre la page en ligne avec GitHub Pages

1. Créez un compte sur github.com si vous n'en avez pas.
2. Cliquez sur **New repository**, nommez-le par exemple `qcm-concours`, choisissez **Public**, puis **Create repository**.
3. Cliquez sur **uploading an existing file**, déposez `index.html` (et, si vous le souhaitez, le dossier `data`), puis **Commit changes**.
4. Ouvrez **Settings → Pages**. Dans **Build and deployment**, choisissez **Deploy from a branch**, la branche **main** et le dossier **/ (root)**, puis **Save**.
5. Après une à deux minutes, la page est disponible à l'adresse `https://VOTRE-NOM.github.io/qcm-concours/`.

Pour mettre à jour la banque, il suffit de remplacer `index.html` par sa nouvelle version (même procédure d'envoi).

## Fonctionnement

- **Entraînement** : correction immédiate après chaque question, avec explication.
- **Examen blanc** : chronométré (72 s, 108 s ou 45 s par question), correction à la fin.
- **Mes erreurs** : reprend les questions ratées lors de vos séries.
- **Banque** : toutes les questions avec leur réponse, recherche par mot-clé et filtre par thème.
- **Progression** : statistiques par thème.

La progression est enregistrée dans le navigateur de l'appareil utilisé ; elle n'est pas partagée entre téléphone et ordinateur.

## Contenu actuel

**Sujet 1 – Concours d'élèves-officiers d'active, XLVIIIe promotion de l'Académie militaire d'Antsirabe**, culture générale, 19 juin 2025 (100 QCM).

- 100 questions transcrites depuis 9 photos. Elles ont été vérifiées sur l'image quand l'OCR était douteux.
- Aucun doublon au sein de ce sujet. Les 6 photos présentes en double exact dans l'archive ont été écartées.
- 92 questions ont une réponse retenue, dont 8 accompagnées d'une nuance. La question 66 accepte deux réponses.

### Corrections apportées au sujet

- **Q10** : le sujet date le Directoire militaire de « 1972 ». Il a en réalité gouverné du 12 février au 15 juin 1975, présidé par le général Andriamahazo.
- **Q100** : π est le rapport de la circonférence au diamètre, et non l'inverse. L'énoncé a été corrigé.
- **Q4** : l'option « 1987 » est vraisemblablement une coquille.
- **Q11, Q28, Q40, Q41** : orthographe des noms propres corrigée (Kadhafi, Guterres, Annan, Jobs, Rhimes).
- **Q33 et Q70** : fin d'énoncé masquée sur la photo, ce qui est signalé dans la question.

### Questions signalées (exclues des séries, consultables dans la banque)

- **Aucune option exacte** :
  - Q64 : il n'y avait pas de Premier ministre à l'indépendance ;
  - Q69 : Madagascar a adhéré à la SADC en 2005, date absente des options.
- **Réponse à vérifier** :
  - Q48 : auteur de « Fasana faharoa » ;
  - Q68 : nombre de régimes transitoires, qui dépend du décompte ;
  - Q75 : nombre d'armes des Forces armées ;
  - Q84 : emplacement du mausolée ;
  - Q87 : quotidien publiant « Le naïf » ;
  - Q88 : émission de Haja Ratsimbazafy.

**Sujet 2 – Série de tests en tableaux** (conjugaison, orthographe et grammaire, vocabulaire, mathématiques), 80 questions à trois options.

- 80 questions transcrites depuis 9 photos, lues sur l'image car l'OCR ne reconnaît pas les tableaux.
- Les réponses surlignées sur le document ont été contrôlées une à une, et une explication a été ajoutée à chaque question.
- Aucun doublon avec le sujet 1.

### Écarts avec le corrigé du document (sujet 2)

- **Q71** : le document surligne « 2³ × 3³ », qui vaut 216. La bonne réponse est 2³ × 3² (8 × 9 = 72).
- **Q62** : le document surligne 16, qui correspond au nombre de billes *après* le don ; avant le don, on en a 20. Les deux réponses sont acceptées.
- **Q24** : « des abat-jours » (orthographe de 1990) et « des abat-jour » (orthographe traditionnelle) sont tous deux acceptés.
- **Q32** : le syllogisme est un raisonnement plutôt qu'une figure de style, ce qui est signalé comme une nuance.
- **Q49, Q66, Q70, Q78** : formulations ou options imprimées telles quelles (« résoudre l'équation », « triangle droit », « 79 », « 12/07/20 »), avec une note dans la question.

## Format des données

Les questions se trouvent dans `index.html`, dans la balise `<script id="qcm-data">`. Une copie lisible est dans `data/questions.json`.

Chaque question comporte les champs suivants :

- `q` : l'énoncé ;
- `o` : la liste des options ;
- `a` : l'indice de la bonne réponse (0 = a, 1 = b…), ou `null` si aucune n'est retenue ;
- `acc` : les réponses acceptées, s'il y en a plusieurs ;
- `x` : l'explication ;
- `n` : une note de transcription ou de correction ;
- `st` : le statut, parmi `ok`, `nuance`, `corrigee`, `erronee` et `averifier`.
