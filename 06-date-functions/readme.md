# Module 06 : Fonctions de Date et Heure

## 📚 Introduction

Les fonctions de date et heure permettent de manipuler, formater et calculer des informations temporelles dans SQL.

---

## 1️⃣ CURTIME, CURDATE et NOW - Date/Heure Actuelles

### CURTIME - Heure Actuelle

```sql
/* ========================================
   CURTIME - Obtenir l'heure actuelle
   Format : HH:MM:SS
   ======================================== */

SELECT CURTIME();           -- Exemple : '14:30:25'
SELECT CURRENT_TIME();      -- Identique
SELECT CURRENT_TIME;        -- Identique (sans parenthèses)

-- Cas d'usage : Enregistrer l'heure d'une action
INSERT INTO logs (action, time) VALUES ('login', CURTIME());
```

### CURDATE - Date Actuelle

```sql
/* ========================================
   CURDATE - Obtenir la date actuelle
   Format : YYYY-MM-DD
   ======================================== */

SELECT CURDATE();           -- Exemple : '2024-12-03'
SELECT CURRENT_DATE();      -- Identique
SELECT CURRENT_DATE;        -- Identique (sans parenthèses)

-- Cas d'usage : Filtrer les enregistrements d'aujourd'hui
SELECT * FROM orders WHERE order_date = CURDATE();
```

### NOW - Date et Heure Actuelles

```sql
/* ========================================
   NOW - Obtenir la date ET l'heure actuelles
   Format : YYYY-MM-DD HH:MM:SS
   ======================================== */

SELECT NOW();                    -- Exemple : '2024-12-03 14:30:25'
SELECT CURRENT_TIMESTAMP();      -- Identique
SELECT CURRENT_TIMESTAMP;        -- Identique (sans parenthèses)

-- Cas d'usage : Timestamp de création
INSERT INTO posts (title, created_at) VALUES ('Mon Article', NOW());

-- Utiliser dans une condition
SELECT * FROM events WHERE event_datetime > NOW();
```

---

## 2️⃣ DAY, DAYNAME et Fonctions Associées

### DAYNAME - Nom du Jour

```sql
/* ========================================
   DAYNAME - Obtenir le nom du jour
   Résultat : Monday, Tuesday, Wednesday, etc.
   ======================================== */

-- Jour actuel
SELECT CURDATE() AS Today, DAYNAME(CURDATE()) AS TodayName;
-- Résultat : '2024-12-03', 'Tuesday'

-- Jour spécifique
SELECT DAYNAME('2016-07-30') AS DayName;
-- Résultat : 'Saturday'

-- Avec une table
SELECT name, date, DAYNAME(date) AS day_name FROM `try`;

-- Cas d'usage : Trouver les commandes du week-end
SELECT * FROM orders 
WHERE DAYNAME(order_date) IN ('Saturday', 'Sunday');
```

### DAYOFMONTH - Jour du Mois

```sql
/* ========================================
   DAYOFMONTH - Numéro du jour dans le mois (1-31)
   ======================================== */

SELECT DAYOFMONTH('2024-12-03');  -- Résultat : 3
SELECT DAYOFMONTH(CURDATE());     -- Jour actuel du mois

-- Avec une table
SELECT name, date, DAYOFMONTH(date) AS day_number FROM `try`;

-- Cas d'usage : Trouver les événements du 1er du mois
SELECT * FROM events WHERE DAYOFMONTH(event_date) = 1;
```

### DAYOFWEEK - Jour de la Semaine (Numéro)

```sql
/* ========================================
   DAYOFWEEK - Numéro du jour dans la semaine
   1 = Dimanche, 2 = Lundi, ..., 7 = Samedi
   ======================================== */

SELECT DAYOFWEEK('2024-12-03');   -- Résultat : 3 (Mardi)
SELECT DAYOFWEEK(CURDATE());

-- Avec une table
SELECT name, date, DAYOFWEEK(date) AS day_num FROM `try`;

-- Cas d'usage : Filtrer les jours de semaine (Lundi-Vendredi)
SELECT * FROM attendance 
WHERE DAYOFWEEK(date) BETWEEN 2 AND 6;
```

