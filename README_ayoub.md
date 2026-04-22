# 🏔️ Alpitrail — Sales Performance Analytics

> Projet Data Analyst complet · Python · SQL · Machine Learning · Power BI

---

## 📌 Contexte business

**Alpitrail** est une marque fictive spécialisée dans les équipements outdoor (trail running, hiking, jackets, backpacks, accessories), opérant sur 3 marchés : **Europe**, **Amérique du Nord** et **Asie**.

**Problématique :** Comment optimiser les performances commerciales par région, catégorie et segment client afin de maximiser la rentabilité ?

---

## 🗂️ Structure du projet

```
alpitrail-data-project/
│
├── data/
│   ├── alpitrail_sales_dirty_dataset.csv      # Dataset brut (avec anomalies)
│   ├── alpitrail_sales_version_final.csv      # Dataset nettoyé
│   ├── erp_sales_transactions.csv             # Table de faits ERP
│   └── crm_customer_database.csv             # Référentiel clients CRM (1 500 comptes)
│
├── sql/
│   └── alpitrail_queries.sql                  # 4 requêtes analytiques avancées
│
├── python/
│   └── alpitrail_cleaning_analysis.ipynb      # Notebook Google Colab complet
│
├── dashboard/
│   └── screenshots/                           # Captures Power BI
│
├── documentation/
│   └── workflow_alpitrail.pdf                 # Workflow complet du projet
│
└── README.md
```

---

## 🔄 Pipeline du projet

```
Génération données  →  Nettoyage Python  →  Stockage SQL  →  Jointure CRM/ERP  →  ML  →  Dashboard
     (Colab)             (Pandas/NumPy)      (BigQuery)        (SQLite/DuckDB)    (Sklearn)  (Power BI)
```

---

## 📊 Dataset

| Indicateur | Valeur |
|---|---|
| Nombre de transactions | 3 050 lignes |
| Période couverte | 2023 – 2024 |
| Régions | Europe, North America, Asia |
| Catégories produits | Trail Shoes, Hiking Shoes, Jackets, Backpacks, Accessories |
| Comptes clients CRM | 1 500 |
| CA total simulé | ~4,86 M$ |

---

## 🧹 Étape 1 – Nettoyage des données (Python)

Librairies : `pandas`, `numpy`

Actions réalisées :
- Suppression des doublons
- Suppression des colonnes inutiles (`random_notes`, `temp_id`)
- Normalisation du texte (régions, pays, catégories)
- Traitement des dates + extraction année / mois / saison
- Réparation des régions manquantes (`Unknown` → mapping pays)
- Correction des valeurs négatives (remplacement par la médiane)
- Traitement des outliers sur `unit_price` (méthode IQR)
- Calcul des KPI business : `revenue`, `gross_profit`, `margin`, `is_star_product`

```python
# Exemple — Calcul des KPI business
df["revenue"] = df["units_sold"] * df["unit_price"]
df["gross_profit"] = df["revenue"] - (df["units_sold"] * df["production_cost"])
df["margin"] = (df["gross_profit"] / df["revenue"]).fillna(0)
df["is_star_product"] = df["revenue"] >= df["revenue"].quantile(0.90)
```

---

## 🔎 Étape 2 – Analyse SQL

Environnements utilisés : **Google BigQuery** + **DuckDB** + **SQLite (in-memory)**

### Requête 1 — Performance par catégorie et année
```sql
SELECT year, category,
       COUNT(*) AS nb_ventes,
       ROUND(SUM(revenue), 0) AS ca_eur,
       ROUND(AVG(margin) * 100, 2) || '%' AS marge_moyenne
FROM sales
GROUP BY year, category
ORDER BY year DESC, category ASC
```

### Requête 2 — Top 3 produits rentables par région
```sql
WITH Classement AS (
  SELECT region, product_name,
         ROUND(SUM(gross_profit), 0) AS profit_total,
         RANK() OVER (PARTITION BY region ORDER BY SUM(gross_profit) DESC) AS rang
  FROM sales
  GROUP BY region, product_name
)
SELECT region, product_name, profit_total
FROM Classement WHERE rang <= 3
ORDER BY region, profit_total DESC
```

