# Module 02 : Gestion des Tables en SQL

## 📚 Introduction

Les tables sont la structure fondamentale d'une base de données relationnelle. Ce module vous apprendra à créer, modifier, et gérer des tables SQL efficacement.

---

## 1️⃣ Gestion des Bases de Données

Avant de créer des tables, vous devez d'abord créer et gérer des bases de données.

```sql
/* ========================================
   CRÉER UNE BASE DE DONNÉES
   ======================================== */

-- Créer une nouvelle base de données nommée 'elzero'
CREATE DATABASE elzero;

-- ✅ Bonne pratique : Vérifier si la base existe déjà
CREATE DATABASE IF NOT EXISTS elzero;

-- 💡 Créer avec un encodage spécifique (UTF-8 recommandé)
CREATE DATABASE elzero 
CHARACTER SET utf8mb4 
COLLATE utf8mb4_unicode_ci;


/* ========================================
   AFFICHER LES BASES DE DONNÉES
   ======================================== */

-- Afficher toutes les bases de données disponibles
SHOW DATABASES;

-- Afficher uniquement les bases dont le nom contient 'elzero'
SHOW DATABASES LIKE 'elzero';

-- Afficher les bases commençant par 'el'
SHOW DATABASES LIKE 'el%';


/* ========================================
   UTILISER UNE BASE DE DONNÉES
   ======================================== */

-- Sélectionner la base de données 'elzero' pour l'utiliser
-- Toutes les commandes suivantes s'appliqueront à cette base
USE elzero;

-- 💡 Vérifier quelle base de données est actuellement sélectionnée
SELECT DATABASE();


/* ========================================
   SUPPRIMER UNE BASE DE DONNÉES
   ======================================== */

-- ⚠️ ATTENTION : Cette commande supprime DÉFINITIVEMENT la base et toutes ses tables
DROP DATABASE elzero;

-- ✅ Bonne pratique : Vérifier si la base existe avant de la supprimer
DROP DATABASE IF EXISTS elzero;
```

---

## 2️⃣ Créer, Afficher et Supprimer des Tables

### Créer une Table

```sql
/* ========================================
   CRÉER UNE TABLE SIMPLE
   ======================================== */

-- Créer une table 'students' avec 3 colonnes
CREATE TABLE students (
    id INT(11),              -- Identifiant numérique (max 11 chiffres)
    name VARCHAR(255),       -- Nom de l'étudiant (max 255 caractères)
    email VARCHAR(255)       -- Email de l'étudiant
);

-- ✅ Version améliorée avec contraintes
CREATE TABLE students (
    id INT(11) PRIMARY KEY AUTO_INCREMENT,  -- ID auto-incrémenté
    name VARCHAR(255) NOT NULL,             -- Nom obligatoire
    email VARCHAR(255) NOT NULL UNIQUE,     -- Email unique et obligatoire
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP  -- Date de création auto
);


/* ========================================
   CRÉER UNE TABLE AVEC MOTEUR SPÉCIFIQUE
   ======================================== */

-- InnoDB : Moteur recommandé (supporte les transactions et clés étrangères)
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    total DECIMAL(10,2)
) ENGINE = InnoDB;

-- MyISAM : Moteur plus rapide pour la lecture (pas de transactions)
CREATE TABLE logs (
    log_id INT PRIMARY KEY,
    message TEXT
) ENGINE = MyISAM;
```

### Afficher les Informations d'une Table

