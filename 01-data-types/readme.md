# Module 01 : Types de Données en SQL

## 📚 Introduction

Les types de données définissent le type d'information qu'une colonne peut stocker dans une table SQL. Choisir le bon type de données est crucial pour :
- **Optimiser les performances** de la base de données
- **Économiser l'espace de stockage**
- **Garantir l'intégrité des données**

---

## 1️⃣ Types Numériques (Numeric Types)

Les types numériques sont utilisés pour stocker des nombres entiers ou décimaux.

### Types d'Entiers

```sql
/* TINYINT - Pour les très petits nombres */
-- Plage : -128 à 127 (signé) ou 0 à 255 (non signé)
-- Taille : 1 octet
-- Exemple d'utilisation : âge, statut (0/1), petits compteurs
TINYINT           -- Max Length: 4 chiffres

/* SMALLINT - Pour les petits nombres */
-- Plage : -32,768 à 32,767 (signé) ou 0 à 65,535 (non signé)
-- Taille : 2 octets
-- Exemple d'utilisation : quantités, codes postaux
SMALLINT          -- Max Length: 6 chiffres

/* MEDIUMINT - Pour les nombres moyens */
-- Plage : -8,388,608 à 8,388,607 (signé) ou 0 à 16,777,215 (non signé)
-- Taille : 3 octets
-- Exemple d'utilisation : populations de villes, compteurs moyens
MEDIUMINT         -- Max Length: 9 chiffres

/* INT - Type entier standard (le plus utilisé) */
-- Plage : -2,147,483,648 à 2,147,483,647 (signé) ou 0 à 4,294,967,295 (non signé)
-- Taille : 4 octets
-- Exemple d'utilisation : IDs, prix en centimes, compteurs généraux
INT               -- Max Length: 11 chiffres

/* BIGINT - Pour les très grands nombres */
-- Plage : -9,223,372,036,854,775,808 à 9,223,372,036,854,775,807
-- Taille : 8 octets
-- Exemple d'utilisation : timestamps, très grands compteurs, données scientifiques
BIGINT            -- Max Length: 20 chiffres
```

### 💡 Conseils pour les Types Numériques

```sql
-- ✅ BON : Utiliser le type approprié pour économiser l'espace
CREATE TABLE users (
    id INT,                    -- Suffisant pour des millions d'utilisateurs
    age TINYINT,              -- L'âge ne dépasse jamais 255
    followers_count BIGINT    -- Peut contenir de très grands nombres
);

-- ❌ MAUVAIS : Gaspillage d'espace
CREATE TABLE users (
    id BIGINT,                -- Trop grand pour un simple ID
    age INT                   -- Trop grand pour un âge
);
```

---

## 2️⃣ Types Date et Heure (Date and Time Types)

Les types de date et heure permettent de stocker des informations temporelles.

```sql
/* DATE - Pour stocker uniquement la date */
-- Format : YYYY-MM-DD
-- Plage : '1000-01-01' à '9999-12-31'
-- Taille : 3 octets
-- Exemple d'utilisation : date de naissance, date d'inscription
Date              -- YYYY-MM-DD (1000-01-01 To 9999-12-31)

/* DATETIME - Pour stocker la date ET l'heure */
-- Format : YYYY-MM-DD HH:MM:SS
-- Plage : '1000-01-01 00:00:00' à '9999-12-31 23:59:59'
-- Taille : 8 octets
-- Exemple d'utilisation : date de création d'un article, historique d'actions
Datetime          -- YYYY-MM-DD HH:MM:SS

/* TIMESTAMP - Pour stocker un horodatage (timestamp) */
-- Format : YYYY-MM-DD HH:MM:SS
-- Plage : '1970-01-01 00:00:01' UTC à '2038-01-19 03:14:07' UTC
-- Taille : 4 octets
-- Avantage : Se met à jour automatiquement, converti en UTC
-- Exemple d'utilisation : dernière modification, logs système
Timestamp         -- YYYY-MM-DD HH:MM:SS

/* TIME - Pour stocker uniquement l'heure */
-- Format : HH:MM:SS
-- Plage : '-838:59:59' à '838:59:59'
-- Taille : 3 octets
-- Exemple d'utilisation : durée, heure d'ouverture
Time              -- HH:MM:SS

/* YEAR - Pour stocker uniquement l'année */
-- Format : YYYY ou YY
-- Plage : 1901 à 2155 (format YYYY) ou 70-69 (format YY = 1970-2069)
-- Taille : 1 octet
-- Exemple d'utilisation : année de fabrication, année scolaire
Year              -- YYYY | YY
```

