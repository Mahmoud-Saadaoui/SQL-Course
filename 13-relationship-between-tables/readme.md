# Module 13 : Relations entre Tables

## 📚 Introduction

Les relations entre tables sont au cœur des bases de données relationnelles. Ce module couvre le regroupement, le tri, et surtout les **jointures** (JOIN) qui permettent de combiner des données de plusieurs tables.

---

## 1️⃣ GROUP BY, ORDER BY et HAVING

### ORDER BY - Trier les Résultats

```sql
/* ========================================
   ORDER BY - Trier les résultats
   ASC : Ordre croissant (par défaut)
   DESC : Ordre décroissant
   ======================================== */

-- Trier par nom (ordre alphabétique)
SELECT * FROM `contribution` ORDER BY `name`;

-- Trier par nom, puis par points
SELECT * FROM `contribution` ORDER BY `name`, `points`;

-- Trier par points décroissant
SELECT * FROM `orders` ORDER BY status;
```

### GROUP BY - Regrouper les Données

```sql
/* ========================================
   GROUP BY - Regrouper les lignes ayant la même valeur
   Utilisé avec des fonctions d'agrégation (SUM, COUNT, AVG, etc.)
   ======================================== */

-- Regrouper par nom
SELECT * FROM `contribution` GROUP BY `name`;

-- Afficher nom et points
SELECT name, points FROM `contribution` GROUP BY `name`;

-- Calculer la somme des points par personne
SELECT name, SUM(points) AS total_points FROM `contribution` GROUP BY `name`;

-- Trier par points
SELECT name, SUM(points) AS total_points 
FROM `contribution` 
GROUP BY `name` 
ORDER BY total_points;

-- Trier par points décroissant
SELECT name, SUM(points) AS total_points 
FROM `contribution` 
GROUP BY `name` 
ORDER BY total_points DESC;
```

### HAVING - Filtrer les Groupes

```sql
/* ========================================
   HAVING - Filtrer après le regroupement
   Différence avec WHERE :
   - WHERE filtre AVANT le regroupement
   - HAVING filtre APRÈS le regroupement
   ======================================== */

-- Compter les statuts de commandes
SELECT status, COUNT(status) AS How_Much 
FROM `orders` 
GROUP BY status 
ORDER BY status;

-- Filtrer les groupes avec plus d'une occurrence
SELECT status, COUNT(status) AS How_Much 
FROM `orders` 
GROUP BY status 
HAVING How_Much > 1;

-- Exemple : Clients avec plus de 5 commandes
SELECT customer_id, COUNT(*) AS order_count
FROM orders
GROUP BY customer_id
HAVING order_count > 5;
```

---

## 2️⃣ Simulation de JOIN (Méthode Ancienne)

```sql
/* ========================================
   SIMULATION DE JOIN - Méthode ancienne (à éviter)
   Utilise la virgule pour combiner les tables
   ======================================== */

-- Créer une table avec clé étrangère
CREATE TABLE users (
    user_id INT NOT NULL AUTO_INCREMENT,
    user_name VARCHAR(255),
    lang_id INT NOT NULL,
    PRIMARY KEY (user_id),
    FOREIGN KEY (lang_id) REFERENCES langs(lang_id)
) ENGINE = INNODB;

-- Produit cartésien (toutes les combinaisons)
SELECT * FROM `users`, `langs`;
-- ⚠️ Résultat : Chaque utilisateur avec chaque langue (non filtré)

-- Filtrer avec WHERE (simulation de JOIN)
SELECT * FROM `users`, `langs` WHERE users.lang_id = langs.lang_id;
-- ✅ Résultat : Utilisateurs avec leur langue correspondante
```

---

## 3️⃣ Alias en Profondeur