### DAYOFYEAR - Jour de l'Année

```sql
/* ========================================
   DAYOFYEAR - Numéro du jour dans l'année (1-366)
   ======================================== */

SELECT DAYOFYEAR('2024-12-03');   -- Résultat : 338
SELECT DAYOFYEAR(CURDATE());

-- Avec une table
SELECT name, date, DAYOFYEAR(date) AS day_of_year FROM `try`;

-- Exemple complet
SELECT 
    name, 
    date, 
    DAYNAME(date) AS day_name,
    DAYOFMONTH(date) AS day_of_month,
    DAYOFWEEK(date) AS day_of_week,
    DAYOFYEAR(date) AS day_of_year
FROM `try`;
```

---

## 3️⃣ MONTH, HOUR, MINUTE - Extraction de Composants

### MONTH et MONTHNAME

```sql
/* ========================================
   MONTH - Numéro du mois (1-12)
   MONTHNAME - Nom du mois
   ======================================== */

-- Numéro du mois
SELECT MONTH('2024-12-03');       -- Résultat : 12
SELECT MONTH(CURDATE());

-- Nom du mois
SELECT MONTHNAME('2024-12-03');   -- Résultat : 'December'
SELECT MONTHNAME(CURDATE());

-- Avec une table
SELECT date, MONTH(date) AS month_num FROM `try`;
SELECT date, MONTHNAME(date) AS month_name FROM `try`;

-- Combiné
SELECT 
    date, 
    MONTH(date) AS month_num, 
    MONTHNAME(date) AS month_name
FROM `try`;

-- Cas d'usage : Statistiques mensuelles
SELECT 
    MONTHNAME(order_date) AS month,
    COUNT(*) AS total_orders,
    SUM(total) AS total_sales
FROM orders
GROUP BY MONTH(order_date), MONTHNAME(order_date)
ORDER BY MONTH(order_date);
```

### HOUR et MINUTE

```sql
/* ========================================
   HOUR - Heure (0-23)
   MINUTE - Minute (0-59)
   ======================================== */

-- Extraire l'heure
SELECT HOUR('2024-12-03 14:30:25');   -- Résultat : 14
SELECT HOUR(NOW());

-- Extraire les minutes
SELECT MINUTE('2024-12-03 14:30:25'); -- Résultat : 30
SELECT MINUTE(NOW());

-- Avec une table
SELECT 
    date, 
    HOUR(date) AS hour, 
    MINUTE(date) AS minute
FROM `try`;

-- Cas d'usage : Analyser les heures de pointe
SELECT 
    HOUR(created_at) AS hour,
    COUNT(*) AS orders_count
FROM orders
GROUP BY HOUR(created_at)
ORDER BY orders_count DESC;
```

### YEAR et SECOND

```sql
/* ========================================
   YEAR - Année
   SECOND - Secondes (0-59)
   ======================================== */

SELECT YEAR('2024-12-03');        -- Résultat : 2024
SELECT SECOND('14:30:25');        -- Résultat : 25

-- Cas d'usage : Filtrer par année
SELECT * FROM posts WHERE YEAR(created_at) = 2024;
```

---

## 4️⃣ DATEDIFF - Différence entre Dates

```sql
/* ========================================
   DATEDIFF - Calculer la différence en jours
   Syntaxe : DATEDIFF(date1, date2)
   Résultat : date1 - date2 (en jours)
   ======================================== */

-- Différence entre deux dates
SELECT DATEDIFF('2024-12-10', '2024-12-03');  -- Résultat : 7 jours

-- Jours depuis une date
SELECT DATEDIFF(CURDATE(), '2024-01-01');     -- Jours depuis le 1er janvier

-- Avec une table
SELECT 
    id, 
    name, 
    date, 
    DATEDIFF(CURDATE(), date) AS days_ago
FROM `try`;

-- Formaté avec texte
SELECT 
    id, 
    name, 
    date,
    CONCAT('Registered ', DATEDIFF(CURDATE(), date), ' Days Ago.') AS registration_info
FROM `try`;

-- Cas d'usage 1 : Calculer l'âge d'un compte
SELECT 
    username,
    created_at,
    DATEDIFF(CURDATE(), created_at) AS account_age_days,
    FLOOR(DATEDIFF(CURDATE(), created_at) / 365) AS account_age_years
FROM users;

-- Cas d'usage 2 : Trouver les abonnements expirant bientôt
SELECT * FROM subscriptions 
WHERE DATEDIFF(expiry_date, CURDATE()) BETWEEN 0 AND 7;
```

