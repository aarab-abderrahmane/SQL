

### **1. Gestion des Tables (DDL - Langage de Définition de Données)**
#### **Créer une table**
```sql
CREATE TABLE NomTable (
    Colonne1 TYPE Contraintes,
    Colonne2 TYPE Contraintes,
    ...
);
```
**Exemple :**
```sql
CREATE TABLE Employés (
    ID INT PRIMARY KEY,
    Nom VARCHAR(50) NOT NULL,
    Salaire DECIMAL(10,2) CHECK (Salaire > 0),
    DepartementID INT,
    FOREIGN KEY (DepartementID) REFERENCES Departements(ID)
);
```

#### **Modifier une table**
- **Ajouter une colonne** :
  ```sql
  ALTER TABLE NomTable ADD Colonne TYPE Contraintes;
  ```
  Exemple :
  ```sql
  ALTER TABLE Employés ADD Email VARCHAR(100);
  ```

- **Supprimer une colonne** :
  ```sql
  ALTER TABLE NomTable DROP COLUMN Colonne;
  ```
  Exemple :
  ```sql
  ALTER TABLE Employés DROP COLUMN Email;
  ```

- **Modifier le type d’une colonne** :
  - **MySQL** :
    ```sql
    ALTER TABLE NomTable MODIFY Colonne NOUVEAU_TYPE [Contraintes];
    ```
    Exemple :
    ```sql
    ALTER TABLE Employés MODIFY Salaire NUMERIC(12,2) NOT NULL;
    ```
  - **PostgreSQL** :
    ```sql
    ALTER TABLE NomTable ALTER COLUMN Colonne TYPE NOUVEAU_TYPE USING (Colonne::NOUVEAU_TYPE);
    ```
    Exemple :
    ```sql
    ALTER TABLE Employés ALTER COLUMN Salaire TYPE NUMERIC(12,2) USING (Salaire::NUMERIC(12,2));
    ```

#### **Supprimer une table**
```sql
DROP TABLE NomTable;
```
> ⚠️ **Attention** : Cette commande supprime définitivement la table et ses données.

---

### **2. Gestion des Données (DML - Langage de Manipulation de Données)**
#### **Insérer des données**
```sql
INSERT INTO NomTable (Col1, Col2, ...) VALUES (Val1, Val2, ...);
```
**Exemple :**
```sql
INSERT INTO Employés (ID, Nom, Salaire, DepartementID) 
VALUES (1, 'Dupont', 5000.00, 3);
```

#### **Mettre à jour des données**
```sql
UPDATE NomTable SET Colonne = Valeur WHERE Condition;
```
**Exemple :**
```sql
UPDATE Employés SET Salaire = 5500.00 WHERE ID = 1;
```
> ⚠️ **Oublier le `WHERE` supprime toutes les lignes !**

#### **Supprimer des données**
```sql
DELETE FROM NomTable WHERE Condition;
```
**Exemple :**
```sql
DELETE FROM Employés WHERE ID = 1;
```
> ⚠️ **Oublier le `WHERE` supprime toutes les lignes !**

---

### **3. Contraintes pour l'Intégrité des Données**
| Contrainte       | Description                          | Exemple |
|------------------|--------------------------------------|---------|
| `PRIMARY KEY`    | Identifiant unique + NOT NULL        | `ID INT PRIMARY KEY` |
| `FOREIGN KEY`    | Lien avec une clé primaire d'une autre table | `FOREIGN KEY (DepartementID) REFERENCES Departements(ID)` |
| `NOT NULL`       | Interdit les valeurs nulles          | `Nom VARCHAR(50) NOT NULL` |
| `UNIQUE`         | Valeurs uniques dans la colonne      | `Email VARCHAR(100) UNIQUE` |
| `CHECK`          | Condition sur les valeurs            | `CHECK (Salaire > 0)` |
| `DEFAULT`        | Valeur par défaut                    | `Statut VARCHAR(20) DEFAULT 'Actif'` |

---

### **4. Conditions et Filtres (WHERE, JOIN, GROUP BY)**
#### **Filtrer avec `WHERE`**
```sql
SELECT * FROM Employés WHERE Salaire > 5000;
```

