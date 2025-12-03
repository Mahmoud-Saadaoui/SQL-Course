# Module 03 : Contraintes SQL (Constraints)

## 📚 Introduction

Les contraintes SQL sont des règles appliquées aux colonnes d'une table pour garantir l'**intégrité**, la **cohérence** et la **validité** des données. Elles empêchent l'insertion de données incorrectes ou incohérentes.

---

## 1️⃣ Introduction aux Contraintes

### Qu'est-ce qu'une Contrainte ?

Une contrainte est une règle qui limite les valeurs pouvant être insérées dans une colonne. Les contraintes principales sont :

- **NOT NULL** : La colonne ne peut pas contenir de valeur NULL
- **UNIQUE** : Toutes les valeurs doivent être uniques
- **PRIMARY KEY** : Identifiant unique de chaque ligne
- **FOREIGN KEY** : Lien vers une autre table
- **DEFAULT** : Valeur par défaut si aucune n'est fournie
- **CHECK** : Condition personnalisée à respecter

```sql
/* ========================================
   AJOUTER LA CONTRAINTE NOT NULL
   ======================================== */

-- Modifier une colonne existante pour la rendre obligatoire
-- NOT NULL signifie que la colonne DOIT avoir une valeur
ALTER TABLE students MODIFY username VARCHAR(255) NOT NULL;

-- Ajouter une nouvelle colonne obligatoire
-- ⚠️ Attention : La table doit être vide OU vous devez fournir une valeur par défaut
ALTER TABLE students ADD osama VARCHAR(255) NOT NULL;

-- ✅ Meilleure approche : Ajouter avec une valeur par défaut
ALTER TABLE students ADD osama VARCHAR(255) NOT NULL DEFAULT 'default_value';
```

---

## 2️⃣ NOT NULL et UNIQUE

### NOT NULL - Valeur Obligatoire

```sql
/* ========================================
   CONTRAINTE NOT NULL
   ======================================== */

-- NOT NULL force l'utilisateur à fournir une valeur
-- La colonne ne peut PAS être vide (NULL)

-- Exemple : Créer une table avec NOT NULL
CREATE TABLE users (
    id INT NOT NULL,
    username VARCHAR(50) NOT NULL,    -- Obligatoire
    email VARCHAR(255) NOT NULL,      -- Obligatoire
    bio TEXT                          -- Optionnel (peut être NULL)
);

-- Tester l'insertion
INSERT INTO users (id, username, email) VALUES (1, 'john', 'john@email.com');  -- ✅ OK
INSERT INTO users (id, username) VALUES (2, 'jane');  -- ❌ ERREUR : email est NOT NULL
INSERT INTO users (id, email) VALUES (3, 'test@email.com');  -- ❌ ERREUR : username est NOT NULL
```

### UNIQUE - Valeur Unique

```sql
/* ========================================
   CONTRAINTE UNIQUE
   ======================================== */

-- UNIQUE garantit que toutes les valeurs de la colonne sont différentes
-- Aucune duplication n'est autorisée

-- Ajouter UNIQUE à une colonne existante
ALTER TABLE students ADD UNIQUE(username);

-- Ajouter UNIQUE à plusieurs colonnes (combinaison unique)
ALTER TABLE students ADD UNIQUE(email, phone);

-- Créer une table avec UNIQUE
CREATE TABLE products (
    id INT PRIMARY KEY,
    sku VARCHAR(50) UNIQUE,           -- Code produit unique
    barcode VARCHAR(100) UNIQUE,      -- Code-barres unique
    name VARCHAR(200)
);

-- Tester l'insertion
INSERT INTO products VALUES (1, 'SKU001', '123456', 'Product A');  -- ✅ OK
INSERT INTO products VALUES (2, 'SKU001', '789012', 'Product B');  -- ❌ ERREUR : SKU dupliqué
```

### Supprimer une Contrainte UNIQUE

```sql
/* ========================================
   SUPPRIMER UN INDEX UNIQUE
   ======================================== */

-- UNIQUE crée automatiquement un index
-- Pour le supprimer, on supprime l'index

-- Supprimer l'index UNIQUE de la colonne 'osama'
ALTER TABLE students DROP INDEX osama;

-- Vérifier les index existants
SHOW INDEXES FROM students;
```

