# Module 04 : Fonctions de Chaînes de Caractères

## 📚 Introduction

Les fonctions de chaînes permettent de manipuler, transformer et analyser du texte dans vos requêtes SQL. Ces fonctions sont essentielles pour le traitement de données textuelles.

---

## 1️⃣ LEFT, RIGHT et MID - Extraction de Sous-Chaînes

### LEFT - Extraire depuis la Gauche

```sql
/* ========================================
   LEFT - Extraire les N premiers caractères
   Syntaxe : LEFT(chaîne, longueur)
   ======================================== */

-- Extraire les 3 premiers caractères
SELECT LEFT('Hello World', 3);  -- Résultat : 'Hel'

-- Exemple avec une table
SELECT text, LEFT(text, 3) AS first_three FROM `try`;

-- Cas d'usage : Extraire le code pays d'un numéro de téléphone
SELECT phone, LEFT(phone, 3) AS country_code FROM users;
-- Exemple : '+33612345678' → '+33'
```

### RIGHT - Extraire depuis la Droite

```sql
/* ========================================
   RIGHT - Extraire les N derniers caractères
   Syntaxe : RIGHT(chaîne, longueur)
   ======================================== */

-- Extraire les 3 derniers caractères
SELECT RIGHT('Hello World', 3);  -- Résultat : 'rld'

-- Exemple avec une table
SELECT text, RIGHT(text, 3) AS last_three FROM `try`;

-- Cas d'usage : Extraire l'extension d'un fichier
SELECT filename, RIGHT(filename, 4) AS extension FROM files;
-- Exemple : 'document.pdf' → '.pdf'
```

### MID (SUBSTRING) - Extraire du Milieu

```sql
/* ========================================
   MID - Extraire une sous-chaîne depuis une position
   Syntaxe : MID(chaîne, position_départ, longueur)
   Note : La position commence à 1 (pas 0)
   ======================================== */

-- Extraire 3 caractères à partir de la position 2
SELECT MID('Hello World', 2, 3);  -- Résultat : 'ell'

-- Exemple avec une table
SELECT text, MID(text, 2, 3) AS middle_part FROM `try`;

-- SUBSTRING est identique à MID
SELECT SUBSTRING('Hello World', 2, 3);  -- Résultat : 'ell'

-- Cas d'usage : Extraire le mois d'une date au format texte
SELECT date_str, MID(date_str, 6, 2) AS month FROM dates;
-- Exemple : '2024-12-03' → '12'
```

### 💡 Exemple Pratique Complet

```sql
-- Extraire différentes parties d'un email
SELECT 
    email,
    LEFT(email, LOCATE('@', email) - 1) AS username,
    RIGHT(email, LENGTH(email) - LOCATE('@', email)) AS domain
FROM users;
-- Exemple : 'john@example.com' → username: 'john', domain: 'example.com'
```

---

## 2️⃣ LENGTH et CHAR_LENGTH - Longueur de Chaîne

### LENGTH - Longueur en Octets

```sql
/* ========================================
   LENGTH - Retourne la longueur en OCTETS
   Syntaxe : LENGTH(chaîne)
   ⚠️ Attention : Compte les octets, pas les caractères
   ======================================== */

-- Longueur simple
SELECT LENGTH('Hello');  -- Résultat : 5

-- Avec des caractères spéciaux (UTF-8)
SELECT LENGTH('Café');   -- Résultat : 5 (é = 2 octets)

-- Exemple avec une table
SELECT text, LENGTH(text) FROM `try`;

-- Avec alias
SELECT text, LENGTH(text) AS byte_count FROM `try`;
```

### CHAR_LENGTH - Longueur en Caractères

```sql
/* ========================================
   CHAR_LENGTH - Retourne la longueur en CARACTÈRES
   Syntaxe : CHAR_LENGTH(chaîne)
   ✅ Recommandé : Compte les caractères réels
   ======================================== */

-- Longueur en caractères
SELECT CHAR_LENGTH('Café');  -- Résultat : 4 (4 caractères)

-- Comparaison LENGTH vs CHAR_LENGTH
SELECT 
    text,
    LENGTH(text) AS bytes,
    CHAR_LENGTH(text) AS characters
FROM `try`;

-- Cas d'usage : Valider la longueur d'un username
SELECT username FROM users WHERE CHAR_LENGTH(username) >= 3;
```

### 📊 Différence LENGTH vs CHAR_LENGTH

