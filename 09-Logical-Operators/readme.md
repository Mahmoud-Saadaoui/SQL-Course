# Module 09 : Opérateurs Logiques

## 📚 Introduction

Les opérateurs logiques permettent de combiner plusieurs conditions dans une requête SQL pour créer des filtres complexes et précis.

---

## Opérateurs Logiques

```sql
/* ========================================
   OPÉRATEURS LOGIQUES
   AND, && - ET logique (toutes les conditions doivent être vraies)
   OR, || - OU logique (au moins une condition doit être vraie)
   NOT, ! - NON logique (inverse la condition)
   XOR - OU EXCLUSIF (une seule condition doit être vraie, pas les deux)
   ======================================== */
```

---

## 1️⃣ AND - ET Logique

### Syntaxe et Utilisation

```sql
/* ========================================
   AND - Toutes les conditions doivent être VRAIES
   Syntaxe : condition1 AND condition2 AND condition3...
   ======================================== */

-- Nom finit par 'sama' ET number > 100
SELECT * FROM `try` WHERE `name` LIKE '%sama' AND `number` > 100;
-- Résultat : Lignes où LES DEUX conditions sont vraies

-- Nom finit par 'sama' ET number < 10
SELECT * FROM `try` WHERE `name` LIKE '%sama' AND `number` < 10;

-- Syntaxe alternative avec &&
SELECT * FROM `try` WHERE `name` LIKE '%sama' && `number` > 100;
```

### Exemples Pratiques AND

```sql
-- Produits entre 20€ et 100€ avec stock disponible
SELECT * FROM products 
WHERE price >= 20 AND price <= 100 AND stock > 0;

-- Utilisateurs actifs créés cette année
SELECT * FROM users 
WHERE status = 'active' AND YEAR(created_at) = 2024;

-- Commandes livrées avec montant > 50€
SELECT * FROM orders 
WHERE status = 'delivered' AND total > 50;

-- Articles publiés, catégorie Technology, auteur spécifique
SELECT * FROM articles 
WHERE status = 'published' 
AND category = 'Technology' 
AND author_id = 5;
```

---

## 2️⃣ OR - OU Logique

### Syntaxe et Utilisation

```sql
/* ========================================
   OR - Au moins UNE condition doit être VRAIE
   Syntaxe : condition1 OR condition2 OR condition3...
   ======================================== */

-- Nom finit par 'sama' OU number > 100
SELECT * FROM `try` WHERE `name` LIKE '%sama' OR `number` > 100;
-- Résultat : Lignes où AU MOINS UNE condition est vraie

-- Syntaxe alternative avec ||
SELECT * FROM `try` WHERE `name` LIKE '%sama' || `number` > 100;
```

### Exemples Pratiques OR

```sql
-- Produits en promotion OU nouveaux
SELECT * FROM products 
WHERE on_sale = 1 OR is_new = 1;

-- Commandes en attente OU en traitement
SELECT * FROM orders 
WHERE status = 'pending' OR status = 'processing';

-- Utilisateurs admin OU modérateurs
SELECT * FROM users 
WHERE role = 'admin' OR role = 'moderator';

-- Articles de catégorie Technology OU Science
SELECT * FROM articles 
WHERE category = 'Technology' OR category = 'Science';

-- ✅ MEILLEUR avec IN pour plusieurs valeurs
SELECT * FROM articles 
WHERE category IN ('Technology', 'Science', 'Business');
```

---

## 3️⃣ NOT - NON Logique

### Syntaxe et Utilisation

```sql
/* ========================================
   NOT - INVERSE la condition
   Syntaxe : NOT condition
   ======================================== */

-- Nom ne finit PAS par 'sama'
SELECT * FROM `try` WHERE NOT `name` LIKE '%sama';
-- Équivalent à : WHERE `name` NOT LIKE '%sama'

-- Syntaxe alternative avec !
SELECT * FROM `try` WHERE !(`name` LIKE '%sama');
```

### Exemples Pratiques NOT

```sql
-- Produits PAS en rupture de stock
SELECT * FROM products WHERE NOT stock = 0;
-- Équivalent à : WHERE stock != 0 ou WHERE stock > 0

-- Utilisateurs PAS bannis
SELECT * FROM users WHERE NOT status = 'banned';

-- Commandes PAS annulées
SELECT * FROM orders WHERE NOT status = 'cancelled';

-- Articles PAS en brouillon
SELECT * FROM articles WHERE NOT status = 'draft';

-- Combiner NOT avec AND
SELECT * FROM products 
WHERE NOT (price < 10 OR stock = 0);
-- Produits avec prix >= 10 ET stock > 0
```

---

## 4️⃣ XOR - OU EXCLUSIF

### Syntaxe et Utilisation