### Combiner NOT NULL et UNIQUE

```sql
/* ========================================
   NOT NULL + UNIQUE
   ======================================== */

-- Ajouter une colonne qui doit être obligatoire ET unique
ALTER TABLE students ADD test VARCHAR(255) NOT NULL UNIQUE;

-- Exemple pratique : Email unique et obligatoire
CREATE TABLE members (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) NOT NULL UNIQUE,  -- Email obligatoire et unique
    username VARCHAR(50) NOT NULL UNIQUE -- Username obligatoire et unique
);
```

---

## 3️⃣ PRIMARY KEY - Clé Primaire

### Qu'est-ce qu'une PRIMARY KEY ?

Une **PRIMARY KEY** (clé primaire) est une contrainte qui :
- Identifie **UNIQUEMENT** chaque ligne de la table
- Combine automatiquement **NOT NULL** et **UNIQUE**
- Il ne peut y avoir qu'**UNE SEULE** clé primaire par table
- Est souvent utilisée avec **AUTO_INCREMENT**

```sql
/* ========================================
   CRÉER UNE TABLE AVEC PRIMARY KEY
   ======================================== */

-- Méthode 1 : PRIMARY KEY directement sur la colonne
CREATE TABLE classes (
    cid INT NOT NULL PRIMARY KEY,     -- Clé primaire
    name VARCHAR(255) UNIQUE
);

-- Méthode 2 : PRIMARY KEY à la fin de la définition
CREATE TABLE teachers (
    tid INT NOT NULL,
    name VARCHAR(255),
    PRIMARY KEY(tid)                  -- Définir la clé primaire ici
);

-- Méthode 3 : PRIMARY KEY avec AUTO_INCREMENT (recommandé)
CREATE TABLE students (
    id INT PRIMARY KEY AUTO_INCREMENT,  -- S'incrémente automatiquement
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE
);


/* ========================================
   AJOUTER UNE PRIMARY KEY À UNE TABLE EXISTANTE
   ======================================== */

-- Ajouter une clé primaire à la colonne 'id'
ALTER TABLE students ADD PRIMARY KEY(id);

-- Ajouter une clé primaire composite (plusieurs colonnes)
ALTER TABLE enrollments ADD PRIMARY KEY(student_id, course_id);


/* ========================================
   SUPPRIMER UNE PRIMARY KEY
   ======================================== */

-- ⚠️ Attention : Cela supprime la contrainte de clé primaire
ALTER TABLE students DROP PRIMARY KEY;

-- Si la colonne a AUTO_INCREMENT, il faut d'abord le retirer
ALTER TABLE students MODIFY id INT;           -- Retirer AUTO_INCREMENT
ALTER TABLE students DROP PRIMARY KEY;         -- Supprimer la clé primaire


/* ========================================
   AFFICHER LES INDEX (Y COMPRIS PRIMARY KEY)
   ======================================== */

-- Afficher tous les index de la table (PRIMARY KEY apparaît comme index)
SHOW INDEXES FROM students;
```

### Exemples Pratiques

```sql
-- Exemple 1 : Table utilisateurs avec ID auto-incrémenté
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(255) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insertion automatique de l'ID
INSERT INTO users (username, email) VALUES ('alice', 'alice@email.com');
INSERT INTO users (username, email) VALUES ('bob', 'bob@email.com');
-- Les IDs seront 1, 2, 3, etc. automatiquement


-- Exemple 2 : Clé primaire composite (plusieurs colonnes)
CREATE TABLE course_enrollments (
    student_id INT NOT NULL,
    course_id INT NOT NULL,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id)  -- Combinaison unique
);

-- Un étudiant ne peut s'inscrire qu'une seule fois à un cours
INSERT INTO course_enrollments VALUES (1, 101, '2024-01-15');  -- ✅ OK
INSERT INTO course_enrollments VALUES (1, 101, '2024-02-20');  -- ❌ ERREUR : Duplication
INSERT INTO course_enrollments VALUES (1, 102, '2024-01-15');  -- ✅ OK (cours différent)
```

