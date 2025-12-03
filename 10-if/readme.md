# Module 10 : Conditions IF et CASE

## 📚 Introduction

Les structures conditionnelles permettent d'appliquer une logique if-then-else dans vos requêtes SQL pour transformer et catégoriser les données dynamiquement.

---

## 1️⃣ IF - Condition Simple

```sql
/* ========================================
   IF - Condition simple
   Syntaxe : IF(condition, valeur_si_vrai, valeur_si_faux)
   ======================================== */

-- Exemple de base
SELECT id, name, IF(number < 24, 'Hard Luck', 'Congratz') AS result FROM `try`;
-- Si number < 24 → 'Hard Luck', sinon → 'Congratz'

-- Avec CONCAT
SELECT 
    id, 
    name, 
    IF(number < 24, CONCAT('Hard Luck ', number), CONCAT('Congratz ', number)) AS result 
FROM `try`;

-- Mettre à jour avec IF
UPDATE `try` SET `number` = IF(number < 1, number + 10, number);
-- Si number < 1, ajouter 10, sinon garder la valeur
```

### Exemples Pratiques IF

```sql
-- Statut de stock
SELECT 
    product_name,
    stock,
    IF(stock > 0, 'In Stock', 'Out of Stock') AS availability
FROM products;

-- Catégorie de prix
SELECT 
    product_name,
    price,
    IF(price < 50, 'Budget', 'Premium') AS price_category
FROM products;

-- Statut de commande
SELECT 
    order_id,
    total,
    IF(total > 100, 'Large Order', 'Small Order') AS order_size
FROM orders;

-- Vérification d'âge
SELECT 
    name,
    age,
    IF(age >= 18, 'Adult', 'Minor') AS age_group
FROM users;

-- Remise conditionnelle
SELECT 
    product_name,
    price,
    IF(price > 100, price * 0.9, price) AS final_price
FROM products;
-- Remise de 10% si prix > 100€
```

---

## 2️⃣ CASE - Conditions Multiples

### CASE avec Conditions

```sql
/* ========================================
   CASE - Conditions multiples
   Syntaxe :
   CASE
     WHEN condition1 THEN résultat1
     WHEN condition2 THEN résultat2
     WHEN condition3 THEN résultat3
     ELSE résultat_par_défaut
   END
   ======================================== */

-- Exemple de base
SELECT
    id, 
    name,
    CASE
        WHEN number = 10 THEN 'Not Bad'
        WHEN number = 15 THEN 'Good'
        WHEN number = 20 THEN 'Perfect'
        ELSE 'Unknown Rank'
    END AS result
FROM `try`;

-- Avec comparaisons
SELECT
    id, 
    name,
    CASE
        WHEN number > 10 THEN 'Good'
        WHEN number = 24 THEN 'Perfect'
        ELSE 'Unknown Rank'
    END AS result
FROM `try`;
```

### CASE avec Valeur

```sql
/* ========================================
   CASE - Comparaison directe avec une valeur
   Syntaxe :
   CASE valeur
     WHEN valeur1 THEN résultat1
     WHEN valeur2 THEN résultat2
     ELSE résultat_par_défaut
   END
   ======================================== */

-- Comparer directement la valeur
SELECT
    id, 
    name,
    CASE number
        WHEN 10 THEN 'Not Bad'
        WHEN 15 THEN 'Good'
        WHEN 20 THEN 'Perfect'
        ELSE 'Unknown Rank'
    END AS result
FROM `try`;
```

### UPDATE avec CASE

```sql
/* ========================================
   Utiliser CASE dans UPDATE
   ======================================== */

-- Mettre à jour selon des conditions
UPDATE `try` SET `number` =
CASE number
    WHEN 10 THEN number + 10
    WHEN 15 THEN number + 15
    WHEN 24 THEN number + 24
    ELSE number
END;
```

---

## 🎯 Exemples Pratiques CASE

### Exemple 1 : Catégorisation de Prix

```sql
SELECT 
    product_name,
    price,
    CASE
        WHEN price < 20 THEN 'Budget'
        WHEN price BETWEEN 20 AND 100 THEN 'Mid-Range'
        WHEN price > 100 THEN 'Premium'
        ELSE 'Unknown'
    END AS price_category
FROM products;
```

### Exemple 2 : Statut de Commande

```sql
SELECT 
    order_id,
    status,
    CASE status
        WHEN 'pending' THEN 'En attente'
        WHEN 'processing' THEN 'En traitement'
        WHEN 'shipped' THEN 'Expédiée'
        WHEN 'delivered' THEN 'Livrée'
        WHEN 'cancelled' THEN 'Annulée'
        ELSE 'Statut inconnu'
    END AS status_fr
FROM orders;
```

### Exemple 3 : Notes et Mentions