```sql
/* ========================================
   ALIAS - Renommer temporairement tables et colonnes
   Syntaxe : table AS alias ou simplement table alias
   ======================================== */

-- Sélectionner avec nom de table complet
SELECT users.lang_id
FROM `users`, `langs`
WHERE users.lang_id = langs.lang_id;

-- Sélectionner user_id
SELECT users.user_id
FROM `users`, `langs`
WHERE users.lang_id = langs.lang_id;

-- Sélectionner plusieurs colonnes
SELECT 
    users.user_id,
    users.user_name,
    langs.lang_name
FROM `users`, `langs`
WHERE users.lang_id = langs.lang_id;

-- Avec alias de tables (u = users, l = langs)
SELECT 
    u.user_id AS User_Id,
    u.user_name AS Username,
    l.lang_name AS Fav_Lang
FROM `users` u, `langs` l
WHERE u.lang_id = l.lang_id;

-- Alias sans AS
SELECT 
    u.user_id,
    u.user_name,
    l.lang_name
FROM `users` u, `langs` l
WHERE u.lang_id = l.lang_id;
```

---

## 4️⃣ INNER JOIN - Jointure Interne

```sql
/* ========================================
   INNER JOIN - Jointure interne (moderne et recommandée)
   Retourne uniquement les lignes qui ont une correspondance dans les deux tables
   ======================================== */

-- Syntaxe INNER JOIN
SELECT 
    u.user_id,
    u.user_name,
    l.lang_name
FROM `users` u
INNER JOIN `langs` l ON u.lang_id = l.lang_id;

-- Exemple pratique : Commandes avec informations client
SELECT 
    o.order_id,
    o.total,
    c.customer_name,
    c.email
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id;

-- Jointure multiple
SELECT 
    o.order_id,
    c.customer_name,
    p.product_name,
    oi.quantity
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id
INNER JOIN order_items oi ON o.order_id = oi.order_id
INNER JOIN products p ON oi.product_id = p.product_id;
```

---

## 5️⃣ LEFT JOIN et RIGHT JOIN

### LEFT JOIN - Jointure Gauche

```sql
/* ========================================
   LEFT JOIN - Jointure gauche
   Retourne TOUTES les lignes de la table de gauche
   + les correspondances de la table de droite (NULL si pas de correspondance)
   ======================================== */

-- Tous les utilisateurs, même sans langue
SELECT 
    u.user_id,
    u.user_name,
    l.lang_name
FROM `users` u
LEFT JOIN `langs` l ON u.lang_id = l.lang_id;

-- Cas d'usage : Trouver les utilisateurs sans langue
SELECT 
    u.user_id,
    u.user_name
FROM `users` u
LEFT JOIN `langs` l ON u.lang_id = l.lang_id
WHERE l.lang_id IS NULL;

-- Exemple : Clients sans commandes
SELECT 
    c.customer_id,
    c.customer_name,
    COUNT(o.order_id) AS order_count
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
HAVING order_count = 0;
```

### RIGHT JOIN - Jointure Droite

```sql
/* ========================================
   RIGHT JOIN - Jointure droite
   Retourne TOUTES les lignes de la table de droite
   + les correspondances de la table de gauche (NULL si pas de correspondance)
   ======================================== */

-- Toutes les langues, même sans utilisateurs
SELECT 
    u.user_id,
    u.user_name,
    l.lang_name
FROM `users` u
RIGHT JOIN `langs` l ON u.lang_id = l.lang_id;

-- Cas d'usage : Langues non utilisées
SELECT 
    l.lang_id,
    l.lang_name
FROM `users` u
RIGHT JOIN `langs` l ON u.lang_id = l.lang_id
WHERE u.user_id IS NULL;
```

---

## 6️⃣ Exemples Pratiques Avancés

### Compter les Utilisations

```sql
-- Compter combien d'utilisateurs par langue
SELECT 
    l.lang_name,
    COUNT(u.user_id) AS How_Much_Used
FROM `users` u
INNER JOIN `langs` l ON u.lang_id = l.lang_id
GROUP BY l.lang_id, l.lang_name;
```

### Statistiques de Commandes

```sql
-- Statistiques par client
SELECT 
    c.customer_name,
    COUNT(o.order_id) AS total_orders,
    SUM(o.total) AS total_spent,
    AVG(o.total) AS average_order_value
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY total_spent DESC;
```