---

## 4️⃣ FOREIGN KEY - Clé Étrangère

### Qu'est-ce qu'une FOREIGN KEY ?

Une **FOREIGN KEY** (clé étrangère) est une contrainte qui :
- Crée un **lien** entre deux tables
- Garantit l'**intégrité référentielle** (les données restent cohérentes)
- Empêche la suppression de données référencées ailleurs
- Nécessite le moteur **InnoDB**

```sql
/* ========================================
   CRÉER DES TABLES AVEC FOREIGN KEY
   ======================================== */

-- Table parente (clients)
CREATE TABLE clients (
    id INT NOT NULL,
    username VARCHAR(255) UNIQUE,
    email VARCHAR(255) UNIQUE,
    PRIMARY KEY(id)
) ENGINE = InnoDB;  -- ⚠️ InnoDB est OBLIGATOIRE pour les FOREIGN KEY

-- Table enfant (orders) avec clé étrangère
CREATE TABLE orders (
    order_id INT NOT NULL,
    price DECIMAL(10,2),
    client_id INT NOT NULL,              -- Référence vers clients.id
    PRIMARY KEY(order_id),
    FOREIGN KEY(client_id) REFERENCES clients(id)  -- Clé étrangère
) ENGINE = InnoDB;


/* ========================================
   TESTER LA FOREIGN KEY
   ======================================== */

-- Insérer un client
INSERT INTO clients VALUES (1, 'john_doe', 'john@email.com');

-- Insérer une commande pour ce client
INSERT INTO orders VALUES (101, 99.99, 1);  -- ✅ OK (client_id 1 existe)

-- Essayer d'insérer une commande pour un client inexistant
INSERT INTO orders VALUES (102, 49.99, 999);  -- ❌ ERREUR : client_id 999 n'existe pas

-- Essayer de supprimer un client qui a des commandes
DELETE FROM clients WHERE id = 1;  -- ❌ ERREUR : Des commandes référencent ce client
```

### CASCADE - Synchronisation Automatique

```sql
/* ========================================
   ON UPDATE CASCADE et ON DELETE CASCADE
   ======================================== */

-- CASCADE permet de synchroniser automatiquement les modifications

-- Créer la table avec CASCADE
CREATE TABLE orders (
    order_id INT NOT NULL,
    price DECIMAL(10,2),
    client_id INT NOT NULL,
    PRIMARY KEY(order_id),
    FOREIGN KEY(client_id) REFERENCES clients(id)
        ON UPDATE CASCADE  -- Si l'ID du client change, mettre à jour automatiquement
        ON DELETE CASCADE  -- Si le client est supprimé, supprimer ses commandes
) ENGINE = InnoDB;


-- Ajouter CASCADE à une table existante
ALTER TABLE orders
ADD CONSTRAINT fk_client_orders
FOREIGN KEY(client_id) REFERENCES clients(id)
ON UPDATE CASCADE
ON DELETE CASCADE;


/* ========================================
   TESTER CASCADE
   ======================================== */

-- Données de test
INSERT INTO clients VALUES (1, 'alice', 'alice@email.com');
INSERT INTO orders VALUES (101, 50.00, 1);
INSERT INTO orders VALUES (102, 75.00, 1);

-- Mettre à jour l'ID du client (ON UPDATE CASCADE)
UPDATE clients SET id = 10 WHERE id = 1;
-- Les commandes sont automatiquement mises à jour : client_id passe de 1 à 10

-- Supprimer le client (ON DELETE CASCADE)
DELETE FROM clients WHERE id = 10;
-- Toutes les commandes de ce client sont automatiquement supprimées
```

### Options de FOREIGN KEY