```sql
SELECT 
    student_name,
    score,
    CASE
        WHEN score >= 90 THEN 'A - Excellent'
        WHEN score >= 80 THEN 'B - Très Bien'
        WHEN score >= 70 THEN 'C - Bien'
        WHEN score >= 60 THEN 'D - Passable'
        ELSE 'F - Échec'
    END AS grade
FROM students;
```

### Exemple 4 : Priorité de Support

```sql
SELECT 
    ticket_id,
    customer_type,
    issue_severity,
    CASE
        WHEN customer_type = 'premium' AND issue_severity = 'high' THEN 'Urgent'
        WHEN customer_type = 'premium' THEN 'High'
        WHEN issue_severity = 'high' THEN 'Medium'
        ELSE 'Normal'
    END AS priority
FROM support_tickets;
```

### Exemple 5 : Calcul de Remise

```sql
SELECT 
    product_name,
    price,
    CASE
        WHEN price > 200 THEN price * 0.80  -- 20% de remise
        WHEN price > 100 THEN price * 0.90  -- 10% de remise
        WHEN price > 50 THEN price * 0.95   -- 5% de remise
        ELSE price                          -- Pas de remise
    END AS discounted_price,
    CASE
        WHEN price > 200 THEN '20%'
        WHEN price > 100 THEN '10%'
        WHEN price > 50 THEN '5%'
        ELSE '0%'
    END AS discount_rate
FROM products;
```

### Exemple 6 : Analyse de Données

```sql
SELECT 
    MONTH(order_date) AS month,
    COUNT(*) AS total_orders,
    SUM(CASE WHEN status = 'delivered' THEN 1 ELSE 0 END) AS delivered_orders,
    SUM(CASE WHEN status = 'cancelled' THEN 1 ELSE 0 END) AS cancelled_orders,
    SUM(CASE WHEN total > 100 THEN 1 ELSE 0 END) AS large_orders
FROM orders
GROUP BY MONTH(order_date);
```

---

## 📊 IF vs CASE

| Critère | IF | CASE |
|---------|----|----|
| **Conditions** | 1 condition (binaire) | Multiples conditions |
| **Syntaxe** | `IF(cond, vrai, faux)` | `CASE WHEN ... THEN ... END` |
| **Lisibilité** | Simple pour 2 options | Meilleur pour 3+ options |
| **Flexibilité** | Limitée | Très flexible |

---

## 💡 Bonnes Pratiques

```sql
-- ✅ BON : Utiliser IF pour conditions simples
SELECT IF(stock > 0, 'Available', 'Sold Out') AS status FROM products;

-- ✅ BON : Utiliser CASE pour conditions multiples
SELECT 
    CASE
        WHEN stock > 100 THEN 'High Stock'
        WHEN stock > 10 THEN 'Medium Stock'
        WHEN stock > 0 THEN 'Low Stock'
        ELSE 'Out of Stock'
    END AS stock_status
FROM products;

-- ✅ BON : Toujours inclure ELSE pour gérer les cas non prévus
SELECT 
    CASE status
        WHEN 'active' THEN 'Actif'
        WHEN 'inactive' THEN 'Inactif'
        ELSE 'Inconnu'  -- Important !
    END AS status_fr
FROM users;

-- ❌ MAUVAIS : IF imbriqués (difficile à lire)
SELECT IF(price > 100, 'Premium', IF(price > 50, 'Mid', 'Budget')) FROM products;

-- ✅ MEILLEUR : CASE pour plusieurs niveaux
SELECT 
    CASE
        WHEN price > 100 THEN 'Premium'
        WHEN price > 50 THEN 'Mid'
        ELSE 'Budget'
    END AS category
FROM products;
```

---

## 🧪 Exercices

```sql
-- Exercice 1 : Catégoriser les utilisateurs par âge
SELECT 
    name,
    age,
    CASE
        WHEN age < 18 THEN 'Mineur'
        WHEN age BETWEEN 18 AND 65 THEN 'Adulte'
        ELSE 'Senior'
    END AS age_category
FROM users;

-- Exercice 2 : Calculer le statut de livraison
SELECT 
    order_id,
    DATEDIFF(CURDATE(), order_date) AS days_since_order,
    CASE
        WHEN DATEDIFF(CURDATE(), order_date) > 30 THEN 'Retard important'
        WHEN DATEDIFF(CURDATE(), order_date) > 14 THEN 'Retard'
        WHEN DATEDIFF(CURDATE(), order_date) > 7 THEN 'Normal'
        ELSE 'Récent'
    END AS delivery_status
FROM orders;

-- Exercice 3 : Calculer une commission
SELECT 
    salesperson,
    sales_amount,
    CASE
        WHEN sales_amount > 10000 THEN sales_amount * 0.15
        WHEN sales_amount > 5000 THEN sales_amount * 0.10
        ELSE sales_amount * 0.05
    END AS commission
FROM sales;
```

---

## ➡️ Prochaine Étape

Passez au **[Module 11 : Opérateurs Arithmétiques](../11-arithmetic-operators)** pour les calculs mathématiques.