```sql
-- Exemple avec emojis et caractères spéciaux
SELECT 
    'Hello 😊' AS text,
    LENGTH('Hello 😊') AS bytes,        -- 10 octets
    CHAR_LENGTH('Hello 😊') AS chars;   -- 7 caractères
```

---

## 3️⃣ UPPER et LOWER - Changement de Casse

### LOWER / LCASE - Convertir en Minuscules

```sql
/* ========================================
   LOWER - Convertir en minuscules
   Syntaxe : LOWER(chaîne) ou LCASE(chaîne)
   ======================================== */

-- Convertir en minuscules
SELECT LOWER('HELLO WORLD');  -- Résultat : 'hello world'

-- LCASE est identique
SELECT LCASE('HELLO WORLD');  -- Résultat : 'hello world'

-- Exemple avec une table
SELECT text, LOWER(text) AS lowercase FROM `try`;

-- Cas d'usage : Recherche insensible à la casse
SELECT * FROM users WHERE LOWER(email) = LOWER('John@Example.COM');
```

### UPPER / UCASE - Convertir en Majuscules

```sql
/* ========================================
   UPPER - Convertir en majuscules
   Syntaxe : UPPER(chaîne) ou UCASE(chaîne)
   ======================================== */

-- Convertir en majuscules
SELECT UPPER('hello world');  -- Résultat : 'HELLO WORLD'

-- UCASE est identique
SELECT UCASE('hello world');  -- Résultat : 'HELLO WORLD'

-- Exemple avec une table
SELECT text, UPPER(text) AS uppercase FROM `try`;

-- Cas d'usage : Normaliser les codes pays
UPDATE countries SET code = UPPER(code);
-- 'fr' → 'FR', 'us' → 'US'
```

### Combiner avec ORDER BY

```sql
/* ========================================
   TRIER PAR LONGUEUR
   ======================================== */

-- Trier par longueur croissante
SELECT text, CHAR_LENGTH(text) AS count 
FROM `try` 
ORDER BY CHAR_LENGTH(text) ASC;

-- Trier par longueur décroissante
SELECT text, CHAR_LENGTH(text) AS count 
FROM `try` 
ORDER BY CHAR_LENGTH(text) DESC;

-- Filtrer par longueur
SELECT text FROM `try` WHERE CHAR_LENGTH(text) > 10;
```

---

## 4️⃣ REPEAT, REVERSE et REPLACE - Transformations

### REPEAT - Répéter une Chaîne

```sql
/* ========================================
   REPEAT - Répéter une chaîne N fois
   Syntaxe : REPEAT(chaîne, nombre)
   ======================================== */

-- Répéter 3 fois
SELECT REPEAT('SQL ', 3);  -- Résultat : 'SQL SQL SQL '

-- Exemple avec une table
SELECT text, REPEAT(text, 3) AS repeated FROM `try`;

-- Cas d'usage : Créer des séparateurs
SELECT REPEAT('-', 50) AS separator;
-- Résultat : '--------------------------------------------------'

-- Créer un masque pour les mots de passe
SELECT username, REPEAT('*', CHAR_LENGTH(password)) AS masked_password 
FROM users;
```

### REPLACE - Remplacer du Texte

```sql
/* ========================================
   REPLACE - Remplacer une sous-chaîne
   Syntaxe : REPLACE(chaîne, recherche, remplacement)
   ======================================== */

-- Remplacer 'a' par '@'
SELECT REPLACE('banana', 'a', '@');  -- Résultat : 'b@n@n@'

-- Exemple avec une table
SELECT text, REPLACE(text, 'a', '@') AS replaced FROM `try`;

-- Cas d'usage 1 : Mettre à jour HTTP vers HTTPS
SELECT text, REPLACE(text, 'http', 'https') AS replaced FROM `try`;

UPDATE `try` SET text = REPLACE(text, 'http', 'https');

-- Cas d'usage 2 : Nettoyer les URLs
UPDATE `try` SET text = REPLACE(text, 'https://', '');

-- Cas d'usage 3 : Remplacer les espaces par des underscores
SELECT REPLACE('Hello World', ' ', '_');  -- Résultat : 'Hello_World'

-- Remplacements multiples (chaînés)
SELECT 
    REPLACE(
        REPLACE(
            REPLACE(text, 'http://', ''),
            'https://', ''
        ),
        'www.', ''
    ) AS clean_url
FROM websites;
```