```sql
/* ========================================
   DIFFÉRENTES OPTIONS DE FOREIGN KEY
   ======================================== */

-- 1. CASCADE : Propager les modifications/suppressions
FOREIGN KEY(client_id) REFERENCES clients(id)
ON UPDATE CASCADE
ON DELETE CASCADE;

-- 2. SET NULL : Mettre à NULL si le parent est supprimé
FOREIGN KEY(client_id) REFERENCES clients(id)
ON DELETE SET NULL;

-- 3. RESTRICT : Empêcher la suppression (comportement par défaut)
FOREIGN KEY(client_id) REFERENCES clients(id)
ON DELETE RESTRICT;

-- 4. NO ACTION : Identique à RESTRICT
FOREIGN KEY(client_id) REFERENCES clients(id)
ON DELETE NO ACTION;

-- 5. SET DEFAULT : Mettre une valeur par défaut (non supporté par MySQL)
```

### Types de Relations

```sql
/* ========================================
   TYPES DE RELATIONS AVEC FOREIGN KEY
   ======================================== */

-- 1. ONE-TO-ONE (Un à Un)
-- Exemple : Un utilisateur a UN SEUL profil
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50)
) ENGINE = InnoDB;

CREATE TABLE profiles (
    profile_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT UNIQUE,  -- UNIQUE garantit la relation 1:1
    bio TEXT,
    FOREIGN KEY(user_id) REFERENCES users(user_id)
) ENGINE = InnoDB;


-- 2. ONE-TO-MANY (Un à Plusieurs)
-- Exemple : Un client a PLUSIEURS commandes
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100)
) ENGINE = InnoDB;

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,  -- Pas UNIQUE : un client peut avoir plusieurs commandes
    total DECIMAL(10,2),
    FOREIGN KEY(customer_id) REFERENCES customers(customer_id)
) ENGINE = InnoDB;


-- 3. MANY-TO-MANY (Plusieurs à Plusieurs)
-- Exemple : Des étudiants suivent PLUSIEURS cours, et chaque cours a PLUSIEURS étudiants
-- Nécessite une table de jonction

CREATE TABLE students (
    student_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100)
) ENGINE = InnoDB;

CREATE TABLE courses (
    course_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(200)
) ENGINE = InnoDB;

-- Table de jonction
CREATE TABLE enrollments (
    enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    FOREIGN KEY(student_id) REFERENCES students(student_id),
    FOREIGN KEY(course_id) REFERENCES courses(course_id),
    UNIQUE(student_id, course_id)  -- Un étudiant ne peut s'inscrire qu'une fois par cours
) ENGINE = InnoDB;
```

---

## 📊 Tableau Récapitulatif des Contraintes

| Contrainte | Description | Exemple |
|------------|-------------|---------|
| **NOT NULL** | Valeur obligatoire | `name VARCHAR(100) NOT NULL` |
| **UNIQUE** | Valeur unique | `email VARCHAR(255) UNIQUE` |
| **PRIMARY KEY** | Identifiant unique (NOT NULL + UNIQUE) | `id INT PRIMARY KEY` |
| **FOREIGN KEY** | Lien vers une autre table | `FOREIGN KEY(user_id) REFERENCES users(id)` |
| **DEFAULT** | Valeur par défaut | `status VARCHAR(20) DEFAULT 'active'` |
| **CHECK** | Condition personnalisée | `age INT CHECK(age >= 18)` |
| **AUTO_INCREMENT** | Incrémentation automatique | `id INT AUTO_INCREMENT` |

---

## 🎯 Exemples Complets

### Exemple 1 : Système de Blog

```sql
-- Table des auteurs
CREATE TABLE authors (
    author_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE = InnoDB;

-- Table des articles
CREATE TABLE posts (
    post_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(200) NOT NULL,
    content LONGTEXT NOT NULL,
    author_id INT NOT NULL,
    status ENUM('draft', 'published', 'archived') DEFAULT 'draft',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY(author_id) REFERENCES authors(author_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
) ENGINE = InnoDB;

-- Table des commentaires
CREATE TABLE comments (
    comment_id INT PRIMARY KEY AUTO_INCREMENT,
    post_id INT NOT NULL,
    author_name VARCHAR(100) NOT NULL,
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY(post_id) REFERENCES posts(post_id)
        ON DELETE CASCADE
) ENGINE = InnoDB;
```

### Exemple 2 : Système E-commerce

