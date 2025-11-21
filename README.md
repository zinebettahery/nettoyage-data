
# Projet :  Nettoyage, exploration et visualisation de données de ventes retail




##  Présentation de l'équipe
- Équipe : Swiching Crawlers  
- Membres : Chaimaa MAACH, Zineb ET-TAHIRI ,Fatima Ezzahrae BABIOUI ,Hassan ISSIL  
- Organisation : collaboration collective ; chaque membre participe à toutes les étapes et décisions.

---

##  Contenu
Le projet fournit :  
1. **Fichier Power BI complet** Nettoyage, exploration et visualisation de données de ventes retail.pbix  qui contient :
   - Deux pages de rapport : Direction / Marketing  
   - Visualisations interactives et KPI cohérents avec les points de vue métiers

2. **Documentation complète** : Readme.md qui contient :
   - Journal de bord : Visualiser notre suivi quotidien du projet 
   - Tableau des transformations : liste des modifications apportées au dataset (data cleaning, colonnes créées, valeurs corrigées)  avec des démonstations par des captures d'écran
   - Liste des mesures DAX avec explications et justification métier

---

1️⃣ Chargement des données
---
<img width="685" height="577" alt="chargement" src="https://github.com/user-attachments/assets/7715befe-a254-45da-b5dd-60d275d661da" />

**1.1. Importation du fichier**

On a importé le fichier  **retailstoresales.csv** dans Power BI via Obtenir des données → Text/CSV_.  
<img width="301" height="466" alt="importation" src="https://github.com/user-attachments/assets/b1ab0c9d-6835-45a5-8c11-81d372798798" />

Power BI a automatiquement détecté la structure du fichier, mais une vérification manuelle a été nécessaire pour confirmer la bonne lecture des données.

**1.2. Vérification de l’encodage et du format**

-   Résoudre les problèmes d'encodage :"en-US" a été ajouté lors du transformation des colonnes "texte->nombre décimal" pour detecter la virgule des nombres décimaux
  
-   Séparateur : virgule
    
**1.3. Structure initiale**

Après importation, les caractéristiques du fichier sont :

-   **12 575 lignes**
    
-   **11 colonnes**
    
-   **Types de données initiaux : plusieurs incorrects**
    
    -   Colonnes numériques importées comme texte (ex : Price Unit, Quantity, Total Spent)
<img width="1512" height="333" alt="impor 1" src="https://github.com/user-attachments/assets/bd369639-d34e-4941-b594-023f9cbae800" />




**1.4. Anomalies visibles au premier chargement**

-   Types de données non conformes
    
-   Valeurs nulles dans plusieurs colonnes
    
-   Incohérence : Discount Applied
    

Cette étape a permis d’identifier les premiers problèmes nécessitant un nettoyage approfondi.

---

2️⃣ Analyse exploratoire (EDA)
---
<img width="676" height="596" alt="analyse" src="https://github.com/user-attachments/assets/cb225b32-6e7a-4c78-863e-7addbe1e9bd7" />

 **2.1. Objectif**

L’EDA a pour but :

-  de comprendre la structure des données brutes

-  d’identifier les anomalies

-  d’observer les relations logiques entre les colonnes

-  et de formuler des premières hypothèses métier

Cette analyse a été réalisée via Power BI (visualisations simples) et inspection dans Power Query.

**2.2. Vérification des valeurs manquantes et incohérences**

Colonnes fortement liées entre elles

L’exploration a montré des relations logiques :

-  Total Spent = Price Unit × Quantity
→ Cette relation permet d’identifier les valeurs incohérentes ou manquantes.
→ Si un des champs est vide mais les deux autres présents, on peut recalculer la valeur manquante.

-  Category → Item → Price Unit
Chaque category contient un ensemble d’items spécifiques.
Chaque item possède un Price Unit constant dans la majorité des cas.
Cela a permis de vérifier ou reconstruire certaines valeurs manquantes ou erronées (ex : Price Unit manquant mais connu via l’item).

