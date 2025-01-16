# Bienvenue au Daily Bugle

## Description

Vous avez été recruté par **The Daily Buggle** pour remplacer le développeur précédent qui travaillait sur un moyen de changer rapidement le titre de la page d'accueil du site web.

Malheureusement, il n'a pas terminé et a laissé le projet incomplet...

Donc, maintenant c'est votre travail de le réparer !  
>**Votre objectif** : 
> - Choisir un titre aléatoire sans recharger toute la page. 
> - Ensuite, ajoutez un menu pour rechercher un titre avec des mots-clés spécifiques (toujours sans recharger la page).

## Pour commencer

Clonez ce dépôt. 
{: .alert-info } 

Puis, réalisez les tâches suivantes :
- ```bash
  cd workshop-php-ajax-daily
  ```
- ```bash
  composer install
  ```
- Créez le fichier `db.php` à partir du fichier **db.php.dist** avec les informations d'identification pour PDO et la base de données `workshop-php-ajax-daily`.
- Exécutez la commande php `php migration.php`.
- Enfin, démarrez le serveur avec la commande
```bash
php -S localhost:8000 -t public
```

## 1. Obtenir un titre aléatoire sans recharger la page

1. Tout d'abord, vous devez préparer le côté serveur. Regardez dans `routes.php`, vous verrez que vous avez déjà la route `api/articles/random`. Essayez [http://localhost:8000/api/articles/random](http://localhost:8000/api/articles/random). Oui, rien...

2. Pour le corriger, allez dans `AjaxController.php` et complétez la fonction `getRandomArticle()`. Cette fonction doit retourner les données d'un article aléatoire au format JSON (un seul article !). 

Indice 1 : Créez une méthode personnalisée `selectRandomOne()` dans `ArticleManager`.  
Indice 2 : Utilisez `ORDER BY RAND() LIMIT 1` dans votre requête pour obtenir un seul résultat aléatoire.
{: .alert-info }

Résultat : Si vous avez bien fait, [http://localhost:8000/api/articles/random](http://localhost:8000/api/articles/random) devrait ressembler à ça : 
```json
{
  "id": "3",
  "title": "Le Docteur Octopus braque une autre banque !",
  "author": "Peter Parker",
  "picture": "assets/images/article_3.jpg",
  "date": "2021-04-28 00:00:00",
  "content": "[ARTICLE_CONTENT]"
}
```

1. Une fois la route prête, passons du côté client. Dans `public\assets\js\script.js`, vous trouverez un `//TODO 1 : Obtenir un article aléatoire`, c'est la partie du code qui sera déclenchée lors du clic sur le bouton "**Changer le titre**". L'écouteur d'événement est déjà configuré.

C'est ici que vous devez travailler pour contacter la route `api/articles/random`, obtenir les données du titre et mettre à jour la page d'accueil.

Indice 1 : Utilisez `fetch()` en JS pour appeler la route. [Documentation Fetch ici](https://developer.mozilla.org/fr/docs/Web/API/Fetch_API/Using_Fetch).  
Indice 2 : Lorsque vous avez les données JSON, utilisez la fonction fournie `updateHeadline(article)` qui mettra à jour le DOM de la page d'accueil avec les données de l'article aléatoire.
{: .alert-info }

## 2. Ajouter un menu pour sélectionner un titre à partir de mots-clés spécifiques

Nous devons maintenant pouvoir sélectionner un article spécifique par un mot-clé de son titre.
Le développeur précédent a commencé à implémenter la fonctionnalité dans la div `searchMenu` (qui s'ouvre lorsque vous cliquez sur le bouton "**Rechercher un titre spécifique**") de `index.html.twig`.

Il a également laissé un `//TODO 2` dans `script.js` pour déclencher du code lors de la saisie dans le champ de recherche.

### Préparez la route
Dans `routes.php`, il y a `api/articles/search`. Attention, cette route prend un paramètre. Elle se réfère à la méthode `searchArticles()` de `AjaxController`.
Commencez par préparer cette route pour retourner tous les articles contenant dans le titre le mot passé en paramètre.

Cela devrait ressembler à quelque chose comme [http://localhost:8000/api/articles/search?search=spider](http://localhost:8000/api/articles/search?search=spider).  
Ici, nous recherchons tous les articles contenant le mot "**spider**" dans leur titre.

Indice 1 : Vous aurez besoin d'une nouvelle méthode dans `ArticleManager` (et utilisez un LIKE).  
Indice 2 : Votre méthode `searchArticles()` dans `AjaxController` doit retourner une collection d'articles au format JSON.
{: .alert-info }

Par exemple :
```json
[
  {
    "id": "1",
    "title": "Spider-Man : Ami ou Ennemi ?",
    "author": "Jonah Jamesson",
    "picture": "assets/images/article_1.jpg",
    "date": "2021-01-18 00:00:00",
    "content": "Cillum enim dolor nostrud irure sint cupidatat esse nulla ipsum proident nisi. Eiusmod reprehenderit aliqua nostrud mollit. Ex ut in ipsum commodo culpa esse ullamco ex. Anim velit et qui non elit pariatur. Non occaecat est veniam aliquip incididunt duis eiusmod irure magna aute. Irure officia consequat est cillum occaecat officia ipsum culpa sint irure pariatur cillum veniam aliqua. Tempor quis veniam aliqua amet magna laborum consectetur laborum laborum do. Sit anim laborum aliquip eu voluptate do aliqua proident. Ex consectetur occaecat in aliqua labore deserunt duis deserunt eiusmod fugiat. Dolor in quis sint consequat occaecat ea aliquip minim proident labore."
  },
  {
    "id": "5",
    "title": "Un autre Spider-man ? Un autre criminel",
    "author": "Jonah Jamesson",
    "picture": "assets/images/article_5.jpg",
    "date": "2021-05-23 00:00:00",
    "content": "Minim eiusmod Lorem do exercitation id pariatur non dolore ullamco ea. Magna id veniam eu nulla nostrud velit consectetur ad in fugiat in ea aliqua proident. Ipsum adipisicing minim cupidatat eiusmod aute. Consequat voluptate minim nostrud laboris sunt eiusmod ut ut exercitation qui eiusmod nostrud minim quis. Eu reprehenderit ad eiusmod consequat. Aute pariatur ullamco esse dolor eu eiusmod exercitation do sit amet enim. Cupidatat magna eiusmod fugiat id anim eiusmod consectetur consectetur deserunt tempor esse proident est. Laborum nulla fugiat aliqua elit mollit laboris. Tempor nisi id culpa quis sunt duis in. Ut consectetur incididunt."
  }
]
```

### Fetch la route avec la requête de recherche
Une fois la méthode fonctionnelle, retournez dans `script.js` pour appeler la route associée `api/articles/search?search=mySearch` avec `fetch()`. L'écouteur d'événement associé sera exécuté à chaque fois qu'une nouvelle lettre est ajoutée dans le champ de saisie. La chaîne de requête doit utiliser la valeur actuelle du champ de saisie (c'est déjà fait avec la ligne `let search = e.target.value;`)

### Afficher la réponse
Une fois que vous avez la réponse JSON, vous devez alimenter le `<ul id="resultList">` avec les titres des articles correspondants.

## 🎁 Bonus : titres cliquables
Maintenant, rendez ces titres cliquables et envoyez-les à la route `/article` (regardez la route dans `routes.php` pour savoir ce dont cette route a besoin pour fonctionner) et complétez le contrôleur pour afficher une page avec les détails de l'article.

Voilà ! Bonne chance avec ce projet passionnant ! 🚀