### 💡 Exemples Pratiques

```sql
-- Créer une table avec différents types de date
CREATE TABLE events (
    id INT PRIMARY KEY,
    event_name VARCHAR(100),
    event_date DATE,                    -- Seulement la date
    event_datetime DATETIME,            -- Date et heure exacte
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  -- Auto-rempli
    duration TIME,                      -- Durée de l'événement
    year_founded YEAR                   -- Année de fondation
);

-- Insérer des données
INSERT INTO events VALUES (
    1,
    'Conférence SQL',
    '2024-12-15',                       -- Date simple
    '2024-12-15 14:30:00',             -- Date et heure
    CURRENT_TIMESTAMP,                  -- Timestamp actuel
    '02:30:00',                         -- 2h30 de durée
    2024                                -- Année
);
```

---

## 3️⃣ Types de Chaînes de Caractères (String Types)

Les types de chaînes permettent de stocker du texte.

### CHAR - Chaîne de Longueur Fixe

```sql
/* CHAR - Stocke des valeurs de longueur FIXE */
-- Caractéristiques :
--   ✓ Longueur fixe (toujours la même taille)
--   ✓ Max : 255 caractères
--   ✓ 50% plus rapide que VARCHAR
--   ✓ Utilise de la mémoire statique (réservée à l'avance)
--   ✓ Idéal pour : codes, IDs, données de taille constante

-- Exemple d'utilisation :
CREATE TABLE countries (
    country_code CHAR(2),        -- 'FR', 'US', 'CA' (toujours 2 caractères)
    postal_code CHAR(5),         -- '75001', '10001' (toujours 5 caractères)
    status CHAR(1)               -- 'A', 'I', 'P' (toujours 1 caractère)
);

-- ⚠️ Attention : CHAR(10) stockera toujours 10 caractères
-- Si vous stockez 'SQL', il sera complété avec des espaces : 'SQL       '
```

### VARCHAR - Chaîne de Longueur Variable

```sql
/* VARCHAR - Stocke des valeurs de longueur VARIABLE */
-- Caractéristiques :
--   ✓ Longueur variable (s'adapte au contenu)
--   ✓ Max : 65,535 caractères (MySQL 5.0.3+)
--   ✓ Plus lent que CHAR mais économise l'espace
--   ✓ Utilise de la mémoire dynamique (ajustée selon le contenu)
--   ✓ Idéal pour : noms, emails, commentaires, descriptions

-- Exemple d'utilisation :
CREATE TABLE users (
    username VARCHAR(50),        -- Nom d'utilisateur (max 50 caractères)
    email VARCHAR(255),          -- Email (longueur variable)
    bio VARCHAR(500),            -- Biographie courte
    comment VARCHAR(1000)        -- Commentaire (longueur variable)
);

-- ✅ Avantage : VARCHAR(100) ne stocke que la longueur réelle
-- Si vous stockez 'SQL', il prendra seulement 3 caractères + 1-2 octets d'en-tête
```

### TEXT - Texte Long

```sql
/* TEXT - Stocke de longues chaînes de caractères */
-- Caractéristiques :
--   ✓ Pour les textes longs (articles, descriptions détaillées)
--   ✓ Comparaison et tri dépendent de l'encodage (UTF-8, Latin1, etc.)
--   ✓ Plusieurs variantes : TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT
--   ✓ Idéal pour : articles, descriptions longues, contenu HTML

-- Types de TEXT :
TINYTEXT          -- Max : 255 caractères
TEXT              -- Max : 65,535 caractères (~64 KB)
MEDIUMTEXT        -- Max : 16,777,215 caractères (~16 MB)
LONGTEXT          -- Max : 4,294,967,295 caractères (~4 GB)

-- Exemple d'utilisation :
CREATE TABLE articles (
    id INT PRIMARY KEY,
    title VARCHAR(200),          -- Titre court
    summary TEXT,                -- Résumé (quelques paragraphes)
    content LONGTEXT            -- Contenu complet de l'article
);
```

### BLOB - Données Binaires