Ces relations ont été utiles pour déterminer comment remplir les valeurs manquantes de manière cohérente.

-  Colonnes sans relation exploitable
La colonne Discount Applied contient des valeurs True / False, mais présente aussi des valeurs nulles.
Aucune autre colonne ne permet de déduire si un discount a réellement été appliqué (montant réduit, code promo, etc.).
Donc on ne peut pas imputer les valeurs manquantes, c'est pour cela les valeurs nulles doivent être conservées ou remplacées par une catégorie “Unknown” selon le besoin analytique.

**2.3. Conclusion de l’EDA**

L’analyse exploratoire a permis :
-  de comprendre les relations entre les colonnes
-  d’identifier quelles valeurs nulles peuvent être corrigées et lesquelles doivent rester non imputées
-  de préparer l’étape suivante : Nettoyage et transformation du dataset

---

## 3️⃣  Nettoyage et transformation (Power Query)
---
<img width="687" height="698" alt="nettoyage" src="https://github.com/user-attachments/assets/025b813e-cd3b-44f8-b3b1-09feeaf8dbcc" />

Après l’analyse exploratoire, plusieurs opérations de transformation, correction et enrichissement ont été appliquées au dataset pour garantir la qualité des données et permettre des analyses fiables.
Le tableau suivant résume l’ensemble des actions réalisées.

- A. Transformations des types de données :
  
| Étape |	Colonne | Action réalisée |Justification|
|-------|--------|--------|--------|
|Transformation |	Item,Price Per Unit, Quantity	| Correction des types de données : conversion de texte → valeur décimale. |Cela permet d’effectuer correctement les calculs et d’éviter les erreurs dans les mesures Power BI.|
|Transformation	|Discount Applied	|Conversion du type boolean → texte |Cette transformation facilite le traitement des valeurs True/False/Null et permet une gestion plus flexible des données manquantes.|

- B. Nettoyage des données et correction des valeurs manquantes :
  
| Étape | Colonne |Action réalisée | 
|-------|--------|--------|
|Nettoyage | Price Per Unit |Lorsque la valeur était manquante : recalcul du prix unitaire = Total Spent / Quantity.| 
|Nettoyage| Item | Remplissage des valeurs manquantes en s’appuyant sur le Price Per Unit, qui est constant pour un même item.|
|Nettoyage| Quantity | Remplacement des valeurs manquantes par la moyenne de la quantité pour le même item.|
|Nettoyage | Total Spent | Recalcul automatique = Price Per Unit × Quantity si la valeur était absente ou incohérente.|
|Nettoyage | Discount Applied|Remplacement des valeurs manquantes par la catégorie “Unknown”, car aucune relation ne permettait de déduire True/False.| 

- C. Création de nouvelles variables (Feature Engineering):

| Étape | Colonne |Action réalisée | 
|-------|--------|--------| 
|Feature |Month | Extraction du mois à partir de Transaction Date.| 
|Feature | Day| Extraction du jour de la semaine.|
|Feature | Year |Extraction de l’année pour faciliter les analyses temporelles. | 


**3.1. Démonstration par captures d'écran :**

- Lorsque le prix unitaire était manquant, il a été recalculé automatiquement comme Total Spent divisé par Quantity.
Cette méthode garantit la cohérence mathématique entre les trois colonnes.
<img width="862" height="545" alt="3" src="https://github.com/user-attachments/assets/8ece4c44-7bc6-4cfe-b3e5-b1e4c226722c" />
<img width="207" height="606" alt="4" src="https://github.com/user-attachments/assets/f08a566d-81f3-4b68-8a78-c138ac6038b8" />


