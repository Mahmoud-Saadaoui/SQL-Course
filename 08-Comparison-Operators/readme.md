# Module 08 : Opérateurs de Comparaison Simples

## 📚 Introduction

Les opérateurs de comparaison simples sont les fondations du filtrage de données en SQL. Ils permettent de comparer des valeurs et de filtrer les résultats.

---

## Opérateurs de Comparaison

```sql
/* ========================================
   OPÉRATEURS DE COMPARAISON SIMPLES
   = Equal (Égal)
   > Greater Than (Plus grand que)
   >= Greater Than Or Equal (Plus grand ou égal)
   < Less Than (Plus petit que)
   <= Less Than Or Equal (Plus petit ou égal)
   != Or <> Not Equal (Différent)
   ======================================== */

-- = ÉGAL
SELECT * FROM `try` WHERE number = 1;
-- Trouve les lignes où number est exactement 1

-- > PLUS GRAND QUE
SELECT * FROM `try` WHERE number > 1;
-- Trouve les lignes où number est strictement supérieur à 1

-- >= PLUS GRAND OU ÉGAL
SELECT * FROM `try` WHERE number >= 1;
-- Trouve les lignes où number est supérieur ou égal à 1

-- < PLUS PETIT QUE
SELECT * FROM `try` WHERE number < 6;
-- Trouve les lignes où number est strictement inférieur à 6

-- <= PLUS PETIT OU ÉGAL
SELECT * FROM `try` WHERE number <= 6;
-- Trouve les lignes où number est inférieur ou égal à 6

-- != DIFFÉRENT (méthode 1)
SELECT * FROM `try` WHERE number != 6;
-- Trouve les lignes où number n'est PAS égal à 6

-- <> DIFFÉRENT (méthode 2, identique à !=)
SELECT * FROM `try` WHERE number <> 2;
-- Trouve les lignes où number n'est PAS égal à 2
```

---

## 🎯 Exemples Pratiques

### Avec des Nombres

```sql
-- Produits à moins de 50€
SELECT * FROM products WHERE price < 50;

-- Produits à 50€ ou plus
SELECT * FROM products WHERE price >= 50;

-- Produits exactement à 99.99€
SELECT * FROM products WHERE price = 99.99;

-- Produits pas à 0€
SELECT * FROM products WHERE price != 0;

-- Stock faible (moins de 10 unités)
SELECT * FROM products WHERE stock < 10;
```

### Avec des Dates

```sql
-- Commandes après le 1er janvier 2024
SELECT * FROM orders WHERE order_date > '2024-01-01';

-- Commandes du 1er janvier 2024 ou après
SELECT * FROM orders WHERE order_date >= '2024-01-01';

-- Commandes avant le 1er janvier 2024
SELECT * FROM orders WHERE order_date < '2024-01-01';

-- Commandes d'aujourd'hui
SELECT * FROM orders WHERE order_date = CURDATE();

-- Commandes pas d'aujourd'hui
SELECT * FROM orders WHERE order_date != CURDATE();
```

### Avec des Chaînes

```sql
-- Utilisateurs avec username 'admin'
SELECT * FROM users WHERE username = 'admin';

-- Catégories différentes de 'Electronics'
SELECT * FROM products WHERE category != 'Electronics';

-- Comparaison alphabétique
SELECT * FROM users WHERE username > 'M';  -- Noms après 'M'
SELECT * FROM users WHERE username < 'M';  -- Noms avant 'M'
```

---

## 📊 Tableau Récapitulatif

| Opérateur | Signification | Exemple | Résultat |
|-----------|---------------|---------|----------|
| **=** | Égal | `5 = 5` | TRUE |
| **!=** ou **<>** | Différent | `5 != 3` | TRUE |
| **>** | Plus grand | `5 > 3` | TRUE |
| **>=** | Plus grand ou égal | `5 >= 5` | TRUE |
| **<** | Plus petit | `3 < 5` | TRUE |
| **<=** | Plus petit ou égal | `5 <= 5` | TRUE |

---

## 💡 Bonnes Pratiques

```sql
-- ✅ BON : Comparaison avec le bon type
SELECT * FROM products WHERE price = 99.99;  -- Nombre
SELECT * FROM orders WHERE order_date = '2024-12-03';  -- Date

-- ❌ ATTENTION : Comparaison de chaînes (sensible à la casse)
SELECT * FROM users WHERE email = 'John@Example.com';  -- Peut ne rien trouver

-- ✅ MEILLEUR : Comparaison insensible à la casse
SELECT * FROM users WHERE LOWER(email) = LOWER('John@Example.com');

-- ✅ BON : Combiner plusieurs conditions
SELECT * FROM products 
WHERE price >= 10 AND price <= 100 AND stock > 0;
```

---

## 🧪 Exercices

```sql
-- Exercice 1 : Trouver les produits entre 20€ et 50€
SELECT * FROM products WHERE price >= 20 AND price <= 50;

-- Exercice 2 : Utilisateurs créés cette année
SELECT * FROM users WHERE YEAR(created_at) = YEAR(CURDATE());

-- Exercice 3 : Commandes avec total supérieur à 100€
SELECT * FROM orders WHERE total > 100;
```

---

## ➡️ Prochaine Étape

Passez au **[Module 09 : Opérateurs Logiques](../09-Logical-Operators)** pour combiner plusieurs conditions.