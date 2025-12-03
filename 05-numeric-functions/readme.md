# Module 05 : Fonctions Numériques

## 📚 Introduction

Les fonctions numériques permettent d'effectuer des calculs mathématiques et des transformations sur des nombres dans vos requêtes SQL.

---

## 1️⃣ CEIL, FLOOR et ROUND - Arrondissement

### CEIL - Arrondir au Supérieur

```sql
/* ========================================
   CEIL - Arrondir au nombre entier SUPÉRIEUR
   Syntaxe : CEIL(nombre) ou CEILING(nombre)
   ======================================== */

-- Exemples de base
SELECT CEIL(4.2);   -- Résultat : 5
SELECT CEIL(4.8);   -- Résultat : 5
SELECT CEIL(-4.2);  -- Résultat : -4

-- Avec une table
SELECT number AS MainNumber, CEIL(number) AS newNumber FROM `try`;

-- Cas d'usage : Calculer le nombre de pages nécessaires
SELECT 
    total_items,
    items_per_page,
    CEIL(total_items / items_per_page) AS total_pages
FROM pagination;
-- 45 items, 10 par page → 5 pages
```

### FLOOR - Arrondir à l'Inférieur

```sql
/* ========================================
   FLOOR - Arrondir au nombre entier INFÉRIEUR
   Syntaxe : FLOOR(nombre)
   ======================================== */

-- Exemples de base
SELECT FLOOR(4.2);   -- Résultat : 4
SELECT FLOOR(4.8);   -- Résultat : 4
SELECT FLOOR(-4.2);  -- Résultat : -5

-- Avec une table
SELECT number AS MainNumber, FLOOR(number) AS newNumber FROM `try`;

-- Cas d'usage : Calculer l'âge à partir de la date de naissance
SELECT 
    name,
    birth_date,
    FLOOR(DATEDIFF(CURDATE(), birth_date) / 365.25) AS age
FROM users;
```

### ROUND - Arrondir au Plus Proche

```sql
/* ========================================
   ROUND - Arrondir au nombre le plus proche
   Syntaxe : ROUND(nombre, [décimales])
   ======================================== */

-- Arrondir à l'entier le plus proche
SELECT ROUND(4.2);   -- Résultat : 4
SELECT ROUND(4.5);   -- Résultat : 5 (arrondi supérieur)
SELECT ROUND(4.8);   -- Résultat : 5

-- Arrondir avec décimales
SELECT ROUND(123.456, 0);   -- Résultat : 123
SELECT ROUND(123.456, 1);   -- Résultat : 123.5
SELECT ROUND(123.456, 2);   -- Résultat : 123.46

-- Avec une table
SELECT number AS MainNumber, ROUND(number) AS newNumber FROM `try`;
SELECT number AS MainNumber, ROUND(number, 2) AS newNumber FROM `try`;

-- Cas d'usage : Arrondir les prix
SELECT 
    product_name,
    price,
    ROUND(price * 1.2, 2) AS price_with_tax
FROM products;
-- 19.99 → 23.99 (avec 20% de taxe)
```

### 📊 Comparaison CEIL vs FLOOR vs ROUND

```sql
SELECT 
    4.3 AS original,
    CEIL(4.3) AS ceil_result,    -- 5
    FLOOR(4.3) AS floor_result,  -- 4
    ROUND(4.3) AS round_result;  -- 4
```

---

## 2️⃣ TRUNCATE, MOD et POW

### TRUNCATE - Tronquer les Décimales

```sql
/* ========================================
   TRUNCATE - Tronquer (couper) les décimales
   Syntaxe : TRUNCATE(nombre, décimales)
   Différence avec ROUND : Ne fait PAS d'arrondi, coupe simplement
   ======================================== */

-- Tronquer à 2 décimales
SELECT TRUNCATE(123.456789, 2);  -- Résultat : 123.45 (pas d'arrondi)
SELECT ROUND(123.456789, 2);     -- Résultat : 123.46 (avec arrondi)

-- Comparaison TRUNCATE vs ROUND
SELECT 
    number AS MainNumber,
    ROUND(number, 2) AS rounded,
    TRUNCATE(number, 2) AS truncated
FROM `try`;

-- Insérer une valeur tronquée
INSERT INTO `test`.`try` (`id`, `number`)
VALUES (NULL, TRUNCATE('123456.65656565', 2));
-- Résultat : 123456.65

-- Cas d'usage : Afficher un prix sans arrondir
SELECT 
    product_name,
    TRUNCATE(price, 2) AS display_price
FROM products;
```