- Remplissage des valeurs manquantes dans Item : Les valeurs manquantes de la colonne Item ont été complétées en utilisant Price Per Unit, car un même item possède généralement un prix unitaire constant.
<img width="502" height="492" alt="5" src="https://github.com/user-attachments/assets/75b18442-1136-433f-8dd0-8e4caa357e5c" />
<img width="1067" height="762" alt="6" src="https://github.com/user-attachments/assets/2a03c13d-665f-491a-9821-9083dd6159b7" />
<img width="855" height="775" alt="7" src="https://github.com/user-attachments/assets/740d44c2-9d63-4a49-a983-c87dc1e52e5e" />
<img width="213" height="733" alt="8" src="https://github.com/user-attachments/assets/13e92665-5c16-455d-a836-7db4c10f3396" />


- Traitement des valeurs manquantes dans Discount Applied : Les valeurs manquantes dans Discount Applied ont été remplacées par la catégorie “Unknown”, car aucune autre colonne ne permettait de déterminer si une remise avait été appliquée.
<img width="857" height="372" alt="9" src="https://github.com/user-attachments/assets/e20a9926-9ef4-471f-89c1-fa5fcdb44d69" />
<img width="202" height="710" alt="10" src="https://github.com/user-attachments/assets/c8fbd248-26a1-4ed9-b474-ad21a3ee5d5a" />


- Remplissage des valeurs manquantes dans Quantity : Les quantités manquantes ont été remplacées par la moyenne observée pour le même item.
<img width="1123" height="722" alt="11" src="https://github.com/user-attachments/assets/8fff8aa0-729c-42bf-ad2c-eb04cc8a92c1" />
<img width="857" height="547" alt="12" src="https://github.com/user-attachments/assets/8fb03002-b8d1-4760-8d17-4b3cdce9b4d0" />
<img width="661" height="702" alt="13" src="https://github.com/user-attachments/assets/3d2059c1-9d7d-44d4-bce1-0c84a27ae1de" />
<img width="233" height="707" alt="14" src="https://github.com/user-attachments/assets/13639456-84dc-44c9-87f3-6031caaf1c9e" />
<img width="201" height="701" alt="15" src="https://github.com/user-attachments/assets/b5571dab-65c8-432d-929b-99fb8ec0cd43" />


- Recalcul de Total Spent : Lorsque le montant total était absent, il a été recalculé automatiquement à partir de Price Per Unit × Quantity.
<img width="871" height="555" alt="16" src="https://github.com/user-attachments/assets/4b43bde3-61de-4aed-9ffe-ea3d3f51f649" />
<img width="405" height="717" alt="17" src="https://github.com/user-attachments/assets/0dcc3126-5d74-4224-b0fd-d1ca3bf8b3a0" />


---

4️⃣ Modélisation du Modèle de Données
---
<img width="682" height="582" alt="modelisation" src="https://github.com/user-attachments/assets/da06d0fb-57cb-4a34-b519-a20d2a0f6b91" />

**4.1. Objectif de la modélisation**

Après le nettoyage et la préparation du dataset, on a crée les tables de dimentions nécessaires et on a obtenu un modèle de données structuré en schéma en étoile dans Power BI.
L'objectif était de séparer les informations descriptives (dimensions) des données transactionnelles (table de faits), afin de :
- faciliter l’analyse
- améliorer les performances
- assurer la cohérence des relations
- simplifier la création des mesures DAX

  
**4.2. Tables de dimension créées**

📌 1. Dim_Customer : Cette table regroupe toutes les informations liées aux clients.

| Table_Name | Colonnes |Justification | 
|-------|--------|--------| 
|Dim_Customer |Customer_ID | Identifiant unique du client| 
|Dim_Customer |Panier Moyen Client | mesure représentant la moyenne des montants dépensés| 
|Dim_Customer |First Purchase Date | première date d’achat détectée dans l’historique| 
|Dim_Customer |Type Client_Promo| catégorisation selon la première transaction (pendant une promotion ou hors promotion)| 

→ Rôle global :
Permet d’analyser le comportement client, la fidélité, le cycle de vie et l’impact des promotions.

📌 2. Dim_Product : Regroupe les caractéristiques des produits vendus.

| Table_Name | Colonnes |Justification | 
|-------|--------|--------| 
|Dim_Product |Category | Catégorie à laquelle appartient le produit| 
|Dim_Product|Item| Nom du produit acheté| 
|Dim_Product|Price Per Unit | Prix unitaire du produit| 