```sql
/* ========================================
   AFFICHER LA STRUCTURE D'UNE TABLE
   ======================================== */

-- Méthode 1 : DESCRIBE (la plus courante)
DESCRIBE students;

-- Méthode 2 : DESC (raccourci de DESCRIBE)
DESC students;

-- Méthode 3 : EXPLAIN (identique à DESCRIBE)
EXPLAIN students;

-- Méthode 4 : SHOW COLUMNS (affichage détaillé)
SHOW COLUMNS FROM students;

-- Méthode 5 : SHOW FIELDS (identique à SHOW COLUMNS)
SHOW FIELDS FROM students;


/* ========================================
   AFFICHER LE STATUT DES TABLES
   ======================================== */

-- Afficher des informations détaillées sur toutes les tables
-- (moteur, nombre de lignes, taille, encodage, etc.)
SHOW TABLE STATUS;

-- Afficher le statut d'une table spécifique
SHOW TABLE STATUS WHERE Name = 'students';

-- Afficher toutes les tables de la base de données actuelle
SHOW TABLES;

-- Afficher les tables dont le nom commence par 'st'
SHOW TABLES LIKE 'st%';
```

### Supprimer une Table

```sql
/* ========================================
   SUPPRIMER UNE TABLE
   ======================================== */

-- ⚠️ ATTENTION : Supprime définitivement la table et toutes ses données
DROP TABLE students;

-- ✅ Bonne pratique : Vérifier si la table existe
DROP TABLE IF EXISTS students;

-- Supprimer plusieurs tables en une seule commande
DROP TABLE IF EXISTS students, teachers, courses;


/* ========================================
   VIDER UNE TABLE (SANS LA SUPPRIMER)
   ======================================== */

-- Supprimer toutes les lignes mais garder la structure
TRUNCATE TABLE students;

-- Alternative (plus lente mais plus flexible)
DELETE FROM students;
```

---

## 3️⃣ Renommer et Modifier le Moteur

```sql
/* ========================================
   RENOMMER UNE TABLE
   ======================================== */

-- Méthode 1 : RENAME TABLE (recommandée)
RENAME TABLE students TO learners;

-- Renommer plusieurs tables en une seule commande
RENAME TABLE s1 TO new1, s2 TO new2;

-- Méthode 2 : ALTER TABLE
ALTER TABLE students RENAME TO learners;


/* ========================================
   CHANGER LE MOTEUR D'UNE TABLE
   ======================================== */

-- Passer de InnoDB à MyISAM
ALTER TABLE new1 ENGINE = MyISAM;

-- Passer de MyISAM à InnoDB (recommandé)
ALTER TABLE new1 ENGINE = InnoDB;

-- 💡 Vérifier le moteur actuel
SHOW TABLE STATUS WHERE Name = 'new1';
```

---

## 4️⃣ Modifier la Structure d'une Table (ALTER)

### Ajouter des Colonnes

```sql
/* ========================================
   AJOUTER UNE NOUVELLE COLONNE
   ======================================== */

-- Ajouter une colonne 'password' à la fin de la table
ALTER TABLE students ADD password VARCHAR(255);

-- ✅ Version améliorée avec contrainte NOT NULL
ALTER TABLE students ADD password VARCHAR(255) NOT NULL;


/* ========================================
   AJOUTER UNE COLONNE À UNE POSITION SPÉCIFIQUE
   ======================================== */

-- Ajouter 'username' APRÈS la colonne 'name'
ALTER TABLE students ADD username VARCHAR(255) AFTER name;

-- Ajouter 'test' comme PREMIÈRE colonne de la table
ALTER TABLE students ADD test VARCHAR(255) FIRST;


/* ========================================
   AJOUTER PLUSIEURS COLONNES
   ======================================== */

-- Ajouter plusieurs colonnes en une seule commande
ALTER TABLE students 
ADD phone VARCHAR(20),
ADD address TEXT,
ADD city VARCHAR(100);
```

### Supprimer des Colonnes

```sql
/* ========================================
   SUPPRIMER UNE COLONNE
   ======================================== */

-- Supprimer la colonne 'last' (⚠️ données perdues définitivement)
ALTER TABLE students DROP last;

-- Supprimer plusieurs colonnes
ALTER TABLE students 
DROP test,
DROP phone;


/* ========================================
   VÉRIFIER AVANT DE SUPPRIMER
   ======================================== */

-- ✅ Bonne pratique : Vérifier la structure avant de supprimer
DESC students;

-- Ensuite supprimer la colonne
ALTER TABLE students DROP test;
```