```sql
/* ========================================
   XOR - Une SEULE condition doit être vraie (pas les deux)
   Syntaxe : condition1 XOR condition2
   Résultat : TRUE si exactement UNE condition est vraie
   ======================================== */

-- Nom finit par 'sama' XOR number < 10
SELECT * FROM `try` WHERE `name` LIKE '%sama' XOR `number` < 10;
-- Résultat : Lignes où UNE SEULE des deux conditions est vraie

-- Nom finit par 'xxxx' XOR number > 100
SELECT * FROM `try` WHERE `name` LIKE '%xxxx' XOR `number` > 100;
```

### Exemples Pratiques XOR

```sql
-- Produits en promotion OU nouveaux (mais pas les deux)
SELECT * FROM products 
WHERE on_sale = 1 XOR is_new = 1;

-- Utilisateurs avec email vérifié OU téléphone vérifié (mais pas les deux)
SELECT * FROM users 
WHERE email_verified = 1 XOR phone_verified = 1;
```

### 📊 Table de Vérité XOR

| Condition 1 | Condition 2 | Résultat XOR |
|-------------|-------------|--------------|
| TRUE | TRUE | FALSE |
| TRUE | FALSE | TRUE |
| FALSE | TRUE | TRUE |
| FALSE | FALSE | FALSE |

---

## 5️⃣ Combiner les Opérateurs

### Priorité des Opérateurs

```sql
/* ========================================
   PRIORITÉ DES OPÉRATEURS (du plus prioritaire au moins)
   1. NOT
   2. AND
   3. OR, XOR
   ======================================== */

-- Sans parenthèses (AND est prioritaire)
SELECT * FROM products 
WHERE category = 'Electronics' OR category = 'Books' AND price < 20;
-- Interprété comme : category = 'Electronics' OR (category = 'Books' AND price < 20)

-- ✅ MEILLEUR : Avec parenthèses pour clarifier
SELECT * FROM products 
WHERE (category = 'Electronics' OR category = 'Books') AND price < 20;
```

### Exemples Complexes

```sql
-- Produits Electronics ou Books, prix entre 10 et 50, en stock
SELECT * FROM products 
WHERE (category = 'Electronics' OR category = 'Books')
AND price BETWEEN 10 AND 50
AND stock > 0;

-- Utilisateurs actifs ou premium, créés cette année, email vérifié
SELECT * FROM users 
WHERE (status = 'active' OR is_premium = 1)
AND YEAR(created_at) = 2024
AND email_verified = 1;

-- Commandes livrées ou en transit, montant > 100, pas annulées
SELECT * FROM orders 
WHERE (status = 'delivered' OR status = 'in_transit')
AND total > 100
AND NOT status = 'cancelled';
```

---

## 📊 Tableau Récapitulatif

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| **AND** | Toutes les conditions vraies | `price > 10 AND stock > 0` |
| **OR** | Au moins une condition vraie | `status = 'active' OR status = 'pending'` |
| **NOT** | Inverse la condition | `NOT status = 'banned'` |
| **XOR** | Une seule condition vraie | `on_sale = 1 XOR is_new = 1` |

---

## 💡 Bonnes Pratiques

```sql
-- ✅ BON : Utiliser des parenthèses pour clarifier
SELECT * FROM products 
WHERE (category = 'A' OR category = 'B') AND price < 50;

-- ❌ MAUVAIS : Ambigu sans parenthèses
SELECT * FROM products 
WHERE category = 'A' OR category = 'B' AND price < 50;

-- ✅ BON : Utiliser IN au lieu de multiples OR
SELECT * FROM products WHERE category IN ('A', 'B', 'C');

-- ❌ MAUVAIS : Répétitif
SELECT * FROM products 
WHERE category = 'A' OR category = 'B' OR category = 'C';

-- ✅ BON : Utiliser NOT IN
SELECT * FROM users WHERE status NOT IN ('banned', 'deleted');

-- ❌ MAUVAIS : Répétitif
SELECT * FROM users WHERE status != 'banned' AND status != 'deleted';
```

---

## 🧪 Exercices

```sql
-- Exercice 1 : Produits Electronics ou Computers, prix < 500, en stock
SELECT * FROM products 
WHERE (category = 'Electronics' OR category = 'Computers')
AND price < 500
AND stock > 0;

-- Exercice 2 : Utilisateurs actifs, créés en 2024, email OU téléphone vérifié
SELECT * FROM users 
WHERE status = 'active'
AND YEAR(created_at) = 2024
AND (email_verified = 1 OR phone_verified = 1);

-- Exercice 3 : Commandes livrées, montant > 50, PAS de retour
SELECT * FROM orders 
WHERE status = 'delivered'
AND total > 50
AND NOT has_return = 1;
```

---

## ➡️ Prochaine Étape

Passez au **[Module 10 : Conditions IF](../10-if)** pour apprendre les structures conditionnelles.