### REVERSE - Inverser une Chaîne

```sql
/* ========================================
   REVERSE - Inverser l'ordre des caractères
   Syntaxe : REVERSE(chaîne)
   ======================================== */

-- Inverser une chaîne
SELECT REVERSE('Hello');  -- Résultat : 'olleH'

-- Exemple avec une table
SELECT text, REVERSE(text) AS reversed FROM `try`;

-- Mettre à jour la table
UPDATE `try` SET text = REVERSE(text);

-- Cas d'usage : Vérifier les palindromes
SELECT 
    word,
    CASE 
        WHEN word = REVERSE(word) THEN 'Palindrome'
        ELSE 'Not a palindrome'
    END AS is_palindrome
FROM words;
-- 'radar' → 'Palindrome'
-- 'hello' → 'Not a palindrome'
```

---

## 5️⃣ CONCAT et CONCAT_WS - Concaténation

### CONCAT - Concaténer des Chaînes

```sql
/* ========================================
   CONCAT - Joindre plusieurs chaînes
   Syntaxe : CONCAT(chaîne1, chaîne2, ...)
   ======================================== */

-- Concaténer simplement
SELECT CONCAT('Hello', ' ', 'World');  -- Résultat : 'Hello World'

-- Avec une table
SELECT id, text, CONCAT(text, ' Added By Me') AS mytext FROM `try`;

-- Ajouter du texte avant et après
SELECT id, text, CONCAT('First ', text, ' Last') AS mytext FROM `try`;

-- Combiner plusieurs colonnes
SELECT 
    id, 
    text, 
    CONCAT('This Country Is ', text, ' And Its ID Is ', id) AS mytextwithid
FROM `try`;

-- Cas d'usage : Créer un nom complet
SELECT 
    CONCAT(first_name, ' ', last_name) AS full_name
FROM users;

-- Avec NULL : ⚠️ Si une valeur est NULL, le résultat est NULL
SELECT CONCAT('Hello', NULL, 'World');  -- Résultat : NULL
```

### CONCAT_WS - Concaténer avec Séparateur

```sql
/* ========================================
   CONCAT_WS - Concaténer avec un séparateur
   Syntaxe : CONCAT_WS(séparateur, chaîne1, chaîne2, ...)
   ✅ Avantage : Ignore les valeurs NULL
   ======================================== */

-- Avec virgule comme séparateur
SELECT CONCAT_WS(',', 'Apple', 'Banana', 'Orange');
-- Résultat : 'Apple,Banana,Orange'

-- Exemple avec une table
SELECT id, text, CONCAT_WS(',', text, 'Added') AS mytextwithid FROM `try`;

-- Avec tiret
SELECT id, text, CONCAT_WS('-', text, 'Added') AS mytextwithid FROM `try`;

-- Combiner ID et texte
SELECT id, text, CONCAT_WS(',', id, text) AS mytextwithid FROM `try`;

-- Avec espaces
SELECT id, text, CONCAT_WS(',', id, ' ', text) AS mytextwithid FROM `try`;

-- Combiner avec d'autres fonctions
SELECT id, text, CONCAT_WS(',', CONCAT(id, ' '), text) AS mytextwithid FROM `try`;

SELECT id, text, CONCAT_WS(',', id, REPEAT(text, 2)) AS mytextwithid FROM `try`;

-- Cas d'usage : Créer une adresse complète
SELECT 
    CONCAT_WS(', ', street, city, postal_code, country) AS full_address
FROM addresses;

-- Avec NULL : ✅ Les NULL sont ignorés
SELECT CONCAT_WS(',', 'Apple', NULL, 'Orange');  -- Résultat : 'Apple,Orange'
```

### Combinaisons Avancées

```sql
-- Combiner CONCAT, REVERSE et autres fonctions
SELECT id, text, REVERSE(CONCAT(id, text)) AS mytextwithid FROM `try`;

SELECT id, text, REVERSE(CONCAT(id, text, 'Osama')) AS mytextwithid FROM `try`;

-- Créer un slug pour URL
SELECT 
    title,
    LOWER(REPLACE(REPLACE(title, ' ', '-'), ',', '')) AS slug
FROM articles;
-- 'Hello, World' → 'hello-world'
```

---

## 6️⃣ INSERT - Insérer dans une Chaîne

