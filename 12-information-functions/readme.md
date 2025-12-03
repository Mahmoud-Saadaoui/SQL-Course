# Module 12 : Fonctions d'Information

## 📚 Introduction

Les fonctions d'information permettent d'obtenir des métadonnées sur la session MySQL, la base de données, l'utilisateur et la configuration système.

---

## 1️⃣ Informations Utilisateur

```sql
/* ========================================
   USER - Informations sur l'utilisateur connecté
   ======================================== */

-- Obtenir le nom d'utilisateur actuel
SELECT USER();
-- Résultat : 'root@localhost' ou 'username@hostname'

-- SESSION_USER - Identique à USER()
SELECT SESSION_USER();

-- SYSTEM_USER - Identique à USER()
SELECT SYSTEM_USER();

-- CURRENT_USER - Utilisateur authentifié
SELECT CURRENT_USER();
```

### Différences entre les Fonctions Utilisateur

```sql
-- USER() : Utilisateur qui s'est connecté
-- CURRENT_USER() : Compte utilisé pour vérifier les privilèges

-- Exemple pratique : Audit des connexions
INSERT INTO login_logs (username, login_time)
VALUES (USER(), NOW());
```

---

## 2️⃣ Informations Système

```sql
/* ========================================
   VERSION - Version de MySQL
   ======================================== */

-- Obtenir la version de MySQL
SELECT VERSION();
-- Résultat : '8.0.35' ou '5.7.44' etc.

-- Cas d'usage : Vérifier la compatibilité
SELECT 
    VERSION() AS mysql_version,
    CASE 
        WHEN VERSION() LIKE '8.%' THEN 'MySQL 8.x'
        WHEN VERSION() LIKE '5.7%' THEN 'MySQL 5.7'
        ELSE 'Autre version'
    END AS version_category;
```

---

## 3️⃣ Informations de Base de Données

```sql
/* ========================================
   DATABASE - Base de données actuelle
   ======================================== */

-- Obtenir le nom de la base de données sélectionnée
SELECT DATABASE();
-- Résultat : 'tab' ou NULL si aucune base n'est sélectionnée

-- Cas d'usage : Vérifier la base active
SELECT 
    DATABASE() AS current_database,
    USER() AS current_user,
    NOW() AS current_time;

-- Cas d'usage : Logs avec contexte
INSERT INTO query_logs (database_name, user, query_time)
VALUES (DATABASE(), USER(), NOW());
```

---

## 4️⃣ Informations de Connexion

```sql
/* ========================================
   CONNECTION_ID - ID de connexion unique
   ======================================== */

-- Obtenir l'ID de la connexion actuelle
SELECT CONNECTION_ID();
-- Résultat : 42 (numéro unique pour cette session)

-- Cas d'usage : Identifier les sessions
SELECT 
    CONNECTION_ID() AS session_id,
    USER() AS user,
    DATABASE() AS db;

-- Cas d'usage : Tuer une connexion spécifique (admin)
-- KILL 42;  -- Termine la connexion avec l'ID 42
```

---

## 5️⃣ Informations de Charset

```sql
/* ========================================
   CHARSET - Jeu de caractères
   ======================================== */

-- Charset de l'utilisateur
SELECT CHARSET(USER());
-- Résultat : 'utf8mb4' ou 'latin1' etc.

-- Convertir et vérifier le charset
SELECT CHARSET(CONVERT(USER() USING latin1));
-- Résultat : 'latin1'

-- Avec alias
SELECT CHARSET(CONVERT(USER() USING latin1)) AS converted;

-- Cas d'usage : Vérifier les charsets des colonnes
SHOW FULL COLUMNS FROM users;
```

---

## 6️⃣ Autres Fonctions Utiles

### LAST_INSERT_ID

```sql
/* ========================================
   LAST_INSERT_ID - Dernier ID auto-incrémenté
   ======================================== */

-- Insérer une ligne et récupérer son ID
INSERT INTO users (username, email) VALUES ('john', 'john@example.com');
SELECT LAST_INSERT_ID();
-- Résultat : 15 (ID de la ligne insérée)

-- Cas d'usage : Insérer dans deux tables liées
INSERT INTO users (username) VALUES ('alice');
SET @user_id = LAST_INSERT_ID();
INSERT INTO profiles (user_id, bio) VALUES (@user_id, 'Hello!');
```

### FOUND_ROWS