### Produits les Plus Vendus

```sql
-- Top 10 des produits
SELECT 
    p.product_name,
    SUM(oi.quantity) AS total_sold,
    SUM(oi.quantity * oi.unit_price) AS total_revenue
FROM products p
INNER JOIN order_items oi ON p.product_id = oi.product_id
GROUP BY p.product_id, p.product_name
ORDER BY total_sold DESC
LIMIT 10;
```

---

## 📊 Types de Jointures - Résumé

| Type | Description | Cas d'usage |
|------|-------------|-------------|
| **INNER JOIN** | Lignes avec correspondance dans les deux tables | Données liées obligatoires |
| **LEFT JOIN** | Toutes les lignes de gauche + correspondances | Trouver les éléments sans relation |
| **RIGHT JOIN** | Toutes les lignes de droite + correspondances | Rarement utilisé (préférer LEFT) |
| **CROSS JOIN** | Produit cartésien (toutes les combinaisons) | Cas très spécifiques |

---

## 🎯 Diagramme de Venn

```
INNER JOIN:        LEFT JOIN:         RIGHT JOIN:
    ┌───┐              ┌───┐              ┌───┐
    │ A │              │ A │              │ A │
    └─┬─┘              └─┬─┘              └─┬─┘
      │                  │                  │
    ┌─┴─┐              ┌─┴─┐              ┌─┴─┐
    │ ∩ │              │ A │              │ B │
    └─┬─┘              │ + │              │ + │
      │                │ ∩ │              │ ∩ │
    ┌─┴─┐              └─┬─┘              └─┬─┘
    │ B │                │                  │
    └───┘              ┌─┴─┐              ┌─┴─┐
                       │ B │              │ A │
                       └───┘              └───┘
```

---

## 💡 Bonnes Pratiques

```sql
-- ✅ BON : Utiliser INNER JOIN (syntaxe moderne)
SELECT * FROM users u
INNER JOIN orders o ON u.user_id = o.user_id;

-- ❌ MAUVAIS : Virgule (syntaxe ancienne)
SELECT * FROM users u, orders o WHERE u.user_id = o.user_id;

-- ✅ BON : Utiliser des alias courts et clairs
SELECT u.name, o.total
FROM users u
INNER JOIN orders o ON u.id = o.user_id;

-- ✅ BON : Toujours spécifier ON pour les JOIN
SELECT * FROM users u
INNER JOIN orders o ON u.user_id = o.user_id;

-- ✅ BON : Utiliser LEFT JOIN pour trouver les absences
SELECT u.name
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
WHERE o.order_id IS NULL;
```

---

## 🧪 Exercices

```sql
-- Exercice 1 : Lister tous les articles avec leur auteur
SELECT a.title, u.username
FROM articles a
INNER JOIN users u ON a.author_id = u.user_id;

-- Exercice 2 : Trouver les catégories sans produits
SELECT c.category_name
FROM categories c
LEFT JOIN products p ON c.category_id = p.category_id
WHERE p.product_id IS NULL;

-- Exercice 3 : Top 5 des clients par montant dépensé
SELECT 
    c.customer_name,
    SUM(o.total) AS total_spent
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY total_spent DESC
LIMIT 5;
```

---

## 🎓 Félicitations !

Vous avez terminé le cours SQL ! Vous maîtrisez maintenant :
- ✅ Les types de données
- ✅ La création et gestion des tables
- ✅ Les contraintes et l'intégrité des données
- ✅ Les fonctions de manipulation (chaînes, nombres, dates)
- ✅ Les opérateurs de comparaison et logiques
- ✅ Les conditions et calculs
- ✅ Les relations et jointures entre tables

---

## 📚 Pour Aller Plus Loin

- Transactions (BEGIN, COMMIT, ROLLBACK)
- Sous-requêtes (subqueries)
- Vues (CREATE VIEW)
- Procédures stockées (STORED PROCEDURES)
- Triggers
- Index et optimisation
- Sécurité et gestion des utilisateurs

**Bon apprentissage et bonne pratique ! 🚀**