```sql
-- Table des catégories
CREATE TABLE categories (
    category_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL UNIQUE,
    description TEXT
) ENGINE = InnoDB;

-- Table des produits
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(200) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL CHECK(price > 0),
    stock INT UNSIGNED DEFAULT 0,
    category_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY(category_id) REFERENCES categories(category_id)
        ON DELETE SET NULL
) ENGINE = InnoDB;

-- Table des clients
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE = InnoDB;

-- Table des commandes
CREATE TABLE customer_orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    total DECIMAL(10,2) NOT NULL,
    status ENUM('pending', 'processing', 'shipped', 'delivered') DEFAULT 'pending',
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY(customer_id) REFERENCES customers(customer_id)
) ENGINE = InnoDB;

-- Table des détails de commande (Many-to-Many)
CREATE TABLE order_items (
    item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL CHECK(quantity > 0),
    price DECIMAL(10,2) NOT NULL,
    FOREIGN KEY(order_id) REFERENCES customer_orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY(product_id) REFERENCES products(product_id)
) ENGINE = InnoDB;
```

---

## 🧪 Exercices Pratiques

### Exercice 1 : Créer un Système de Bibliothèque

```sql
-- Créez les tables suivantes avec les contraintes appropriées :
-- 1. authors (author_id, name, country)
-- 2. books (book_id, title, isbn, author_id, publication_year)
-- 3. members (member_id, name, email, join_date)
-- 4. loans (loan_id, book_id, member_id, loan_date, return_date)

-- Solution :
CREATE TABLE authors (
    author_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    country VARCHAR(100)
) ENGINE = InnoDB;

CREATE TABLE books (
    book_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(200) NOT NULL,
    isbn VARCHAR(13) UNIQUE NOT NULL,
    author_id INT NOT NULL,
    publication_year YEAR,
    FOREIGN KEY(author_id) REFERENCES authors(author_id)
) ENGINE = InnoDB;

CREATE TABLE members (
    member_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    join_date DATE DEFAULT (CURRENT_DATE)
) ENGINE = InnoDB;

CREATE TABLE loans (
    loan_id INT PRIMARY KEY AUTO_INCREMENT,
    book_id INT NOT NULL,
    member_id INT NOT NULL,
    loan_date DATE NOT NULL,
    return_date DATE,
    FOREIGN KEY(book_id) REFERENCES books(book_id),
    FOREIGN KEY(member_id) REFERENCES members(member_id)
) ENGINE = InnoDB;
```

---

## ⚠️ Erreurs Courantes

### ❌ Erreur 1 : Oublier ENGINE = InnoDB

```sql
-- ❌ MAUVAIS : MyISAM ne supporte pas les FOREIGN KEY
CREATE TABLE orders (
    ...
    FOREIGN KEY(client_id) REFERENCES clients(id)
) ENGINE = MyISAM;  -- ERREUR !

-- ✅ BON
) ENGINE = InnoDB;
```

### ❌ Erreur 2 : Types de données incompatibles

```sql
-- ❌ MAUVAIS : Types différents
CREATE TABLE clients (id INT PRIMARY KEY);
CREATE TABLE orders (client_id BIGINT, FOREIGN KEY(client_id) REFERENCES clients(id));

-- ✅ BON : Même type
CREATE TABLE clients (id INT PRIMARY KEY);
CREATE TABLE orders (client_id INT, FOREIGN KEY(client_id) REFERENCES clients(id));
```

---

## 💡 Bonnes Pratiques

1. ✅ Toujours utiliser **InnoDB** pour les tables avec FOREIGN KEY
2. ✅ Nommer explicitement les contraintes : `CONSTRAINT fk_name FOREIGN KEY...`
3. ✅ Utiliser **CASCADE** avec précaution (risque de suppressions en cascade)
4. ✅ Toujours définir une **PRIMARY KEY** pour chaque table
5. ✅ Utiliser **AUTO_INCREMENT** pour les clés primaires numériques

---

## ➡️ Prochaine Étape

Maintenant que vous maîtrisez les contraintes, passez au **[Module 04 : Fonctions de Chaînes](../04-string-functions)** pour apprendre à manipuler du texte en SQL.