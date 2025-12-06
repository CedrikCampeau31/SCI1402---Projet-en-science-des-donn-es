# Prédiction des points NHL – Projet final SCI1402

Projet final du Certificat en science des données (SCI1402)  
Auteur : Cédrik Sigouin Campeau

## 1. Contexte et objectif

Dans ce projet, je construis un pipeline complet de machine learning pour prédire le nombre de points (buts + passes) que les joueurs de la LNH (NHL) pourraient obtenir à la saison suivante, en utilisant leurs statistiques historiques des saisons précédentes.

L’idée est de partir de données publiques, de les transformer proprement, de faire une EDA solide, de tester plusieurs modèles, de gérer le côté temporel (pas de fuite d’info sur le futur) et de livrer :

- un notebook complet qui documente le process;
- des artefacts d’EDA et de modèles sauvegardés;
- des fichiers d’output (prédictions, métriques, hypothèses vs résultats).

Ce dépôt est pensé comme un exemple de projet de data science de bout en bout, pas juste un notebook isolé.

## 2. Source de données

Les données viennent du site :  
https://www.quanthockey.com/nhl/seasons/nhl-players-stats.html

- Unité d’analyse : un joueur pour une saison donnée.
- Période couverte : plusieurs saisons récentes (par exemple 2019-2020 à 2024-2025).
- Variables de base (exemples) :
  - Identité : `Player`, `Season`, `Team`, `Position`, `Age`
  - Volume : `GP` (games played), `TOI`, `TOI/GP`
  - Production : `G` (goals), `A` (assists), `P` (points)
- Variables dérivées dans le projet :
  - points par 60 minutes, deltas d’une saison à l’autre,
  - moyennes glissantes, indicateurs de tiers (High/Mid/Low), âge, rôle, etc.

Le chargement et le nettoyage de base sont gérés directement dans le notebook principal.

## 3. Problème de ML et cible

Type de problème : régression supervisée.

- Cible : `Points_next_season` (approximation des points futurs à partir de la dernière saison connue et de l’historique).
- Features :
  - stats de la ou des saisons précédentes;
  - variables dérivées (ratios, rolling stats, progression/déclin);
  - encodage de la position, âge, temps de jeu, etc.

Objectif : atteindre une erreur moyenne raisonnable (MAE) en points, tout en respectant le caractère temporel (train sur passé, test sur futur).

## 4. Pipeline ML (vue d’ensemble)

Tout le pipeline est orchestré dans le notebook :

`SCI1402_Colab_NHL_ML_CedrikSigouinCampeau_Final.ipynb`

### 4.1 ETL / Préparation

- Récupération des stats de QuantHockey.
- Harmonisation des colonnes, types et formats.
- Nettoyage de base : valeurs manquantes, doublons, incohérences.
- Construction de la table finale par joueur/saison.

### 4.2 EDA (Exploration)

- Statistiques descriptives (distribution des points, âge, TOI, etc.).
- Visualisations : distributions, corrélations, scatter plots, boxplots.
- Analyse par segments : position (F/D), tiers de production, âge.

Les figures et tableaux importants sont exportés dans le dossier :

- `artifacts_eda/`

### 4.3 Feature engineering et sélection

- Création de features dérivées :
  - points par 60, indicateurs d’usage, deltas de performance;
  - historiques sur plusieurs saisons (rolling windows);
  - indicateurs de progression/déclin.
- Tests de feature selection (importance, corrélation, modèles d’arbres).

Les scripts / notebooks liés à cette phase sont regroupés dans :

- `featuredselect/`

### 4.4 Modélisation

Modèles testés (selon les cas) :

- baseline simple (reconduction de la saison précédente, éventuellement régression linéaire);
- modèles d’arbres (Random Forest, Gradient Boosting);
- XGBoost ou modèle équivalent comme candidat principal.

Stratégie de validation :

- split temporel (train sur saisons plus anciennes, validation sur saison récente);
- saison la plus récente gardée comme vrai test futur.

Les modèles entraînés, leurs hyperparamètres et les fichiers de résultats intermédiaires sont stockés dans :

- `artifacts_models/`

### 4.5 Évaluation et interprétation

Métriques utilisées :

- MAE : erreur moyenne en points;
- RMSE : pénalise plus fortement les grosses erreurs;
- R² : part de variance expliquée.

Analyses complémentaires :

- comparaison modèle avancé vs baseline;
- focus sur certains profils de joueurs (vedettes, jeunes en progression, low TOI);
- inspection de l’importance des features (feature importance).

Les prédictions finales et les tableaux de synthèse sont écrits dans :

- `Output/`

## 5. Hypothèses et résultats

Le fichier `hypothese_result.xlsx` résume de façon structurée :

- les hypothèses de départ (par exemple le modèle devrait mieux gérer les vedettes en déclin que la baseline, l’impact du temps de jeu, etc.);
- les résultats observés (métriques par segment, cas où le modèle est meilleur ou pire);
- les conclusions et pistes de réflexion.

C’est le lien direct entre la réflexion théorique (cours SCI1402) et ce qui sort concrètement du modèle.

## 6. Structure du dépôt


- SCI1402_Colab_NHL_ML_CedrikSigouinCampeau_Final.ipynb  (Notebook principal (pipeline complet))
- hypothese_result.xlsx                                  (Synthèse hypothèses vs résultats)
- artifacts_eda/                                         (Graphiques, stats et tableaux EDA exportés)
- artifacts_models/                                      (Modèles, logs d'entraînement, métriques intermédiaires)
- featuredselect/                                        (Scripts / notebooks de feature engineering et feature selection)
- Output/                                                (Prédictions finales, tables prêtes à être analysées ou visualisées)
- README.md                                             
