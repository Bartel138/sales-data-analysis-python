# sales-data-analysis-python
Exploratory Data Analysis (EDA) en Python sur un dataset de ventes : nettoyage, manipulation temporelle avancée (feature engineering) et visualisations macro-économiques.

# 📊 Exploratory Data Analysis & Ventes E-commerce (Python)

Bienvenue sur ce dépôt dédié à l'analyse exploratoire et au traitement de données commerciales en Python. Ce projet met en valeur une démarche rigoureuse de Data Analyst, de la qualité des données (Data Cleaning) jusqu'à la visualisation des tendances macro-économiques.

---

## 🚀 Structure & Briques du Projet

Ce projet s'articule autour d'une approche modulaire :

1. **Exploration & Data Cleaning**
   - Audit initial de la structure du fichier de ventes brut.
   - Contrôle de la qualité des données et gestion des valeurs aberrantes.
   
2. **Feature Engineering & Manipulation Temporelle**
   - Normalisation et mise en conformité des formats de dates (`pd.to_datetime`).
   - Extraction de colonnes dérivées (`Annee`, `Mois`, `AnneeMois` via l'accesseur `.dt` de Pandas) pour s'affranchir du bruit de la granularité journalière.

3. **Calcul d'Indicateurs Clés (KPIs) & Agrégations**
   - Calcul du Chiffre d'Affaires global et regroupements métiers (`groupby`, `reset_index`).

4. **Visualisations Professionnelles (Dataviz)**
   - **Évolution temporelle :** Courbe de tendance du CA mois par mois (`sns.lineplot`).
   - **Répartition structurelle :** Cartographie du CA par statut de commande via une Treemap (`squarify`).

---

## 💻 Code Source (Partie 1 : Analyse Globale & Temporelle)

Voici le script Python complet utilisé pour nettoyer les données, créer les indicateurs temporels et générer les deux visualisations de cette première brique :

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import squarify

# --- 1. CHARGEMENT ET PRÉREQUIS TEMPORELS ---
# Chargement du dataset (adapter le chemin selon ton environnement)
# Sales = pd.read_csv('sales_2 (1).csv')

Sales['OrderDate'] = pd.to_datetime(Sales['OrderDate'])

# Enrichissement du dataset (Feature Engineering)
Sales['Annee'] = Sales['OrderDate'].dt.year
Sales['Mois'] = Sales['OrderDate'].dt.month
Sales['AnneeMois'] = Sales['OrderDate'].dt.to_period('M')


# --- 2. GRAPHIQUE 1 : Évolution du CA Mois par Mois (Line Plot) ---
ca_total_mois = Sales.groupby("AnneeMois")["TotalPrice"].sum().reset_index()
ca_total_mois['AnneeMois'] = ca_total_mois['AnneeMois'].astype(str)

plt.figure(figsize=(12, 6))
sns.lineplot(
    data=ca_total_mois, 
    x="AnneeMois", 
    y="TotalPrice", 
    color="#1f77b4", 
    linewidth=2, 
    marker='o'
)
plt.title("Évolution globale du Chiffre d'Affaires Mois par Mois", fontsize=14, fontweight='bold', pad=15)
plt.xlabel("Mois (Année-Mois)", fontsize=11)
plt.ylabel("Chiffre d'Affaires (€)", fontsize=11)
plt.xticks(rotation=45)
plt.grid(True, linestyle='--', alpha=0.6)
plt.show()


# --- 3. GRAPHIQUE 2 : Répartition du CA par Statut de Commande (Treemap) ---
ca_order_status = Sales.groupby("OrderStatus")["TotalPrice"].sum().reset_index()

plt.figure(figsize=(12, 8))
squarify.plot(
    sizes=ca_order_status["TotalPrice"], 
    label=ca_order_status["OrderStatus"], 
    alpha=0.85,
    text_kwargs={'fontsize': 11, 'fontweight': 'bold'}
)
plt.title("Répartition du Chiffre d'Affaires par Statut de Commande", fontsize=14, fontweight='bold', pad=15)
plt.axis('off')
plt.show()