```sql
/* ========================================
   FOUND_ROWS - Nombre de lignes trouvées
   ======================================== */

-- Compter les lignes sans LIMIT
SELECT SQL_CALC_FOUND_ROWS * FROM products LIMIT 10;
SELECT FOUND_ROWS();
-- Résultat : 150 (nombre total de lignes, pas juste 10)
```

### ROW_COUNT

```sql
/* ========================================
   ROW_COUNT - Lignes affectées par la dernière requête
   ======================================== */

-- Après un UPDATE
UPDATE products SET price = price * 1.1 WHERE category = 'Electronics';
SELECT ROW_COUNT();
-- Résultat : 25 (nombre de lignes mises à jour)

-- Après un DELETE
DELETE FROM logs WHERE created_at < DATE_SUB(NOW(), INTERVAL 1 YEAR);
SELECT ROW_COUNT();
-- Résultat : 1500 (nombre de lignes supprimées)
```

---

## 📊 Tableau Récapitulatif

| Fonction | Description | Exemple de Résultat |
|----------|-------------|---------------------|
| **USER()** | Utilisateur connecté | `'root@localhost'` |
| **DATABASE()** | Base de données active | `'tab'` |
| **VERSION()** | Version MySQL | `'8.0.35'` |
| **CONNECTION_ID()** | ID de session | `42` |
| **CHARSET()** | Jeu de caractères | `'utf8mb4'` |
| **LAST_INSERT_ID()** | Dernier ID inséré | `15` |
| **FOUND_ROWS()** | Lignes trouvées | `150` |
| **ROW_COUNT()** | Lignes affectées | `25` |

---

## 🎯 Exemples Pratiques

### Exemple 1 : Tableau de Bord Système

```sql
SELECT 
    USER() AS current_user,
    DATABASE() AS current_database,
    VERSION() AS mysql_version,
    CONNECTION_ID() AS session_id,
    NOW() AS current_time,
    CHARSET(DATABASE()) AS database_charset;
```

### Exemple 2 : Logs d'Audit

```sql
CREATE TABLE audit_logs (
    log_id INT PRIMARY KEY AUTO_INCREMENT,
    user VARCHAR(100),
    database_name VARCHAR(100),
    action VARCHAR(255),
    timestamp DATETIME
);

-- Enregistrer une action
INSERT INTO audit_logs (user, database_name, action, timestamp)
VALUES (USER(), DATABASE(), 'Updated product prices', NOW());
```

### Exemple 3 : Vérification de Configuration

```sql
SELECT 
    VERSION() AS version,
    CASE 
        WHEN VERSION() LIKE '8.%' THEN 'Compatible'
        ELSE 'Mise à jour recommandée'
    END AS compatibility_status,
    DATABASE() AS active_db,
    USER() AS connected_as;
```

---

## 💡 Bonnes Pratiques

```sql
-- ✅ BON : Vérifier la base de données avant des opérations critiques
SELECT DATABASE();  -- S'assurer qu'on est dans la bonne base

-- ✅ BON : Logger les actions avec contexte
INSERT INTO activity_logs (user, db, action, time)
VALUES (USER(), DATABASE(), 'Data export', NOW());

-- ✅ BON : Utiliser LAST_INSERT_ID pour les relations
INSERT INTO orders (customer_id, total) VALUES (1, 99.99);
SET @order_id = LAST_INSERT_ID();
INSERT INTO order_items (order_id, product_id, quantity)
VALUES (@order_id, 5, 2);

-- ⚠️ ATTENTION : CONNECTION_ID change à chaque connexion
-- Ne pas le stocker comme identifiant permanent
```

---

## 🧪 Exercices

```sql
-- Exercice 1 : Créer un rapport de session
SELECT 
    CONNECTION_ID() AS session,
    USER() AS user,
    DATABASE() AS db,
    VERSION() AS version,
    NOW() AS time;

-- Exercice 2 : Insérer avec audit
INSERT INTO products (name, price) VALUES ('New Product', 29.99);
INSERT INTO audit_logs (action, user, timestamp)
VALUES (CONCAT('Inserted product ID ', LAST_INSERT_ID()), USER(), NOW());

-- Exercice 3 : Vérifier les mises à jour
UPDATE products SET stock = stock + 10 WHERE category = 'Books';
SELECT CONCAT(ROW_COUNT(), ' products updated') AS result;
```

---

## ➡️ Prochaine Étape

Passez au **[Module 13 : Relations entre Tables](../13-relationship-between-tables)** pour maîtriser les jointures et les relations.