```sql
/* ========================================
   INSERT - Insérer une chaîne à une position
   Syntaxe : INSERT(chaîne, position, longueur_à_remplacer, nouvelle_chaîne)
   ======================================== */

-- Remplacer 2 caractères à partir de la position 3 par 'Osama'
SELECT INSERT('Hello World', 3, 2, 'Osama');
-- Résultat : 'HeOsamao World'

-- Exemple avec une table
SELECT text, INSERT(text, 3, 2, 'Osama') AS newString FROM `try`;

-- Mettre à jour une table
UPDATE `try` SET text = INSERT(text, 4, 6, 'Serial');

-- Remplacer par un seul caractère
UPDATE `try` SET text = INSERT(text, 6, 4, '#');

-- Insérer l'ID dans le texte
UPDATE `try` SET text = INSERT(text, 7, 1, id);

-- Cas d'usage : Masquer une partie d'un numéro de carte
SELECT 
    card_number,
    INSERT(card_number, 5, 8, '********') AS masked_card
FROM payments;
-- '1234567890123456' → '1234********3456'
```

---

## 7️⃣ TRIM, LTRIM et RTRIM - Supprimer les Espaces

### TRIM - Supprimer les Espaces

```sql
/* ========================================
   TRIM - Supprimer les espaces (ou autres caractères)
   Syntaxe : TRIM([LEADING | TRAILING | BOTH] [caractère] FROM chaîne)
   ======================================== */

-- Supprimer les espaces des deux côtés (par défaut)
SELECT TRIM('   Hello World   ');  -- Résultat : 'Hello World'

-- Exemple avec une table
SELECT text, TRIM(text) AS textWithoutSpace FROM `try`;

-- LEADING : Supprimer uniquement au début
SELECT TRIM(LEADING FROM '   Hello   ');  -- Résultat : 'Hello   '

-- TRAILING : Supprimer uniquement à la fin
SELECT TRIM(TRAILING FROM '   Hello   ');  -- Résultat : '   Hello'

-- BOTH : Supprimer des deux côtés (par défaut)
SELECT TRIM(BOTH FROM '   Hello   ');  -- Résultat : 'Hello'

-- Avec longueur
SELECT 
    text, 
    TRIM(BOTH FROM text) AS textWithoutSpace,
    CHAR_LENGTH(TRIM(BOTH FROM text)) AS BothTrim,
    CHAR_LENGTH(text) AS textLengthWithoutTrim
FROM `try`;
```

### TRIM avec Caractères Personnalisés

```sql
/* ========================================
   TRIM - Supprimer des caractères spécifiques
   ======================================== */

-- Supprimer '@' au début
SELECT TRIM(LEADING '@' FROM '@@Hello@@');  -- Résultat : 'Hello@@'

-- Supprimer '@' à la fin
SELECT TRIM(TRAILING '@' FROM '@@Hello@@');  -- Résultat : '@@Hello'

-- Supprimer '@' des deux côtés
SELECT TRIM(BOTH '@' FROM '@@Hello@@');  -- Résultat : 'Hello'

-- Cas d'usage : Nettoyer les hashtags
SELECT TRIM(BOTH '#' FROM '##sql##') AS clean_tag;  -- Résultat : 'sql'
```

### LTRIM et RTRIM

```sql
/* ========================================
   LTRIM - Supprimer les espaces à gauche
   RTRIM - Supprimer les espaces à droite
   ======================================== */

-- LTRIM : Left Trim (gauche)
SELECT LTRIM('   Hello   ');  -- Résultat : 'Hello   '

-- RTRIM : Right Trim (droite)
SELECT RTRIM('   Hello   ');  -- Résultat : '   Hello'

-- Exemple avec une table
SELECT text, LTRIM(text) AS textWithTrim FROM `try`;
SELECT text, RTRIM(text) AS textWithTrim FROM `try`;

-- Combiner les deux
SELECT LTRIM(RTRIM('   Hello   '));  -- Résultat : 'Hello'
-- Équivalent à TRIM(BOTH FROM '   Hello   ')
```

---

## 8️⃣ LPAD et RPAD - Remplissage

