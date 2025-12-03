# Module 11 : Opérateurs Arithmétiques

## 📚 Introduction

Les opérateurs arithmétiques permettent d'effectuer des calculs mathématiques directement dans vos requêtes SQL.

---

## Opérateurs Arithmétiques

```sql
/* ========================================
   OPÉRATEURS ARITHMÉTIQUES
   + Addition
   - Soustraction
   * Multiplication
   / Division (avec décimales)
   DIV Division entière (sans décimales)
   % ou MOD Modulo (reste de division)
   ======================================== */
```

---

## 1️⃣ Opérations de Base

### Addition et Multiplication

```sql
-- Calculs simples
SELECT (2 * 2) + 100;  -- Résultat : 104
-- Priorité : multiplication d'abord, puis addition

-- Exemple pratique : Calculer le salaire
SELECT 
    name, 
    days AS DaysOfWork, 
    dayrate AS DayRate 
FROM `try`;

-- Calculer le salaire total
SELECT 
    name, 
    days AS DaysOfWork, 
    dayrate AS DayRate, 
    days * dayrate AS NeededMoney 
FROM `try`;

-- Avec bonus
SELECT 
    name, 
    days AS DaysOfWork, 
    dayrate AS DayRate, 
    (days * dayrate) + 100 AS NeededMoney 
FROM `try`;
```

### Division

```sql
-- Division normale (avec décimales)
SELECT 20 / 2;  -- Résultat : 10.0000

-- Division entière (sans décimales)
SELECT 20 DIV 2;  -- Résultat : 10

-- Différence entre / et DIV
SELECT 21 / 2;    -- Résultat : 10.5000
SELECT 21 DIV 2;  -- Résultat : 10
```

### Modulo (Reste)

```sql
-- Reste de la division
SELECT 21 % 2;  -- Résultat : 1 (21 ÷ 2 = 10 reste 1)

-- Cas d'usage : Trouver les nombres pairs
SELECT * FROM numbers WHERE number % 2 = 0;

-- Cas d'usage : Trouver les nombres impairs
SELECT * FROM numbers WHERE number % 2 = 1;
```

---

## 2️⃣ Exemples Pratiques Complets

### Exemple 1 : Calcul de Salaire

```sql
-- Salaire de base
SELECT
    name,
    days AS DaysOfWork,
    dayrate AS DayRate,
    (days * dayrate) AS NeededMoney
FROM `try`;

-- Avec bonus
SELECT
    name,
    days AS DaysOfWork,
    dayrate AS DayRate,
    (days * dayrate) AS NeededMoney,
    (days * dayrate) + 100 AS NeededMoneyWithBonus
FROM `try`;

-- Avec déductions
SELECT
    name,
    days AS DaysOfWork,
    dayrate AS DayRate,
    (days * dayrate) AS NeededMoney,
    (days * dayrate) + 100 AS NeededMoneyWithBonus,
    (days * dayrate) + 100 - 50 AS ResultMoneyWithOfficeBoyMoney
FROM `try`;
```

### Exemple 2 : Calculs de Prix

```sql
-- Prix avec TVA (20%)
SELECT 
    product_name,
    price AS price_ht,
    price * 1.20 AS price_ttc,
    price * 0.20 AS tva_amount
FROM products;

-- Prix avec remise
SELECT 
    product_name,
    price AS original_price,
    price * 0.90 AS discounted_price,  -- 10% de remise
    price - (price * 0.90) AS savings
FROM products;

-- Marge bénéficiaire
SELECT 
    product_name,
    cost_price,
    selling_price,
    selling_price - cost_price AS profit,
    ((selling_price - cost_price) / cost_price) * 100 AS profit_margin_percent
FROM products;
```

### Exemple 3 : Calculs de Commandes

```sql
-- Total de commande
SELECT 
    order_id,
    quantity,
    unit_price,
    quantity * unit_price AS subtotal,
    (quantity * unit_price) * 0.20 AS tax,
    (quantity * unit_price) * 1.20 AS total
FROM order_items;

-- Moyenne de panier
SELECT 
    customer_id,
    COUNT(*) AS total_orders,
    SUM(total) AS total_spent,
    SUM(total) / COUNT(*) AS average_order_value
FROM orders
GROUP BY customer_id;
```

### Exemple 4 : Calculs de Temps