---

## 5️⃣ DATE_ADD, DATE_SUB et LAST_DAY

### LAST_DAY - Dernier Jour du Mois

```sql
/* ========================================
   LAST_DAY - Obtenir le dernier jour du mois
   Syntaxe : LAST_DAY(date)
   ======================================== */

-- Dernier jour du mois actuel
SELECT LAST_DAY(CURDATE());       -- Exemple : '2024-12-31'

-- Dernier jour d'un mois spécifique
SELECT LAST_DAY('2024-02-15');    -- Résultat : '2024-02-29' (année bissextile)

-- Avec une table
SELECT name, date, LAST_DAY(date) AS month_end FROM `try`;

-- Avec le nom du jour
SELECT 
    name, 
    date, 
    LAST_DAY(date) AS month_end,
    DAYNAME(LAST_DAY(date)) AS last_day_name
FROM `try`;

-- Cas d'usage : Calculer la fin du mois de facturation
SELECT 
    invoice_date,
    LAST_DAY(invoice_date) AS billing_period_end
FROM invoices;
```

### DATE_ADD - Ajouter une Période

```sql
/* ========================================
   DATE_ADD - Ajouter une période à une date
   Syntaxe : DATE_ADD(date, INTERVAL valeur unité)
   Unités : DAY, WEEK, MONTH, YEAR, HOUR, MINUTE, SECOND
   ======================================== */

-- Ajouter 10 jours
SELECT DATE_ADD('2024-12-03', INTERVAL 10 DAY);
-- Résultat : '2024-12-13'

-- Ajouter 1 mois
SELECT DATE_ADD('2024-12-03', INTERVAL 1 MONTH);
-- Résultat : '2025-01-03'

-- Ajouter 1 an
SELECT DATE_ADD('2024-12-03', INTERVAL 1 YEAR);
-- Résultat : '2025-12-03'

-- Ajouter des heures
SELECT DATE_ADD(NOW(), INTERVAL 2 HOUR);

-- Mettre à jour une table
UPDATE `try` SET date = DATE_ADD(date, INTERVAL 10 DAY);
UPDATE `try` SET date = DATE_ADD(date, INTERVAL 1 MONTH);

-- Cas d'usage 1 : Calculer la date d'expiration
SELECT 
    subscription_date,
    DATE_ADD(subscription_date, INTERVAL 1 YEAR) AS expiry_date
FROM subscriptions;

-- Cas d'usage 2 : Rappels futurs
INSERT INTO reminders (task, remind_at)
VALUES ('Renouveler abonnement', DATE_ADD(CURDATE(), INTERVAL 30 DAY));
```

### DATE_SUB - Soustraire une Période

```sql
/* ========================================
   DATE_SUB - Soustraire une période d'une date
   Syntaxe : DATE_SUB(date, INTERVAL valeur unité)
   ======================================== */

-- Soustraire 1 mois
SELECT DATE_SUB('2024-12-03', INTERVAL 1 MONTH);
-- Résultat : '2024-11-03'

-- Soustraire 7 jours
SELECT DATE_SUB(CURDATE(), INTERVAL 7 DAY);
-- Il y a une semaine

-- Mettre à jour une table
UPDATE `try` SET date = DATE_SUB(date, INTERVAL 1 MONTH);

-- Cas d'usage : Trouver les commandes de la semaine dernière
SELECT * FROM orders 
WHERE order_date BETWEEN DATE_SUB(CURDATE(), INTERVAL 7 DAY) AND CURDATE();

-- Cas d'usage : Données des 30 derniers jours
SELECT * FROM analytics 
WHERE date >= DATE_SUB(CURDATE(), INTERVAL 30 DAY);
```