```sql
/* ========================================
   LPAD - Remplir à gauche
   Syntaxe : LPAD(chaîne, longueur_totale, caractère_de_remplissage)
   ======================================== */

-- Remplir à gauche pour atteindre 10 caractères
SELECT LPAD('SQL', 10, '*');  -- Résultat : '*******SQL'

-- Exemple avec une table
SELECT text AS MainText, LPAD(text, 10, '@') AS TextWithPadded FROM `try`;

-- Cas d'usage : Formater des IDs avec des zéros
SELECT id, LPAD(id, 5, '0') AS formatted_id FROM products;
-- 1 → '00001'
-- 42 → '00042'
-- 999 → '00999'


/* ========================================
   RPAD - Remplir à droite
   Syntaxe : RPAD(chaîne, longueur_totale, caractère_de_remplissage)
   ======================================== */

-- Remplir à droite pour atteindre 10 caractères
SELECT RPAD('SQL', 10, '*');  -- Résultat : 'SQL*******'

-- Exemple avec une table
SELECT text AS MainText, RPAD(text, 10, '@') AS TextWithPadded FROM `try`;

-- Cas d'usage : Créer des colonnes alignées
SELECT 
    RPAD(name, 20, ' ') AS name_column,
    LPAD(price, 10, ' ') AS price_column
FROM products;
```

---

## 📊 Tableau Récapitulatif des Fonctions

| Fonction | Description | Exemple |
|----------|-------------|---------|
| **LEFT** | Extraire N caractères à gauche | `LEFT('Hello', 3)` → 'Hel' |
| **RIGHT** | Extraire N caractères à droite | `RIGHT('Hello', 3)` → 'llo' |
| **MID** | Extraire du milieu | `MID('Hello', 2, 3)` → 'ell' |
| **LENGTH** | Longueur en octets | `LENGTH('Café')` → 5 |
| **CHAR_LENGTH** | Longueur en caractères | `CHAR_LENGTH('Café')` → 4 |
| **UPPER** | Convertir en majuscules | `UPPER('hello')` → 'HELLO' |
| **LOWER** | Convertir en minuscules | `LOWER('HELLO')` → 'hello' |
| **REPEAT** | Répéter N fois | `REPEAT('SQL', 3)` → 'SQLSQLSQL' |
| **REPLACE** | Remplacer du texte | `REPLACE('Hi', 'i', 'ello')` → 'Hello' |
| **REVERSE** | Inverser | `REVERSE('Hello')` → 'olleH' |
| **CONCAT** | Concaténer | `CONCAT('A', 'B')` → 'AB' |
| **CONCAT_WS** | Concaténer avec séparateur | `CONCAT_WS(',', 'A', 'B')` → 'A,B' |
| **TRIM** | Supprimer espaces | `TRIM('  Hi  ')` → 'Hi' |
| **LPAD** | Remplir à gauche | `LPAD('5', 3, '0')` → '005' |
| **RPAD** | Remplir à droite | `RPAD('5', 3, '0')` → '500' |

---

## 🎯 Exemples Pratiques Complets

### Exemple 1 : Nettoyer et Formater des Données

```sql
-- Nettoyer des emails
UPDATE users 
SET email = LOWER(TRIM(email));

-- Formater des numéros de téléphone
UPDATE users 
SET phone = REPLACE(REPLACE(REPLACE(phone, ' ', ''), '-', ''), '.', '');

-- Créer des slugs pour URLs
SELECT 
    title,
    LOWER(
        REPLACE(
            REPLACE(
                REPLACE(TRIM(title), ' ', '-'),
                ',', ''
            ),
            '.', ''
        )
    ) AS slug
FROM articles;
```

### Exemple 2 : Validation et Analyse

```sql
-- Trouver les emails invalides (sans @)
SELECT email FROM users 
WHERE LOCATE('@', email) = 0;

-- Trouver les usernames trop courts
SELECT username FROM users 
WHERE CHAR_LENGTH(username) < 3;

-- Analyser la longueur moyenne des commentaires
SELECT AVG(CHAR_LENGTH(comment)) AS avg_comment_length 
FROM comments;
```

---

## 🧪 Exercices Pratiques

### Exercice 1 : Manipulation de Texte

```sql
-- Créez une requête qui :
-- 1. Extrait les 3 premiers caractères du nom
-- 2. Convertit le nom en majuscules
-- 3. Répète le résultat 2 fois
-- 4. Remplace les espaces par des underscores

-- Solution :
SELECT 
    name,
    REPLACE(
        REPEAT(
            UPPER(LEFT(name, 3)),
            2
        ),
        ' ', '_'
    ) AS processed_name
FROM users;
```

---

## ➡️ Prochaine Étape

Passez au **[Module 05 : Fonctions Numériques](../05-numeric-functions)** pour apprendre à manipuler des nombres en SQL.