```sql
/* BLOB - Binary Large Object (Objet Binaire Large) */
-- Caractéristiques :
--   ✓ N'a PAS de charset (pas d'encodage de caractères)
--   ✓ Comparaison basée sur les valeurs numériques des octets
--   ✓ Utilisé pour stocker des fichiers binaires
--   ✓ Idéal pour : images, PDFs, fichiers audio/vidéo, fichiers compressés

-- Types de BLOB :
TINYBLOB          -- Max : 255 octets
BLOB              -- Max : 65,535 octets (~64 KB)
MEDIUMBLOB        -- Max : 16,777,215 octets (~16 MB)
LONGBLOB          -- Max : 4,294,967,295 octets (~4 GB)

-- Exemple d'utilisation :
CREATE TABLE files (
    id INT PRIMARY KEY,
    filename VARCHAR(255),
    file_type VARCHAR(50),       -- 'image/jpeg', 'application/pdf'
    file_data MEDIUMBLOB        -- Le fichier binaire lui-même
);

-- ⚠️ Note : Il est souvent préférable de stocker les fichiers sur le disque
-- et de ne garder que le chemin dans la base de données pour de meilleures performances
```

---

## 📊 Tableau Comparatif : CHAR vs VARCHAR vs TEXT

| Critère | CHAR | VARCHAR | TEXT |
|---------|------|---------|------|
| **Longueur** | Fixe | Variable | Variable |
| **Taille Max** | 255 caractères | 65,535 caractères | 65,535+ caractères |
| **Performance** | ⚡ Très rapide | 🚀 Rapide | 🐌 Plus lent |
| **Mémoire** | Statique (gaspillage possible) | Dynamique (optimisé) | Dynamique |
| **Utilisation** | Codes, IDs fixes | Noms, emails | Articles, descriptions |

---

## 🎯 Bonnes Pratiques

### ✅ À FAIRE

```sql
-- 1. Choisir le type approprié selon l'utilisation
CREATE TABLE products (
    id INT,                      -- ID numérique
    sku CHAR(10),               -- Code produit fixe
    name VARCHAR(200),          -- Nom variable
    description TEXT,           -- Description longue
    price DECIMAL(10,2),        -- Prix avec 2 décimales
    created_at TIMESTAMP        -- Date de création auto
);

-- 2. Utiliser UNSIGNED pour les valeurs toujours positives
CREATE TABLE statistics (
    views INT UNSIGNED,         -- Les vues ne peuvent pas être négatives
    likes BIGINT UNSIGNED       -- Les likes ne peuvent pas être négatifs
);
```

### ❌ À ÉVITER

```sql
-- 1. Ne pas utiliser VARCHAR pour des données de taille fixe
CREATE TABLE bad_example (
    country_code VARCHAR(2),    -- ❌ Utilisez CHAR(2)
    status VARCHAR(1)           -- ❌ Utilisez CHAR(1)
);

-- 2. Ne pas utiliser TEXT pour des données courtes
CREATE TABLE bad_example2 (
    username TEXT,              -- ❌ Utilisez VARCHAR(50)
    email TEXT                  -- ❌ Utilisez VARCHAR(255)
);

-- 3. Ne pas utiliser BIGINT partout
CREATE TABLE bad_example3 (
    id BIGINT,                  -- ❌ INT suffit généralement
    age BIGINT                  -- ❌ TINYINT est suffisant
);
```

---

## 🧪 Exercices Pratiques

### Exercice 1 : Créer une Table Utilisateurs

```sql
-- Créez une table 'users' avec les colonnes appropriées :
-- - id (nombre entier, clé primaire)
-- - username (max 50 caractères)
-- - email (max 255 caractères)
-- - age (nombre entre 0 et 150)
-- - bio (texte long)
-- - created_at (date et heure de création)
-- - is_active (0 ou 1)

-- Solution :
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    age TINYINT UNSIGNED,
    bio TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_active TINYINT(1) DEFAULT 1
);
```

### Exercice 2 : Créer une Table Produits

```sql
-- Créez une table 'products' avec :
-- - product_id (identifiant unique)
-- - product_code (code fixe de 8 caractères)
-- - name (nom du produit)
-- - description (description détaillée)
-- - price (prix avec centimes)
-- - stock_quantity (quantité en stock)
-- - image (fichier image)

-- Solution :
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_code CHAR(8) NOT NULL UNIQUE,
    name VARCHAR(200) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    stock_quantity INT UNSIGNED DEFAULT 0,
    image MEDIUMBLOB
);
```

---

## 📚 Ressources Supplémentaires

- [Documentation MySQL - Types de Données](https://dev.mysql.com/doc/refman/8.0/en/data-types.html)
- [Guide de Choix des Types de Données](https://dev.mysql.com/doc/refman/8.0/en/choosing-types.html)

---

## ➡️ Prochaine Étape

Maintenant que vous maîtrisez les types de données, passez au **[Module 02 : Tables](../02-tables)** pour apprendre à créer et gérer des tables SQL.