### POW / POWER - Puissance

```sql
/* ========================================
   POW - Élever à la puissance
   Syntaxe : POW(nombre, puissance) ou POWER(nombre, puissance)
   ======================================== */

-- Exemples de base
SELECT POW(2, 3);    -- Résultat : 8 (2³ = 2×2×2)
SELECT POW(5, 2);    -- Résultat : 25 (5² = 5×5)
SELECT POW(10, 3);   -- Résultat : 1000 (10³)

-- POWER est identique à POW
SELECT POWER(2, 3);  -- Résultat : 8

-- Avec une table
SELECT number, POWER(number, 2) AS squared FROM `try`;

-- Cas d'usage : Calculer l'aire d'un carré
SELECT 
    side_length,
    POW(side_length, 2) AS area
FROM squares;

-- Calculer le volume d'un cube
SELECT 
    side_length,
    POW(side_length, 3) AS volume
FROM cubes;
```

### MOD - Modulo (Reste de Division)

```sql
/* ========================================
   MOD - Reste de la division
   Syntaxe : MOD(dividende, diviseur) ou dividende % diviseur
   ======================================== */

-- Exemples de base
SELECT MOD(7, 2);    -- Résultat : 1 (7 ÷ 2 = 3 reste 1)
SELECT MOD(10, 3);   -- Résultat : 1 (10 ÷ 3 = 3 reste 1)
SELECT MOD(15, 4);   -- Résultat : 3 (15 ÷ 4 = 3 reste 3)

-- Syntaxe alternative avec %
SELECT 7 % 2;        -- Résultat : 1
SELECT 10 % 3;       -- Résultat : 1

-- Cas d'usage 1 : Trouver les nombres pairs
SELECT number FROM numbers WHERE MOD(number, 2) = 0;
-- Nombres pairs : reste de division par 2 = 0

-- Cas d'usage 2 : Trouver les nombres impairs
SELECT number FROM numbers WHERE MOD(number, 2) = 1;

-- Cas d'usage 3 : Alterner les couleurs de lignes
SELECT 
    id,
    name,
    CASE 
        WHEN MOD(id, 2) = 0 THEN 'even-row'
        ELSE 'odd-row'
    END AS row_class
FROM products;
```

---

## 3️⃣ Autres Fonctions Numériques Utiles

### ABS - Valeur Absolue

```sql
/* ========================================
   ABS - Valeur absolue (sans signe)
   Syntaxe : ABS(nombre)
   ======================================== */

SELECT ABS(-15);     -- Résultat : 15
SELECT ABS(15);      -- Résultat : 15
SELECT ABS(-3.14);   -- Résultat : 3.14

-- Cas d'usage : Calculer la différence absolue
SELECT 
    price1,
    price2,
    ABS(price1 - price2) AS price_difference
FROM price_comparisons;
```

### SQRT - Racine Carrée

```sql
/* ========================================
   SQRT - Racine carrée
   Syntaxe : SQRT(nombre)
   ======================================== */

SELECT SQRT(16);     -- Résultat : 4
SELECT SQRT(25);     -- Résultat : 5
SELECT SQRT(2);      -- Résultat : 1.4142135623731

-- Cas d'usage : Calculer la distance (théorème de Pythagore)
SELECT 
    SQRT(POW(x2 - x1, 2) + POW(y2 - y1, 2)) AS distance
FROM coordinates;
```

### SIGN - Signe du Nombre