---

## 6️⃣ DATE_FORMAT - Formater les Dates

```sql
/* ========================================
   DATE_FORMAT - Formater une date personnalisée
   Syntaxe : DATE_FORMAT(date, format)
   ======================================== */

-- Formats courants
SELECT DATE_FORMAT(NOW(), '%d/%m/%Y');           -- '03/12/2024'
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d');           -- '2024-12-03'
SELECT DATE_FORMAT(NOW(), '%d %M %Y');           -- '03 December 2024'
SELECT DATE_FORMAT(NOW(), '%W, %d %M %Y');       -- 'Tuesday, 03 December 2024'
SELECT DATE_FORMAT(NOW(), '%H:%i:%s');           -- '14:30:25'
SELECT DATE_FORMAT(NOW(), '%d/%m/%Y %H:%i');     -- '03/12/2024 14:30'

-- Codes de format principaux :
-- %d : Jour (01-31)
-- %m : Mois numérique (01-12)
-- %Y : Année (4 chiffres)
-- %y : Année (2 chiffres)
-- %M : Nom du mois (January-December)
-- %b : Nom du mois abrégé (Jan-Dec)
-- %W : Nom du jour (Monday-Sunday)
-- %a : Nom du jour abrégé (Mon-Sun)
-- %H : Heure (00-23)
-- %i : Minutes (00-59)
-- %s : Secondes (00-59)

-- Cas d'usage : Affichage formaté
SELECT 
    title,
    DATE_FORMAT(created_at, '%d %M %Y à %H:%i') AS published_date
FROM articles;
```

---

## 📊 Tableau Récapitulatif

| Fonction | Description | Exemple |
|----------|-------------|---------|
| **CURDATE()** | Date actuelle | `'2024-12-03'` |
| **CURTIME()** | Heure actuelle | `'14:30:25'` |
| **NOW()** | Date et heure actuelles | `'2024-12-03 14:30:25'` |
| **DAYNAME()** | Nom du jour | `'Tuesday'` |
| **MONTH()** | Numéro du mois | `12` |
| **YEAR()** | Année | `2024` |
| **DATEDIFF()** | Différence en jours | `DATEDIFF('2024-12-10', '2024-12-03')` → 7 |
| **DATE_ADD()** | Ajouter une période | `DATE_ADD(date, INTERVAL 1 MONTH)` |
| **DATE_SUB()** | Soustraire une période | `DATE_SUB(date, INTERVAL 7 DAY)` |
| **LAST_DAY()** | Dernier jour du mois | `'2024-12-31'` |

---

## 🎯 Exemples Pratiques

### Exemple 1 : Rapport d'Activité

```sql
SELECT 
    DATE_FORMAT(created_at, '%Y-%m') AS month,
    COUNT(*) AS total_users,
    COUNT(CASE WHEN DAYOFWEEK(created_at) IN (1,7) THEN 1 END) AS weekend_signups
FROM users
WHERE created_at >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
GROUP BY DATE_FORMAT(created_at, '%Y-%m')
ORDER BY month;
```

### Exemple 2 : Gestion d'Abonnements

```sql
SELECT 
    user_id,
    subscription_date,
    DATE_ADD(subscription_date, INTERVAL 1 YEAR) AS expiry_date,
    DATEDIFF(DATE_ADD(subscription_date, INTERVAL 1 YEAR), CURDATE()) AS days_remaining,
    CASE 
        WHEN DATEDIFF(DATE_ADD(subscription_date, INTERVAL 1 YEAR), CURDATE()) < 0 THEN 'Expiré'
        WHEN DATEDIFF(DATE_ADD(subscription_date, INTERVAL 1 YEAR), CURDATE()) <= 30 THEN 'Expire bientôt'
        ELSE 'Actif'
    END AS status
FROM subscriptions;
```

---

## ➡️ Prochaine Étape

Passez au **[Module 07 : Fonctions de Comparaison](../07-comparison-functins)** pour apprendre les opérateurs de comparaison.