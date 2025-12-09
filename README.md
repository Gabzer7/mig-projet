## Objectif
L'objectif est d'automatiser le détourage de trois zones tumorales distinctes pour l'aide au diagnostic :
1.  **Nécrose** (Cœur tumoral mort)
2.  **Œdème** (Gonflement périphérique)
3.  **Tumeur Active** (Zone vascularisée rehaussée)

## Méthodologie & Pipeline de Données

Le défi principal réside dans l'adaptation des données médicales 3D (format `.h5`, 4 modalités) vers un format 2D compatible avec les réseaux de vision par ordinateur standards.

### 1. Ingestion et Mapping RGB
Nous avons développé un script de conversion qui projette les modalités IRM pertinentes sur les canaux RGB pour maximiser l'information visuelle :
**Canal ROUGE (T1ce)** : Cible la **Tumeur Active**.
**Canal VERT (FLAIR)** : Cible l'**Œdème**.
**Canal BLEU (T2)** : Cible la structure anatomique globale.

### 2. Architecture du Modèle
* **Modèle :** YOLO11-Small (`yolo11s-seg`).
* **Type :** Instance Segmentation adapté en Semantic Segmentation (par fusion des masques).
* **Entraînement :** Réalisé sur un sous-ensemble représentatif de **10 000 coupes** (Train/Val/Test split).
* **Infrastructure :** Google Colab (GPU T4).

## Résultats

Le modèle a été validé sur un jeu de test (données jamais vues durant l'entraînement).

**Métriques (Test Set) :**
* **mAP50-95 (Précision Globale) :** 0.43
* **Cohérence Sémantique :** Le modèle parvient à reconstruire la hiérarchie biologique (Nécrose incluse dans la Tumeur Active, elle-même entourée par l'Œdème).

## HOWTO : Reproduction des Résultats

Ce projet est conçu pour être exécuté dans un environnement **Google Colab**.

### Pré-requis
1.  Disposer du dataset **BraTS 2020** (fichiers `.h5`).
2.  Avoir un compte Google Drive pour stocker les données et les modèles sauvegardés.

### Scénario 1 : Entraînement Complet (De A à Z)
Pour reproduire l'ensemble du pipeline (Data Engineering + Entraînement + Validation) :

1.  **Préparation :** Placez vos fichiers `.h5` dans un dossier sur votre Google Drive.
2.  **Lancement :** Ouvrez le notebook principal : `VF.ipynb`.
3.  **Configuration :**
    * Dans la **Cellule 2**, modifiez la variable `SOURCE_DRIVE_PATH` pour qu'elle pointe vers votre dossier Drive contenant les fichiers `.h5`.
4.  **Exécution :** Lancez toutes les cellules (« Exécution » > « Tout exécuter »).
    * *Le script va automatiquement :* Convertir les images, générer l'arborescence Train/Val/Test, entraîner YOLO11-Small et sauvegarder le modèle `best.pt`.

### Scénario 2 : Inférence Uniquement (Test Rapide)
Cette méthode permet de tester le modèle sur de nouvelles images sans relancer l'entraînement.

1.  **Récupération du Modèle :** Assurez-vous d'avoir le fichier `modele_vf.pt` (téléchargeable depuis le repo).
2.  **Initialisation :** Ouvrez le notebook `VF.ipynb`.
3.  **Installation des dépendances :** Exécutez uniquement la **Cellule 1** (Installation des librairies `ultralytics`, `h5py` et montage du Drive).
4.  **Lancement du Dashboard :**
    * Allez directement à la **Dernière Cellule** (Section "Visualisation / Dashboard").
    * Modifiez la variable `MODEL_PATH` avec le chemin vers votre fichier `modele_vf.pt`.
    * Exécutez la cellule pour générer les comparaisons visuelles (IRM brutes vs Prédiction).