### Modifier des Colonnes

```sql
/* ========================================
   CHANGE : Modifier le NOM et le TYPE
   ======================================== */

-- Changer le nom ET le type de la colonne 'username'
-- Syntaxe : CHANGE ancien_nom nouveau_nom nouveau_type
ALTER TABLE students CHANGE username user_name VARCHAR(100);

-- Changer uniquement le type (garder le même nom)
ALTER TABLE students CHANGE test test CHAR(50);

-- Changer le nom, le type ET la position
ALTER TABLE students CHANGE username username VARCHAR(255) AFTER email;


/* ========================================
   MODIFY : Modifier UNIQUEMENT le TYPE
   ======================================== */

-- Modifier uniquement le type de données (le nom reste identique)
-- Syntaxe : MODIFY nom_colonne nouveau_type
ALTER TABLE students MODIFY test VARCHAR(255);

-- Modifier le type et ajouter une contrainte
ALTER TABLE students MODIFY email VARCHAR(255) NOT NULL UNIQUE;

-- Modifier le type et la position
ALTER TABLE students MODIFY username VARCHAR(100) FIRST;


/* ========================================
   DIFFÉRENCE ENTRE CHANGE ET MODIFY
   ======================================== */

-- CHANGE : Peut renommer + modifier le type
ALTER TABLE students CHANGE old_name new_name VARCHAR(100);

-- MODIFY : Modifie uniquement le type (pas de renommage)
ALTER TABLE students MODIFY column_name VARCHAR(100);


/* ========================================
   MODIFIER PLUSIEURS COLONNES
   ======================================== */

-- Modifier plusieurs colonnes en une seule commande
ALTER TABLE students 
MODIFY username CHAR(50), 
CHANGE id userid INT(11);

-- Autre exemple
ALTER TABLE students 
MODIFY username VARCHAR(255), 
CHANGE userid id TINYINT(1);
```

---

## 5️⃣ Opérations Avancées

### Changer l'Encodage de Caractères

```sql
/* ========================================
   MODIFIER L'ENCODAGE DE LA TABLE
   ======================================== */

-- Convertir en UTF-8 (recommandé pour le support international)
ALTER TABLE students CONVERT TO CHARACTER SET utf8;

-- Convertir en UTF-8 MB4 (supporte les emojis 😊)
ALTER TABLE students CONVERT TO CHARACTER SET utf8mb4;

-- Convertir en Latin1 (encodage européen)
ALTER TABLE students CONVERT TO CHARACTER SET latin1;


/* ========================================
   VÉRIFIER L'ENCODAGE ACTUEL
   ======================================== */

-- Afficher l'encodage de la table
SHOW TABLE STATUS WHERE Name = 'students';

-- Afficher l'encodage de chaque colonne
SHOW FULL COLUMNS FROM students;
```

### Modifier les Valeurs par Défaut

```sql
/* ========================================
   AJOUTER/MODIFIER UNE VALEUR PAR DÉFAUT
   ======================================== */

-- Ajouter une valeur par défaut à une colonne existante
ALTER TABLE students 
MODIFY status VARCHAR(20) DEFAULT 'active';

-- Ajouter une colonne avec valeur par défaut
ALTER TABLE students 
ADD is_verified TINYINT(1) DEFAULT 0;

-- Modifier la valeur par défaut
ALTER TABLE students 
ALTER COLUMN status SET DEFAULT 'pending';


/* ========================================
   SUPPRIMER UNE VALEUR PAR DÉFAUT
   ======================================== */

-- Retirer la valeur par défaut d'une colonne
ALTER TABLE students 
ALTER COLUMN status DROP DEFAULT;
```

### Copier une Table