#### **Opérateurs logiques**
- `AND`, `OR`, `NOT`
- `IN`, `BETWEEN`, `LIKE` (pour les motifs)
  ```sql
  SELECT * FROM Employés WHERE Nom LIKE 'D%'; -- Noms commençant par D
  ```

#### **Jointures (`JOIN`)**
- **INNER JOIN** : Données communes aux deux tables
  ```sql
  SELECT E.Nom, D.NomDepartement
  FROM Employés E
  INNER JOIN Departements D ON E.DepartementID = D.ID;
  ```
- **LEFT JOIN** : Toutes les lignes de la table de gauche + correspondances
- **RIGHT JOIN** : Toutes les lignes de la table de droite + correspondances
- **FULL JOIN** : Toutes les lignes des deux tables (si disponible)

#### **Agrégation avec `GROUP BY`**
```sql
SELECT DepartementID, AVG(Salaire) AS SalaireMoyen
FROM Employés
GROUP BY DepartementID
HAVING AVG(Salaire) > 4000;
```

---

### **5. Fonctions SQL Utiles**
| Fonction         | Description                          | Exemple |
|------------------|--------------------------------------|---------|
| `COUNT()`, `SUM()`, `AVG()` | Agrégation | `SELECT COUNT(*) FROM Employés;` |
| `ROUND()`        | Arrondir un nombre                   | `SELECT ROUND(Salaire, 2);` |
| `CONCAT()`       | Concaténer des chaînes               | `SELECT CONCAT(Nom, ' ', Prénom);` |
| `DATE()`         | Extraire la date d’un datetime       | `SELECT DATE(DateNaissance);` |
| `CASE`           | Condition logique                    | ```sql CASE WHEN Salaire > 5000 THEN 'Haut' ELSE 'Bas' END AS Categorie ``` |

---

### **6. Bonnes Pratiques**
1. **Toujours utiliser `WHERE` avec `UPDATE`/`DELETE`** pour éviter de modifier/supprimer toutes les lignes.
2. **Sauvegarder les données avant des opérations destructrices** (ex : `DROP TABLE`).
3. **Utiliser des transactions** pour groupes d’opérations critiques :
   ```sql
   START TRANSACTION;
   UPDATE Comptes SET Solde = Solde - 100 WHERE ID = 1;
   UPDATE Comptes SET Solde = Solde + 100 WHERE ID = 2;
   COMMIT; -- Ou ROLLBACK en cas d'erreur
   ```
4. **Indexer les colonnes fréquemment utilisées dans les clauses `WHERE` ou `JOIN`** pour améliorer les performances :
   ```sql
   CREATE INDEX idx_nom ON Employés(Nom);
   ```

---

### **7. Différences Clés entre SGBD**
| Opération                | MySQL                  | PostgreSQL              |
|--------------------------|------------------------|-------------------------|
| Modifier le type d’une colonne | `MODIFY`               | `ALTER COLUMN ... TYPE` |
| Renommer une colonne     | `CHANGE`               | `ALTER COLUMN ... RENAME TO` |
| Auto-incrémentation      | `AUTO_INCREMENT`       | `SERIAL`                |
| Requêtes JSON            | `JSON`                 | `JSONB` (stockage binaire) |

---

### **8. Résumé Visuel**
| Action                 | Commande SQL                     |
|------------------------|----------------------------------|
| Créer une table        | `CREATE TABLE`                   |
| Modifier une table     | `ALTER TABLE`                    |
| Supprimer une table    | `DROP TABLE`                     |
| Insérer des données    | `INSERT INTO`                    |
| Mettre à jour des données | `UPDATE ... SET ... WHERE`     |
| Supprimer des données  | `DELETE FROM ... WHERE`          |
| Filtrer des résultats  | `WHERE`, `HAVING`                |
| Joindre des tables     | `INNER JOIN`, `LEFT JOIN`        |
| Regrouper des données  | `GROUP BY`                       |
| Contraintes            | `PRIMARY KEY`, `FOREIGN KEY`, `CHECK`, `UNIQUE` |


