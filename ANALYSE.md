# Analyse des bugs

# Petit Info :

- **commit sur le repo d'origine** : je n'est pas pu commit sur votre repo faute de permission vous m'avez pas ajouter dans les collab du projet dans github, donc j'ai créer un fork et j'ouvre une pull request vers votre repo

## Bug 1

- **Fichier :** `src/app.js`, ligne 14.
- **Cause :** `express.json()` était placé après les routes, donc `req.body` était vide.
- **Correction :** j'ai déplacé `express.json()` avant les routes.
- **Test :**

  ```bash
  curl -i -X POST localhost:3000/api/books -H 'Content-Type: application/json' -d '{"title":"Fleuris","author":"Egbou mewe","stock":2}'
  ```

## Bug 2

- **Fichier :** `src/services/books.service.js`, ligne 26.
- **Cause :** `loadBooks()` renvoie une Promise et il manquait `await`.
- **Correction :** ajout de `await` avant `loadBooks()`.
- **Test :**

  ```bash
  curl -i localhost:3000/api/books/available
  ```

## Bug 3

- **Fichier :** `src/services/books.service.js`, ligne 38.
- **Cause :** l'id dans l'URL est une chaîne, mais les ids des livres sont des nombres.
- **Correction :** conversion de l'id avec `Number(id)`.
- **Test :**

  ```bash
  curl -i localhost:3000/api/books/1
  ```

## Bug 4

- **Fichier :** `src/services/books.service.js`, ligne 16.
- **Cause :** la page 1 commençait après les premiers livres.
- **Correction :** l'offset est maintenant `(page - 1) * limit`.
- **Test :**

  ```bash
  curl -s 'localhost:3000/api/books?page=1&limit=3'
  ```

  La réponse doit contenir les livres 1, 2 et 3.

## Bug 5

- **Fichier :** `src/services/books.service.js`, ligne 17.
- **Cause :** `splice()` enlève vraiment les livres du tableau en mémoire.
- **Correction :** remplacement de `splice()` par `slice()`, qui ne modifie pas le tableau.
- **Test :**

  ```bash
  curl -s 'localhost:3000/api/books?page=1&limit=3'
  curl -s 'localhost:3000/api/books?page=1&limit=3'
  ```

  Le total doit rester à 6.

## Bug 6

- **Fichier :** `src/middlewares/validateBook.js`, ligne 16.
- **Cause :** `!stock` considère `0` comme faux alors que c'est un stock valide.
- **Correction :** je vérifie seulement si `stock` vaut `undefined` ou `null`.
- **Test :**

  ```bash
  curl -i -X POST localhost:3000/api/books -H 'Content-Type: application/json' -d '{"title":"ismail","author":"ismail bouloukt","stock":0}'
  ```

## Bug 7

- **Fichier :** `src/services/books.service.js`, ligne 49.
- **Cause :** `books.length + 1` peut donner un id déjà existant après une suppression.
- **Correction :** le nouvel id est calculé avec le plus grand id existant.
- **Test :**

  ```bash
  curl -X DELETE localhost:3000/api/books/3
  curl -i -X POST localhost:3000/api/books -H 'Content-Type: application/json' -d '{"title":"wili","author":"dombo","stock":2}'
  ```