```sql
/* ========================================
   COPIER LA STRUCTURE D'UNE TABLE
   ======================================== */

-- Copier uniquement la structure (sans les données)
CREATE TABLE students_backup LIKE students;

-- Copier la structure ET les données
CREATE TABLE students_backup AS SELECT * FROM students;

-- Copier uniquement certaines colonnes
CREATE TABLE students_simple AS 
SELECT id, name, email FROM students;

-- Copier avec une condition
CREATE TABLE active_students AS 
SELECT * FROM students WHERE status = 'active';
```

---

## 📊 Tableau Récapitulatif des Commandes ALTER

| Commande | Utilisation | Exemple |
|----------|-------------|---------|
| **ADD** | Ajouter une colonne | `ALTER TABLE t ADD col VARCHAR(50);` |
| **DROP** | Supprimer une colonne | `ALTER TABLE t DROP col;` |
| **CHANGE** | Renommer + modifier type | `ALTER TABLE t CHANGE old new VARCHAR(50);` |
| **MODIFY** | Modifier uniquement le type | `ALTER TABLE t MODIFY col VARCHAR(100);` |
| **RENAME** | Renommer la table | `ALTER TABLE t RENAME TO new_t;` |
| **ENGINE** | Changer le moteur | `ALTER TABLE t ENGINE = InnoDB;` |

---

## 🎯 Exemples Pratiques Complets

### Exemple 1 : Créer une Table Utilisateurs Complète

```sql
-- Créer la base de données
CREATE DATABASE IF NOT EXISTS my_app;
USE my_app;

-- Créer la table users avec toutes les bonnes pratiques
CREATE TABLE users (
    -- Identifiant unique auto-incrémenté
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    
    -- Informations de base
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    
    -- Informations personnelles
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    birth_date DATE,
    
    -- Statut et rôle
    status ENUM('active', 'inactive', 'banned') DEFAULT 'active',
    role ENUM('user', 'admin', 'moderator') DEFAULT 'user',
    
    -- Métadonnées
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    last_login DATETIME
) ENGINE = InnoDB CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Vérifier la structure
DESC users;
```

### Exemple 2 : Modifier une Table Existante

```sql
-- Supposons que nous avons une table simple
CREATE TABLE products (
    id INT,
    name VARCHAR(100),
    price DECIMAL(10,2)
);

-- Améliorer la table étape par étape

-- 1. Ajouter une clé primaire
ALTER TABLE products ADD PRIMARY KEY (id);

-- 2. Rendre l'ID auto-incrémenté
ALTER TABLE products MODIFY id INT AUTO_INCREMENT;

-- 3. Ajouter des colonnes manquantes
ALTER TABLE products 
ADD description TEXT AFTER name,
ADD stock INT UNSIGNED DEFAULT 0,
ADD created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP;

-- 4. Modifier des colonnes existantes
ALTER TABLE products 
MODIFY name VARCHAR(200) NOT NULL,
MODIFY price DECIMAL(10,2) NOT NULL;

-- 5. Ajouter des contraintes
ALTER TABLE products 
ADD UNIQUE (name);

-- 6. Changer le moteur et l'encodage
ALTER TABLE products 
ENGINE = InnoDB,
CONVERT TO CHARACTER SET utf8mb4;

-- Résultat final
DESC products;
```

---

## 🧪 Exercices Pratiques

### Exercice 1 : Créer une Base de Données et des Tables

```sql
-- 1. Créer une base de données 'school'
-- 2. Créer une table 'teachers' avec :
--    - teacher_id (INT, clé primaire, auto-incrémenté)
--    - first_name (VARCHAR 100, obligatoire)
--    - last_name (VARCHAR 100, obligatoire)
--    - email (VARCHAR 255, unique)
--    - subject (VARCHAR 100)
--    - hire_date (DATE)

-- Solution :
CREATE DATABASE IF NOT EXISTS school;
USE school;

CREATE TABLE teachers (
    teacher_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE,
    subject VARCHAR(100),
    hire_date DATE
) ENGINE = InnoDB;
```

