

# AlgoJeux — Exercices d'algorithmique interactifs

**Type :** Application web autonome (HTML + CSS + JavaScript, sans dépendances)
**Fichier :** AlgoJeux.html
**Public :** Étudiants en algorithmique (niveau L1/L2 ou équivalent)

## 1. Description

AlgoJeux est une application web mono-fichier qui propose **9 exercices d'algorithmique** classiques. L'étudiant écrit son code JavaScript directement dans un éditeur intégré, clique sur **« Tester mon code »**, et l'application exécute automatiquement une série de tests unitaires sur sa fonction ou sa classe, avec un retour visuel immédiat (tests réussis / échoués, message d'erreur, résultat attendu vs obtenu).

La progression est sauvegardée localement et affichée sur la page d'accueil via une barre de progression (x/9 exercices réussis).

## 2. Fonctionnalités

- **9 exercices** couvrant structures de données et algorithmes fondamentaux
- **Éditeur de code** intégré (textarea stylisé, sauvegarde automatique dans localStorage)
- **Tests automatiques** exécutés dans un scope isolé (new Function), sans rechargement de page
- **Retour détaillé** : nom du test, statut ✅/❌, valeur attendue, valeur reçue, message d'erreur
- **Barre de progression** et compteur global (persistés via localStorage)
- **Bouton « Reset »** pour effacer le code d'un exercice
- **Bouton « Voir solution »** (avec confirmation) pour afficher la correction
- Aucune dépendance externe : un seul fichier HTML à ouvrir dans un navigateur

## 3. Les 9 exercices

| # | ID | Exercice | Thème | Type |
|---|-----|----------|-------|------|
| 1 | merge | Tri fusion | Diviser pour régner / récursivité | Fonction |
| 2 | hash | Table de hachage | Hachage (polynomial, `% 100`) | Fonction |
| 3 | stack | Pile (LIFO) | Structure de données | Classe |
| 4 | bst | Insertion dans un BST | Arbre binaire de recherche | Fonction |
| 5 | heap | Min-heap | Tas binaire (tamisation) | Classe |
| 6 | bfs | Parcours en largeur | Graphes (file FIFO) | Fonction |
| 7 | dij | Dijkstra | Plus court chemin pondéré | Fonction |
| 8 | dp | Fibonacci (DP) | Programmation dynamique O(n) | Fonction |
| 9 | np | Détection de cycle | DFS orienté (sommets « en cours ») | Fonction |

## 4. Utilisation

1. Ouvrir AlgoJeux.html dans un navigateur moderne (Chrome, Firefox, Edge, Safari).
2. Cliquer sur une carte d'exercice pour ouvrir l'éditeur.
3. Écrire la fonction/classe demandée en respectant le **nom exact** et la **signature** indiqués dans l'énoncé.
4. Cliquer sur **▶ Tester mon code**.
5. Lire les résultats ; corriger jusqu'à obtenir tous les tests au vert.
6. Revenir à l'accueil : la carte passe en « ✅ Réussi » et la barre de progression avance.

### Exemple (exercice Tri fusion)

js
function triFusion(tab) {
  if (tab.length <= 1) return tab;
  const mid = Math.floor(tab.length / 2);
  const g = triFusion(tab.slice(0, mid));
  const d = triFusion(tab.slice(mid));
  const r = [];
  let i = 0, j = 0;
  while (i < g.length && j < d.length) {
    if (g[i] <= d[j]) r.push(g[i++]);
    else r.push(d[j++]);
  }
  while (i < g.length) r.push(g[i++]);
  while (j < d.length) r.push(d[j++]);
  return r;
}


## 5. Architecture du fichier

Le fichier est organisé en blocs clairement délimités par des commentaires :

| Bloc | Rôle |
|------|------|
| <style> | Feuille de style complète (thème sombre, cartes, éditeur, résultats) |
| const EXOS = [...] | Définition des 9 exercices : énoncé, code de départ, tests |
| État (S, save, refresh) | Gestion de la progression via localStorage |
| renderHome() | Affichage de la grille de cartes |
| openExo(i) | Ouverture d'un exercice, chargement du code sauvegardé |
| runTests(ex) | Évaluation du code et exécution des tests |
| const SOLUTIONS = {...} | Corrections pour chaque exercice |
| Utils (escapeHtml, back) | Fonctions utilitaires |

### Modèle d'un exercice (objet `EXOS`)

js
{
  id: 'merge',          // identifiant unique (clé localStorage)
  t: 'Tri fusion',      // titre affiché
  e: '🔀',              // emoji
  consigne: '<h2>...</h2>', // HTML de l'énoncé
  starter: '...',       // code initial dans l'éditeur
  fn: 'triFusion',      // nom de la fonction/classe à tester
  isClass: false,       // true si l'exercice attend une classe
  tests: [              // tableau de tests
    { name: '...', args: [[3,1,2]], expected: [1,2,3] },
    { name: '...', args: ['abc'], check: v => v >= 0 },
    // ...
  ]
}


Trois formats de test sont supportés :
- **expected** : comparaison stricte via JSON.stringify` (fonctions pures)
- **check** : fonction de validation personnalisée recevant la sortie
- **compare** (optionnel) : second appel de la fonction pour comparer deux sorties (ex. déterminisme du hachage)

## 6. Persistance des données

Deux clés localStorage sont utilisées :

| Clé | Contenu |
|-----|---------|
| algojeux_exos | Objet { passed: ['merge', 'stack', ...] } — exercices validés |
| algojeux_code_<id> | Code saisi par l'utilisateur pour chaque exercice |

Pour réinitialiser toute la progression : vider le localStorage du navigateur pour l'origine du fichier.

## 7. Limitations connues

- **Exécution du code utilisateur via new Function** : le code saisi est évalué dans la page. C'est acceptable pour un usage pédagogique local, mais **à ne pas exposer en production** sans sandbox (Web Worker, iframe isolée).
- **Pas de limite de temps** : une boucle infinie dans le code de l'utilisateur bloquera l'onglet.
- **Dépendance au localStorage** : la progression n'est pas synchronisée entre appareils, et est perdue en navigation privée ou après nettoyage.
- **Tests « boîte blanche »** : certains tests vérifient la forme du résultat (ex. arbre.gauche.valeur), ce qui impose une représentation de nœud précise ({valeur, gauche, droite}).
- **Pas de gestion multi-langages** : uniquement JavaScript.

## 8. Pistes d'amélioration

- Isoler l'exécution du code utilisateur dans un **Web Worker** avec timeout (protection contre les boucles infinies).
- Ajouter un **mode examen** (sans bouton « Voir solution »).
- Proposer un **export/import** de la progression (JSON).
- Ajouter des exercices (tris par insertion/bulle, recherche binaire, parcours DFS, arbre AVL, etc.).
- Afficher un **récapitulatif par thème** (structures de données / graphes / DP).
- Rendre les tests **plus tolérants** sur la forme quand seule la sémantique compte.



