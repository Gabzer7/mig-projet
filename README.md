# Brain Tumor Segmentation with YOLOv8 (BraTS 2020)

## Objectif
Ce projet explore l'utilisation de l'architecture **YOLOv8-seg** (Ultralytics) pour la segmentation instance de tumeurs cérébrales à partir du dataset multimodal BraTS 2020. L'objectif est de proposer une alternative rapide (Real-Time) aux architectures classiques type U-Net.

## Pipeline Technique
1.  **Data Ingestion :** Chargement des volumes IRM au format natif `.h5` (4 canaux : T1, T1ce, T2, FLAIR).
2.  **Preprocessing :**
    * Conversion des slices 2D en images RGB (Fusion des modalités T1ce, FLAIR, T2).
    * Extraction des masques binaires et conversion en **Polygones YOLO**.
3.  **Training :** Fine-tuning du modèle `yolov8n-seg` sur un sous-ensemble du dataset.

## Résultats
Le modèle parvient à localiser et segmenter la tumeur sur les données de validation.

![Exemple de prédiction](prediction_yolo_proof.png)
*(Ajoutez votre image ici)*

## Stack Technique
* Python 3
* YOLOv8 (Ultralytics)
* H5py, OpenCV, NumPy
* Google Colab (T4 GPU)