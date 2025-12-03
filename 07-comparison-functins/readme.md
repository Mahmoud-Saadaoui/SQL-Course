# Module 07 : Opérateurs de Comparaison (BETWEEN, IN, LIKE)

## 📚 Introduction

Les opérateurs de comparaison avancés permettent de filtrer les données de manière plus flexible et puissante que les opérateurs simples (=, >, <).

---

## 1️⃣ BETWEEN et NOT BETWEEN - Plage de Valeurs

### BETWEEN - Dans une Plage

```sql
/* ========================================
   BETWEEN - Vérifier si une valeur est dans une plage
   Syntaxe : valeur BETWEEN min AND max
   Inclut les bornes (min et max sont inclus)
   ======================================== */

-- Nombres entre 2 et 6 (inclus)
SELECT * FROM `try` WHERE number BETWEEN 2 AND 6;
-- Équivalent à : WHERE number >= 2 AND number <= 6

-- Dates dans une plage
SELECT * FROM `try` WHERE date BETWEEN '2016-05-18' AND '2016-05-25';

-- Cas d'usage 1 : Produits dans une fourchette de prix
SELECT * FROM products WHERE price BETWEEN 10 AND 50;

-- Cas d'usage 2 : Événements des 2 derniers mois
SELECT *
FROM `try`
WHERE date BETWEEN DATE_SUB(CURDATE(), INTERVAL 2 MONTH) AND CURDATE();

-- Cas d'usage 3 : Événements de l'année dernière
SELECT *
FROM `try`
WHERE date BETWEEN DATE_SUB(CURDATE(), INTERVAL 1 YEAR) AND CURDATE();
```

### NOT BETWEEN - Hors d'une Plage

```sql
/* ========================================
   NOT BETWEEN - Exclure une plage de valeurs
   ======================================== */

-- Nombres hors de la plage 2-6
SELECT * FROM `try` WHERE number NOT BETWEEN 2 AND 6;
-- Équivalent à : WHERE number < 2 OR number > 6

-- Cas d'usage : Produits hors d'une fourchette de prix
SELECT * FROM products WHERE price NOT BETWEEN 10 AND 50;

-- Cas d'usage : Dates hors d'une période
SELECT * FROM events 
WHERE event_date NOT BETWEEN '2024-01-01' AND '2024-12-31';
```

### 💡 Conseils BETWEEN

```sql
-- ✅ BON : Ordre croissant (min, max)
SELECT * FROM products WHERE price BETWEEN 10 AND 100;

-- ❌ MAUVAIS : Ordre décroissant (ne retournera rien)
SELECT * FROM products WHERE price BETWEEN 100 AND 10;

-- ✅ BON : Avec des dates
SELECT * FROM orders 
WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31';

-- ✅ BON : Avec des heures
SELECT * FROM logs 
WHERE created_at BETWEEN '2024-12-03 00:00:00' AND '2024-12-03 23:59:59';
```

---

## 2️⃣ IN et NOT IN - Liste de Valeurs

### IN - Dans une Liste

```sql
/* ========================================
   IN - Vérifier si une valeur est dans une liste
   Syntaxe : valeur IN (valeur1, valeur2, ...)
   ======================================== */

-- Nombres dans une liste
SELECT * FROM `try` WHERE number IN (4, 5, 8);
-- Équivalent à : WHERE number = 4 OR number = 5 OR number = 8

-- Avec des valeurs mixtes (SQL ignore les types incompatibles)
SELECT * FROM `try` WHERE number IN (4, 5, 8, 'Osama', 100);

-- Dates dans une liste
SELECT * FROM `try` WHERE date IN ('2016-05-25', '2016-05-18');

-- Cas d'usage 1 : Filtrer par catégories spécifiques
SELECT * FROM products 
WHERE category IN ('Electronics', 'Books', 'Clothing');

-- Cas d'usage 2 : Filtrer par IDs
SELECT * FROM users WHERE user_id IN (1, 5, 10, 15, 20);

-- Cas d'usage 3 : Statuts spécifiques
SELECT * FROM orders 
WHERE status IN ('pending', 'processing', 'shipped');
```