```sql
/* ========================================
   SIGN - Retourne le signe du nombre
   Syntaxe : SIGN(nombre)
   Résultat : -1 (négatif), 0 (zéro), 1 (positif)
   ======================================== */

SELECT SIGN(-15);    -- Résultat : -1
SELECT SIGN(0);      -- Résultat : 0
SELECT SIGN(15);     -- Résultat : 1

-- Cas d'usage : Catégoriser les transactions
SELECT 
    amount,
    CASE SIGN(amount)
        WHEN -1 THEN 'Débit'
        WHEN 0 THEN 'Neutre'
        WHEN 1 THEN 'Crédit'
    END AS transaction_type
FROM transactions;
```

---

## 4️⃣ Fonctions d'Agrégation Numériques

### MIN et MAX

```sql
/* ========================================
   MIN - Valeur minimale
   MAX - Valeur maximale
   ======================================== */

-- Trouver le prix minimum et maximum
SELECT 
    MIN(price) AS lowest_price,
    MAX(price) AS highest_price
FROM products;

-- Par catégorie
SELECT 
    category,
    MIN(price) AS min_price,
    MAX(price) AS max_price
FROM products
GROUP BY category;
```

### SUM - Somme

```sql
/* ========================================
   SUM - Somme totale
   ======================================== */

-- Calculer le total des ventes
SELECT SUM(total) AS total_sales FROM orders;

-- Par client
SELECT 
    customer_id,
    SUM(total) AS total_spent
FROM orders
GROUP BY customer_id;
```

### AVG - Moyenne

```sql
/* ========================================
   AVG - Moyenne
   ======================================== */

-- Calculer le prix moyen
SELECT AVG(price) AS average_price FROM products;

-- Avec arrondi
SELECT ROUND(AVG(price), 2) AS average_price FROM products;
```

### COUNT - Comptage

```sql
/* ========================================
   COUNT - Compter les lignes
   ======================================== */

-- Compter tous les produits
SELECT COUNT(*) AS total_products FROM products;

-- Compter les produits en stock
SELECT COUNT(*) AS in_stock FROM products WHERE stock > 0;
```

---

## 📊 Tableau Récapitulatif

| Fonction | Description | Exemple |
|----------|-------------|---------|
| **CEIL** | Arrondir au supérieur | `CEIL(4.2)` → 5 |
| **FLOOR** | Arrondir à l'inférieur | `FLOOR(4.8)` → 4 |
| **ROUND** | Arrondir au plus proche | `ROUND(4.5)` → 5 |
| **TRUNCATE** | Tronquer (sans arrondir) | `TRUNCATE(4.8, 0)` → 4 |
| **POW** | Puissance | `POW(2, 3)` → 8 |
| **MOD** | Modulo (reste) | `MOD(7, 2)` → 1 |
| **ABS** | Valeur absolue | `ABS(-5)` → 5 |
| **SQRT** | Racine carrée | `SQRT(16)` → 4 |
| **MIN** | Minimum | `MIN(price)` |
| **MAX** | Maximum | `MAX(price)` |
| **SUM** | Somme | `SUM(total)` |
| **AVG** | Moyenne | `AVG(price)` |

---

## 🎯 Exemples Pratiques

### Exemple 1 : Calculer des Statistiques de Prix

```sql
SELECT 
    COUNT(*) AS total_products,
    MIN(price) AS min_price,
    MAX(price) AS max_price,
    ROUND(AVG(price), 2) AS avg_price,
    SUM(price * stock) AS total_inventory_value
FROM products;
```

### Exemple 2 : Pagination

```sql
-- Calculer le nombre total de pages
SELECT 
    COUNT(*) AS total_items,
    CEIL(COUNT(*) / 20) AS total_pages
FROM products;
```

---

## 🧪 Exercices

```sql
-- Exercice 1 : Calculer le prix TTC (avec 20% de TVA) arrondi à 2 décimales
SELECT 
    product_name,
    price AS price_ht,
    ROUND(price * 1.2, 2) AS price_ttc
FROM products;

-- Exercice 2 : Trouver les produits dont le prix est un multiple de 5
SELECT * FROM products WHERE MOD(price, 5) = 0;
```

---

## ➡️ Prochaine Étape

Passez au **[Module 06 : Fonctions de Date](../06-date-functions)** pour manipuler les dates et heures.