```sql
-- Heures travaillées
SELECT 
    employee_name,
    hours_per_day,
    days_worked,
    hours_per_day * days_worked AS total_hours,
    (hours_per_day * days_worked) * hourly_rate AS total_pay
FROM timesheets;

-- Jours restants
SELECT 
    project_name,
    total_days,
    days_completed,
    total_days - days_completed AS days_remaining,
    (days_completed / total_days) * 100 AS completion_percent
FROM projects;
```

### Exemple 5 : Statistiques

```sql
-- Taux de conversion
SELECT 
    campaign_name,
    impressions,
    clicks,
    conversions,
    (clicks / impressions) * 100 AS click_rate,
    (conversions / clicks) * 100 AS conversion_rate
FROM marketing_campaigns;

-- Croissance
SELECT 
    month,
    revenue,
    LAG(revenue) OVER (ORDER BY month) AS previous_month_revenue,
    revenue - LAG(revenue) OVER (ORDER BY month) AS growth,
    ((revenue - LAG(revenue) OVER (ORDER BY month)) / LAG(revenue) OVER (ORDER BY month)) * 100 AS growth_percent
FROM monthly_revenue;
```

---

## 📊 Priorité des Opérateurs

| Priorité | Opérateur | Exemple |
|----------|-----------|---------|
| 1 (haute) | `()` Parenthèses | `(2 + 3) * 4 = 20` |
| 2 | `*`, `/`, `DIV`, `%`, `MOD` | `2 + 3 * 4 = 14` |
| 3 (basse) | `+`, `-` | `2 + 3 - 1 = 4` |

```sql
-- Sans parenthèses
SELECT 2 + 3 * 4;  -- Résultat : 14 (multiplication d'abord)

-- Avec parenthèses
SELECT (2 + 3) * 4;  -- Résultat : 20 (addition d'abord)

-- Exemple complexe
SELECT 10 + 5 * 2 - 3;  -- Résultat : 17
-- Calcul : 10 + (5 * 2) - 3 = 10 + 10 - 3 = 17

-- Avec parenthèses pour clarifier
SELECT (10 + 5) * (2 - 3);  -- Résultat : -15
```

---

## 💡 Bonnes Pratiques

```sql
-- ✅ BON : Utiliser des parenthèses pour clarifier
SELECT (price * quantity) + shipping AS total FROM orders;

-- ❌ MAUVAIS : Ambigu
SELECT price * quantity + shipping AS total FROM orders;

-- ✅ BON : Nommer les colonnes calculées
SELECT 
    price,
    quantity,
    price * quantity AS subtotal,
    (price * quantity) * 0.20 AS tax,
    (price * quantity) * 1.20 AS total
FROM order_items;

-- ✅ BON : Arrondir les résultats financiers
SELECT 
    product_name,
    price,
    ROUND(price * 1.20, 2) AS price_with_tax
FROM products;

-- ⚠️ ATTENTION : Division par zéro
SELECT 10 / 0;  -- Résultat : NULL (pas d'erreur en MySQL)

-- ✅ BON : Éviter la division par zéro
SELECT 
    CASE 
        WHEN quantity = 0 THEN 0
        ELSE total / quantity
    END AS average_price
FROM orders;
```

---

## 🧪 Exercices

```sql
-- Exercice 1 : Calculer le prix final avec remise et TVA
SELECT 
    product_name,
    price,
    price * 0.85 AS price_after_discount,  -- 15% de remise
    (price * 0.85) * 1.20 AS final_price   -- + 20% TVA
FROM products;

-- Exercice 2 : Calculer la commission des vendeurs
SELECT 
    salesperson,
    sales_amount,
    CASE
        WHEN sales_amount > 10000 THEN sales_amount * 0.15
        WHEN sales_amount > 5000 THEN sales_amount * 0.10
        ELSE sales_amount * 0.05
    END AS commission
FROM sales;

-- Exercice 3 : Calculer le ROI (Return on Investment)
SELECT 
    campaign_name,
    cost,
    revenue,
    revenue - cost AS profit,
    ((revenue - cost) / cost) * 100 AS roi_percent
FROM marketing_campaigns;
```

---

## ➡️ Prochaine Étape

Passez au **[Module 12 : Fonctions d'Information](../12-information-functions)** pour obtenir des informations système.