→ Rôle global :
Décrire les caractéristiques du produit afin de faciliter l’analyse des ventes par article et catégorie.

📌 3. Dim_Payment : Contient les informations relatives aux méthodes de paiement.

| Table_Name | Colonnes |Justification | 
|-------|--------|--------| 
|Dim_Payment |Payment Method|Mode de paiement utilisé par le client (ex : Cash, Credit Card, Online Payment).| 

→ Rôle global :
Permet d’analyser les préférences de paiement et de suivre l’évolution des comportements d’achat.

📌 4. Dim_Location : Table décrivant les lieux d’achat.

| Table_Name | Colonnes |Justification | 
|-------|--------|--------| 
|Dim_Location|Location|Canal ou lieu d’achat : Store (magasin physique) ou Online (achat en ligne).|

→ Rôle global :
Analyse des ventes par canal de distribution et comparaison Store vs Online.

**4.3. Table de fait : Fact_Sales**

La table Fact_Sales est la table principale du modèle : Elle contient l’ensemble des transactions du dataset nettoyé.

- Colonnes clés : Customer ID ; Item ; Payment Method ; Location ; Transaction Date ; Quantity ; Price Per Unit ; Total Spent ; Discount Applied

Rôle global :
→ Regroupe toutes les mesures quantitatives et les événements transactionnels ; constitue la base des calculs analytiques (ventes, quantités, promo, etc.).

---

## 5️⃣Création de mesures et colonnes calculées (DAX)
---
<img width="692" height="613" alt="image" src="https://github.com/user-attachments/assets/a2f4c9c6-c418-4e5b-9210-6dd0a7c0a431" />

Dans cette étape, plusieurs mesures DAX ont été créées afin de permettre l’analyse des performances commerciales, du comportement client, et de l’impact des promotions.
Ces KPIs constituent le cœur des visualisations Power BI.

**5.1. Création de colonnes :**

📌 1. FirstPurchaseDate(Date de la première transaction effectuée par un client) :
- Identifier depuis quand un client est actif.
- Permettre de créer des segments pour analyser l’évolution du comportement dans le temps (avant/pendant/après promo).

<img width="522" height="187" alt="firstpurchasedate" src="https://github.com/user-attachments/assets/5cb5df6f-68bf-4cd4-bbfe-3b07232a021a" />


📌2. Panier Moyen Client(Montant moyen dépensé par un client par transaction) :
- Identifier les clients qui dépensent le plus.

<img width="367" height="118" alt="paniermoyenparclient" src="https://github.com/user-attachments/assets/9ea3c32b-5328-40cd-ae74-734711bd7fcb" />


📌3. TypeClient_Promo(Indique si un client a réalisé sa première transaction pendant une période de promotion ou hors promotion) :
- Permettre d’analyser l’effet d’acquisition des promotions.
- Répondre à des questions clés :
Les promos attirent-elles de nouveaux clients ?
Les clients acquis en promo reviennent-ils après ?
Le taux de fidélisation est-il plus fort chez les clients hors promo ?

<img width="885" height="527" alt="typeClient_Promo" src="https://github.com/user-attachments/assets/bf1a81c5-5a65-4999-9b58-ef49d6f9b81e" />


**5.2. Création de mesures :**

📌 1. CA Total (Chiffre d’Affaires Total):
- Mesure le total des ventes réalisées sur la période.
- C’est l’indicateur principal pour suivre la performance du commerce.
  
<img width="341" height="32" alt="ca" src="https://github.com/user-attachments/assets/a8be3243-7cb2-45dc-9ed9-8f8e6a5f486d" />

📌 2. Nombre de Clients:
- Indique combien de clients uniques ont effectué des achats.
- Permet d’évaluer la base active de clients et la fidélisation.

<img width="407" height="30" alt="nbr clt" src="https://github.com/user-attachments/assets/a176ef6b-4b0a-4ed1-b068-bb4b4fdea14e" />