### Exercice 2 : Modifier une Table

```sql
-- Partez de cette table :
CREATE TABLE courses (
    id INT,
    title VARCHAR(100)
);

-- Effectuez les modifications suivantes :
-- 1. Ajouter une clé primaire auto-incrémentée à 'id'
-- 2. Ajouter une colonne 'description' (TEXT) après 'title'
-- 3. Ajouter une colonne 'credits' (INT) avec valeur par défaut 3
-- 4. Renommer 'title' en 'course_name'
-- 5. Ajouter une colonne 'created_at' (TIMESTAMP)

-- Solution :
ALTER TABLE courses ADD PRIMARY KEY (id);
ALTER TABLE courses MODIFY id INT AUTO_INCREMENT;
ALTER TABLE courses ADD description TEXT AFTER title;
ALTER TABLE courses ADD credits INT DEFAULT 3;
ALTER TABLE courses CHANGE title course_name VARCHAR(100);
ALTER TABLE courses ADD created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP;
```

---

## ⚠️ Erreurs Courantes à Éviter

### ❌ Erreur 1 : Oublier USE avant de créer une table

```sql
-- ❌ MAUVAIS : Pas de base sélectionnée
CREATE TABLE users (...);  -- Erreur : No database selected

-- ✅ BON : Sélectionner la base d'abord
USE my_database;
CREATE TABLE users (...);
```

### ❌ Erreur 2 : Supprimer une colonne avec des données importantes

```sql
-- ❌ MAUVAIS : Supprimer sans vérifier
ALTER TABLE users DROP email;  -- Données perdues !

-- ✅ BON : Vérifier d'abord
SELECT email FROM users LIMIT 5;  -- Vérifier les données
-- Faire une sauvegarde
CREATE TABLE users_backup AS SELECT * FROM users;
-- Ensuite supprimer
ALTER TABLE users DROP email;
```

### ❌ Erreur 3 : Utiliser CHANGE au lieu de MODIFY

```sql
-- ❌ MAUVAIS : Utiliser CHANGE pour juste modifier le type
ALTER TABLE users CHANGE email email VARCHAR(300);  -- Redondant

-- ✅ BON : Utiliser MODIFY
ALTER TABLE users MODIFY email VARCHAR(300);
```

---

## 💡 Astuces et Bonnes Pratiques

### ✅ Toujours utiliser IF EXISTS / IF NOT EXISTS

```sql
-- Évite les erreurs si la base/table existe déjà
CREATE DATABASE IF NOT EXISTS my_db;
CREATE TABLE IF NOT EXISTS users (...);
DROP TABLE IF EXISTS old_table;
```

### ✅ Utiliser des noms descriptifs

```sql
-- ❌ MAUVAIS
CREATE TABLE t1 (c1 INT, c2 VARCHAR(50));

-- ✅ BON
CREATE TABLE users (user_id INT, username VARCHAR(50));
```

### ✅ Toujours spécifier le moteur et l'encodage

```sql
CREATE TABLE users (
    ...
) ENGINE = InnoDB CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

---

## 📚 Commandes Utiles de Référence

```sql
-- Afficher toutes les bases de données
SHOW DATABASES;

-- Afficher toutes les tables
SHOW TABLES;

-- Afficher la structure d'une table
DESC table_name;

-- Afficher la commande CREATE d'une table
SHOW CREATE TABLE table_name;

-- Afficher les index d'une table
SHOW INDEXES FROM table_name;

-- Afficher le statut des tables
SHOW TABLE STATUS;
```

---

## ➡️ Prochaine Étape

Maintenant que vous maîtrisez la création et la gestion des tables, passez au **[Module 03 : Contraintes](../03-constraint)** pour apprendre à garantir l'intégrité de vos données.