### NOT IN - Hors d'une Liste

```sql
/* ========================================
   NOT IN - Exclure des valeurs d'une liste
   ======================================== */

-- Exclure certains nombres
SELECT * FROM `try` WHERE number NOT IN (3, 7);
-- Équivalent à : WHERE number != 3 AND number != 7

-- Exclure certaines dates
SELECT * FROM `try` WHERE date NOT IN ('2016-07-16');

-- Cas d'usage 1 : Exclure certains utilisateurs
SELECT * FROM posts WHERE author_id NOT IN (1, 2, 3);

-- Cas d'usage 2 : Exclure certains statuts
SELECT * FROM orders WHERE status NOT IN ('cancelled', 'refunded');
```

### IN avec Sous-Requêtes

```sql
/* ========================================
   IN avec SELECT (Sous-requête)
   ======================================== */

-- Trouver les commandes des clients actifs
SELECT * FROM orders 
WHERE customer_id IN (
    SELECT id FROM customers WHERE status = 'active'
);

-- Trouver les produits jamais commandés
SELECT * FROM products 
WHERE product_id NOT IN (
    SELECT DISTINCT product_id FROM order_items
);
```

---

## 3️⃣ LIKE et NOT LIKE - Recherche de Motifs

### LIKE - Correspondance de Motif

```sql
/* ========================================
   LIKE - Recherche avec motifs (patterns)
   Symboles :
   % = Zéro ou plusieurs caractères
   _ = Exactement un caractère
   ======================================== */

-- Recherche exacte (identique à =)
SELECT * FROM `try` WHERE name = 'Osama';
SELECT * FROM `try` WHERE name LIKE 'Osama';

-- Finit par 'sama'
SELECT * FROM `try` WHERE name LIKE '%sama';
-- Trouve : 'Osama', 'Usama', 'sama'

-- Finit par 'ama'
SELECT * FROM `try` WHERE name LIKE '%ama';
-- Trouve : 'Osama', 'Obama', 'ama'

-- Contient 'ama' n'importe où
SELECT * FROM `try` WHERE name LIKE '%ama%';
-- Trouve : 'Osama', 'Samantha', 'Yamaha'

-- Contient 'i'
SELECT * FROM `try` WHERE name LIKE '%i%';

-- Commence par 'Z' et finit par 'o'
SELECT * FROM `try` WHERE name LIKE 'Z%o';
-- Trouve : 'Zoro', 'Zero'

-- Contient deux 'a'
SELECT * FROM `try` WHERE name LIKE '%a%a%';
-- Trouve : 'Samantha', 'Banana'

-- Underscore _ : Exactement un caractère
SELECT * FROM `try` WHERE name LIKE '_sama';
-- Trouve : 'Osama', 'Usama' (1 caractère + 'sama')

-- Remplacer un caractère spécifique
SELECT * FROM `try` WHERE name LIKE 'Sam_h';
-- Trouve : 'Samah', 'Samih'

-- Finit par 'm' + 1 caractère + 'h'
SELECT * FROM `try` WHERE name LIKE '%m_h';
-- Trouve : 'Samah', 'Ramih'

-- Finit par 1 caractère + 'h'
SELECT * FROM `try` WHERE name LIKE '%_h';

-- Contient au moins un caractère
SELECT * FROM `try` WHERE name LIKE '%_%';
-- Trouve tous les noms non vides
```

### NOT LIKE - Exclure un Motif

```sql
/* ========================================
   NOT LIKE - Exclure un motif
   ======================================== */

-- Exclure les noms finissant par 'sama'
SELECT * FROM `try` WHERE name NOT LIKE '%sama';

-- Cas d'usage : Exclure les emails d'un domaine
SELECT * FROM users WHERE email NOT LIKE '%@spam.com';

-- Exclure les produits commençant par 'OLD'
SELECT * FROM products WHERE name NOT LIKE 'OLD%';
```