📌 3. Nombre de Commandes:
- Mesure l’activité commerciale.
- Un client peut acheter plusieurs fois → utile pour analyser la fréquence d’achat.

<img width="445" height="25" alt="nbr com" src="https://github.com/user-attachments/assets/4d80df25-49ee-46f6-9b07-e3a9a29635a3" />

📌 4. Panier Moyen:
- Montre combien dépense un client en moyenne par transaction.
- C’est un KPI central pour comprendre le comportement d’achat.

<img width="501" height="28" alt="panier moy" src="https://github.com/user-attachments/assets/cfb782ac-1fac-483e-b0f1-a983180984f2" />

📌 5. Rachats Après Première (nombre de clients qui reviennent):
Mesure la fidélisation : combien de clients effectuent plus d’un achat après leur première commande.

<img width="662" height="312" alt="rachat apres premiere trans" src="https://github.com/user-attachments/assets/a693cfa9-89ce-42cf-8fba-0beef669284b" />

📌 6. Taux moyen client:
Indique la fréquence d’achat moyenne par client : Plus la valeur est élevée, plus les clients reviennent souvent.

<img width="440" height="30" alt="tauxMoyen" src="https://github.com/user-attachments/assets/ab2361b2-cc4a-404a-bc56-38fab22c43ab" />

📌 7. Taux d’achat pendant promotion:
Indique la proportion des ventes réalisées pendant les promotions : Permet d’évaluer l’impact des promotions sur le chiffre d’affaires.

<img width="516" height="298" alt="tauxAchatPendantPromo" src="https://github.com/user-attachments/assets/d5a3bbea-386d-4f40-a041-8ff3bd2f0abd" />

📌 8. Taux d’achat hors promotion:
Mesure la part des commandes faites sans promotion : Montre la capacité du business à vendre même hors période promo.

<img width="522" height="302" alt="tauxAchatHorsPromo" src="https://github.com/user-attachments/assets/2d6b19cf-1246-4a3b-9918-9bbd04bfa057" />

---

## 6️⃣ Création du Tableau de Bord – Documentation (2 pages Power BI)
---
<img width="668" height="655" alt="tableauxdebord" src="https://github.com/user-attachments/assets/be92ae09-e458-4659-916d-3b72592548cf" />

### PAGE 1 — Vue Direction des Ventes : 
Cette page répond au besoin :
« En tant que directeur des ventes, je veux visualiser l’évolution du chiffre d’affaires par catégorie de produit et par canal afin d'identifier les catégories en croissance ou en décroissance. »

KPIs : Chiffre d’Affaires Total (CA Total), Nombre de Commandes, Panier Moyen,Chiffre d'affaire par canal et par catégorie
<img width="1162" height="727" alt="vision1" src="https://github.com/user-attachments/assets/39e6b126-a159-42e7-981a-f0610ebf7f21" />

### PAGE 2 — Vue Marketing & Fidélisation :
Cette page répond au besoin :
« En tant que directeur marketing, je veux visualiser le profil des clients, leur comportement d’achat et l’impact des promotions pour définir des actions de fidélisation. »

KPIs : Nombre de Clients, % de clients Promo vs Hors Promo, Rachats après première commande, Taux d’achat pendant promo,Taux d’achat hors promo,Taux d'achat moyen
<img width="1166" height="656" alt="vision2" src="https://github.com/user-attachments/assets/68bdfb66-cba0-4229-9874-7cbe57b7715a" />







- Lien GitHub (simulation) : [https://github.com/team2-retail](https://github.com/team2-retail)  
- Lien Trello (simulation) : [https://trello.com/b/team2-retail](https://trello.com/b/V3JBiryI/my-trello-board)

---

## 6️⃣ Outils Utilisés
- **Power BI Desktop** : EDA, nettoyage et exploration des données,modélisation, visualisations, mesures DAX  
- **Trello** : gestion collaborative des tâches  
- **GitHub** : versioning et partage du projet