### Requête 3 — Top 5 produits par saison
Analyse de la saisonnalité (Printemps, Été, Automne, Hiver) avec `RANK() OVER (PARTITION BY season ...)`.

### Requête 4 — Croissance 2023 vs 2024
```sql
SELECT category,
       ROUND(SUM(CASE WHEN year = 2023 THEN revenue ELSE 0 END), 0) AS ca_2023,
       ROUND(SUM(CASE WHEN year = 2024 THEN revenue ELSE 0 END), 0) AS ca_2024,
       ROUND(((SUM(CASE WHEN year = 2024 THEN revenue ELSE 0 END) /
               NULLIF(SUM(CASE WHEN year = 2023 THEN revenue ELSE 0 END), 0)) - 1) * 100, 2) || '%' AS evolution
FROM sales GROUP BY category ORDER BY evolution DESC
```

---

## 🤝 Étape 3 – Jointure CRM / ERP

Génération d'un référentiel de 1 500 comptes clients avec segmentation (VIP, Standard, SME, Professional) et jointure avec la table de faits pour analyser le CA par segment.

```python
df_customers = pd.DataFrame({
    'customer_id': customer_ids,
    'segment': np.random.choice(['VIP', 'Standard', 'SME', 'Professional'], nb_clients, p=[0.05, 0.25, 0.6, 0.1]),
    'lead_score': np.random.randint(1, 101, nb_clients),
    ...
})
```

---

## 🤖 Étape 4 – Machine Learning

Objectif : prédire `units_sold` à partir des variables explicatives.

**Modèles testés :**
- Régression linéaire (`LinearRegression`)
- Random Forest (`RandomForestRegressor`)

**Métriques d'évaluation :**
- RMSE (Root Mean Squared Error)
- R² (coefficient de détermination)

```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error, r2_score

model = RandomForestRegressor(n_estimators=100)
model.fit(X_train, y_train)
y_pred = model.predict(X_test)
```

---

## 📈 Étape 5 – Dashboard Power BI

KPI affichés :
- **1,5K** clients · **2,00M$** gross profit · **4,86M$** CA total

Visualisations :
- Top 5 produits par revenue
- Distribution du CA par segment CRM
- CA par région (Europe / Asie / Amérique du Nord)
- Évolution semestrielle 2023 → 2024

![Dashboard Alpitrail](dashboard/screenshots/alpitrail_dashboard.png)

---

## 💡 Insights principaux

- **Asie** est le marché le plus dynamique (+15% de CA)
- **Les Jackets** affichent la plus forte croissance 2023→2024 (+40%)
- **Rock Trek** est le produit le plus rentable en Asie
- **Mountain Hike** domine en Europe et Amérique du Nord
- Les **accessoires en hiver** ont la marge la plus faible (impact des remises)
- Le segment **SME** représente la majorité des comptes clients (60%)

---

## 🛠️ Stack technique

| Outil | Usage |
|---|---|
| Python (Pandas, NumPy) | Génération, nettoyage, feature engineering |
| Google Colab | Environnement de développement |
| SQL (BigQuery, DuckDB, SQLite) | Exploration et analyse des données |
| Scikit-Learn | Modèles de Machine Learning |
| Power BI | Dashboard décisionnel |
| GitHub | Versionning et documentation |

---

## 👩‍💻 Auteure

**Ayoub Nechat** — Data Analyst

[![GitHub](https://img.shields.io/badge/GitHub-100000?style=flat&logo=github&logoColor=white)](https://github.com/ton-pseudo)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=flat&logo=linkedin&logoColor=white)](https://linkedin.com/in/ton-profil)

---

*Projet réalisé dans le cadre d'un portfolio Data Analyst — données fictives générées avec Python.*