### 💡 Exemples Pratiques LIKE

```sql
-- Cas d'usage 1 : Recherche d'emails
SELECT * FROM users WHERE email LIKE '%@gmail.com';

-- Cas d'usage 2 : Recherche de codes postaux
SELECT * FROM addresses WHERE postal_code LIKE '75%';  -- Paris

-- Cas d'usage 3 : Recherche de numéros de téléphone
SELECT * FROM contacts WHERE phone LIKE '+33%';  -- France

-- Cas d'usage 4 : Recherche insensible à la casse
SELECT * FROM products WHERE LOWER(name) LIKE '%laptop%';

-- Cas d'usage 5 : Recherche de fichiers par extension
SELECT * FROM files WHERE filename LIKE '%.pdf';

-- Cas d'usage 6 : Recherche de SKU avec format spécifique
SELECT * FROM products WHERE sku LIKE 'PRD-____-2024';
-- Trouve : 'PRD-1234-2024', 'PRD-ABCD-2024'
```

### ⚠️ Attention avec LIKE

```sql
-- ❌ MAUVAIS : LIKE avec % au début est LENT (pas d'index)
SELECT * FROM users WHERE email LIKE '%@gmail.com';

-- ✅ MEILLEUR : Si possible, éviter % au début
SELECT * FROM users WHERE email LIKE 'john%';

-- ✅ BON : Utiliser FULLTEXT pour recherche de texte
-- (nécessite un index FULLTEXT)
```

---

## 📊 Tableau Récapitulatif

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| **BETWEEN** | Dans une plage (inclus) | `price BETWEEN 10 AND 50` |
| **NOT BETWEEN** | Hors d'une plage | `price NOT BETWEEN 10 AND 50` |
| **IN** | Dans une liste | `status IN ('active', 'pending')` |
| **NOT IN** | Hors d'une liste | `status NOT IN ('cancelled')` |
| **LIKE** | Correspondance de motif | `name LIKE '%John%'` |
| **NOT LIKE** | Exclure un motif | `email NOT LIKE '%@spam.com'` |
| **%** | 0 ou plusieurs caractères | `'%abc'`, `'abc%'`, `'%abc%'` |
| **_** | Exactement 1 caractère | `'_bc'`, `'a_c'`, `'ab_'` |

---

## 🎯 Exemples Pratiques Complets

### Exemple 1 : Recherche de Produits

```sql
-- Produits entre 20€ et 100€, catégories spécifiques, nom contenant 'Pro'
SELECT * FROM products 
WHERE price BETWEEN 20 AND 100
AND category IN ('Electronics', 'Computers')
AND name LIKE '%Pro%';
```

### Exemple 2 : Filtrage de Commandes

```sql
-- Commandes du dernier mois, statuts actifs, montant > 50€
SELECT * FROM orders 
WHERE order_date BETWEEN DATE_SUB(CURDATE(), INTERVAL 1 MONTH) AND CURDATE()
AND status IN ('processing', 'shipped')
AND total > 50;
```

---

## 🧪 Exercices

```sql
-- Exercice 1 : Trouver les utilisateurs avec email Gmail ou Yahoo
SELECT * FROM users 
WHERE email LIKE '%@gmail.com' OR email LIKE '%@yahoo.com';

-- Exercice 2 : Produits hors stock (0) et prix entre 10 et 100
SELECT * FROM products 
WHERE stock = 0 
AND price BETWEEN 10 AND 100;

-- Exercice 3 : Articles publiés cette année, catégories spécifiques
SELECT * FROM articles 
WHERE YEAR(published_at) = YEAR(CURDATE())
AND category IN ('Technology', 'Science', 'Business');
```

---

## ➡️ Prochaine Étape

Passez au **[Module 08 : Opérateurs de Comparaison Simples](../08-Comparison-Operators)** pour les opérateurs =, >, <, etc.