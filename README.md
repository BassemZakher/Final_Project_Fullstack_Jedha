
# 🏦 Stratégies de Crédit et Calibration du Risque : Comparatif Stratégique India vs USA

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Neon-336791)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811)
![Status](https://img.shields.io/badge/Status-Completed-success)

## 📄 Contexte et Objectifs

Dans un contexte où l’accès au crédit est un levier de développement économique, ce projet simule une mission de conseil pour une grande banque indienne.

**Le Défi :** La banque manque de données historiques sur les défauts de paiement pour définir un "bon" emprunteur.  
**La Solution :** Une approche par Benchmark. Nous utilisons un jeu de données mature des États-Unis (historique de remboursement) pour identifier les profils à risque, afin de calibrer et automatiser les décisions d'octroi de crédit en Inde.

### Les Jeux de Données
1.  **Loan Approval (Inde) :** Données actuelles de demandes de prêt (Cible : `Approved` / `Rejected`).
2.  **Loan Confirmation (USA) :** Historique de remboursement (Cible : `Paid` / `Default`).

---

## ⚙️ Architecture Technique & Pipeline de Données

Nous avons mis en place un pipeline ELT (Extract, Load, Transform) complet, interconnectant Python, une base de données Cloud et des outils de Business Intelligence.

### 1. Flux de Données
* **Source :** Kaggle (Datasets bruts).
* **Traitement :** VS Code (Python / Pandas) pour le nettoyage et le Feature Engineering.
* **Stockage :** Neon (PostgreSQL) pour la centralisation.
* **Visualisation :** Power BI connecté en direct à la base de données.

### 2. Stratégie de Données : Architecture "Medaillon"
Pour garantir l'intégrité des données, nous avons structuré la base de données Neon en couches distinctes, sans jamais écraser la donnée source :

| Couche (Layer) | Nom de la Table (SQL) | Description & Usage |
| :--- | :--- | :--- |
| **Bronze (Raw)** | `loan_approval_raw` | Donnée brute, telle qu'importée du CSV. Sert de backup immuable. |
| **Silver (Clean)** | `loan_approval_clean` | Donnée nettoyée, typée et standardisée. Source principale pour **Power BI** (contient les ID et libellés). |
| **Gold (ML Ready)** | *(Géré via Python)* | Vue optimisée pour le Machine Learning : suppression des ID, encodage des variables catégorielles (One-Hot/Label Encoding). |

---

## 🛠️ Méthodologie et Transformations

### Phase 1 : Nettoyage et Feature Engineering
Avant l'analyse, les données ont subi un traitement rigoureux (Script Python) :
* **Nettoyage Standard :** Suppression des espaces (`strip`), correction des types (ID en string).
* **Création de segments (Binning) :**
    * *Âge :* <25, <35, <49, <60, >=60.
    * *Montant du prêt :* Segments de 1k$, 2k$, 3k$+.
* **Calcul de Ratios :** `debt_to_income_rating` classé en 4 niveaux (Excellent <20% à Danger >43%).
* **Harmonisation des Scores de Crédit :** Mapping du Score CIBIL (Inde) pour qu'il soit comparable aux standards FICO (USA).

### Phase 2 : Modélisation (Machine Learning)
* **Objectif :** Entraîner un modèle supervisé pour automatiser la décision d'approbation.
* **Algorithmes testés :** Régression Logistique vs Random Forest.
* **Processus :** Entraînement sur les profils USA (Risque avéré) pour prédire les décisions sur le marché Indien.

---

## 📂 Structure du Projet

```bash
projet-banking-risk
│
├── dashboard/
│   └── PBI - Loan Approval Analysis_finalv2.pbix  # Dashboard Power BI final
│
├── dataset/
│   ├── raw/          # Datasets bruts (ne pas modifier)
│   └── processed/    # Datasets nettoyés (Silver layer)
|
├── image/
│   └── photos - All graphics of notebook  # All final photos
│
├── notebooks/
│   └── Loan_Final_Project.ipynb  # Pipeline complet : Cleaning, EDA, ML
|
├── report/
│   ├── Dafs-15_Group-2_DemoDay_Loan_Project.mp4  # Video support de présentation
|   └── Dafs-15_Group-2_DemoDay_Loan_Project.pdf # Pdf support de présentation
│
├── requirements.txt # Dépendances Python (pandas, scikit-learn, psycopg2, etc.)
└── README.md
