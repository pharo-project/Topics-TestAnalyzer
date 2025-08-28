# Objectif : Analyseur de tests et graphe de fingerprints

Lien code: https://github.com/Major1709/Pharo

## Contexte
Lorsqu’un projet contient des tests unitaires qui échouent, il est difficile de savoir par lequel commencer.  
L’article *Ordering Broken Unit Tests for Focused Debugging* (Gaelli, Lanza, Nierstrasz, Wuyts, ICSM 2004) propose une stratégie : identifier le test le plus **spécifique** car le corriger permet souvent de corriger les autres.

## Étape 1 – Message Analyseur
- Pour chaque méthode de test, on construit un **fingerprint** : l’ensemble des messages (selectors) utilisés par la méthode.
- Les messages envoyés à `self` (par ex. `assert:`, `deny:`, `assertEmpty:`…) sont ignorés car ils proviennent du framework de test (SUnit).
- Exemple :

```smalltalk
testBuildPublicationPage
  | builder |
  site rawPath: root.
  builder := site buildPublicationPage: self publicationPageName.
  self assert: builder sections isNotEmpty.
```

➡️ Fingerprint = `#(rawPath: buildPublicationPage: publicationPageName sections isNotEmpty)`

## Étape 2 – Graphe de Fingerprints
- On compare les fingerprints entre eux.  
- Inclusion : un fingerprint **f1 est inclus dans f2** si tous les messages de f1 apparaissent dans f2.
- On construit un **graphe d’inclusion** entre les fingerprints.
- Le test **à corriger en premier** est celui qui est **le plus inclus** (une feuille sans enfant dans ce graphe).

## Objectif pratique
- Développer un **Visitor AST en Pharo** pour extraire les messages d’une méthode (fingerprint).
- Construire ensuite un **comparateur de fingerprints** produisant un graphe d’inclusion.
- Publier le code avec des **tests unitaires**.

## Références
- Ducasse & Polito, *Fun with Interpreters in Pharo* (chap. 2 et 4) → AST & Visitor.  
- Gaelli et al., *Ordering Broken Unit Tests for Focused Debugging*.  
- Reichhart et al., *Rule-based Assessment of Test Quality* (Test Smells).  
- *Testing in Pharo* (SUnit).

