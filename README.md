# La non synchronicité dans JavaScript

Nous avons déjà vu de nombreuses choses dans JavaScript : variables, opérateurs mathématiques, opérateurs logiques, conditions, switches, callbacks, et avons vu comment écrire des algos assez solides à ce sujet ! Mais nous devons maintenant aborder un autre point de matière très complexe, j'ai nommé la programmation asynchrone !

## 1. La programmation asynchrone : définition et analogie

Jusqu'à présent, nous avons fait de la programmation où les tâches étaient effectuées au fur et à mesure que les lignes étaient exécutées par VSCode. Tout au plus prévoyions-nous l'un ou l'autre callback, mais grosso modo, notre code suivait son bout de chemin; un peu comme un employé qui a reçu une liste de tâches de son employeur pour la journée, tenant compte de certaines instructions qui demandent des actions un brin complexes (les callback) :  "Je suis ligne untel, je dois faire ça; ligne suivante, ça; ah tiens, un call back, je vais donc le chercher; bon, ligne suivante, ..." 

Imaginons un employé dans un bureau de poste. Sa mission principale est de préparer des colis à envoyer. Pour chaque colis, il a une liste de tâches à accomplir : peser le colis, étiqueter, sceller et le placer dans le bon compartiment pour l'expédition. Ces étapes sont réalisées l'une après l'autre, de manière systématique.

Cette approche est similaire à la programmation synchrone : chaque ligne de code est exécutée l'une après l'autre, dans l'ordre dans lequel elles apparaissent.

Revenons à notre employé. Tout à coup, un téléphone sonne sur son bureau. Il sait que chaque fois que ce téléphone sonne, c'est pour lui signaler un colis spécial qui nécessite une attention immédiate. Quand cela se produit, il doit interrompre ce qu'il fait, aller chercher ce colis spécial, traiter rapidement puis revenir à sa tâche précédente.

Cette interruption est l'équivalent d'un callback en JavaScript. Même si notre employé a une liste de tâches à accomplir, le téléphone (notre callback) peut interrompre à tout moment sa séquence normale, lui demandant d'effectuer une tâche spécifique avant de continuer là où il s'était arrêté.

Dans cet exemple, nous sommes dans la programmation synchrone, avec un callback qui peut se glisser dedans à tout moment !

Mais nous allons maintenant voir la **programmation asynchrone** qui elle va prendre en compte une nouvelle dimension : le temps d'exécution !

Imaginons que notre employé de poste aille après sa journée de boulot dans un resto fast food, du genre où on reçoit un beeper quand on a commandé, et qui vibre, fait du son et clignotte une fois que notre commande est prête : notre biper illustre la notion d'asynchronicité : ici, la commande n'est prête qu'après un certain délai, et le biper est la **promesse**, concept central de la programmation asynchrone !

Maintenant imaginons que notre employé des postes empoigne son burger et que, catastrophe, plein de sauce tombe sur son pull ! Pour le nettoyer, il l'amène à la blanchisserie et là on lui donne un ticket avec son nom, et une référence. C'est bien entendu une promesse. Mais avoir cett epromesse,é changer ce ticket contre son pull une fois qu'il sera nettoyé ne garantit pas forcément que le pull sera bien nettoyé ! S'il estt probable que tout se apsse bien, il y a aussi un risque non nul qu'il y ait eu trop de détergeant dans la machine, qu'on ait eprdu le pull ou tout simplement qu'on ne l'ait pas nettoyé, car oublié dans un coin. 

La promesse ne garantit pas une bonne exécution de l'instruction de base ! Et il existe ici deux cas de figures : la réussite et l'échec, qui peuvent entrainer des effets différents : en cas de réussite, notre employé des postes paie et remercie la personne à l'accueil de la blanchisserie, soit il pète une durite. 

Pour comprendre els promesse dans un code JS, tentons de voir les aventures de notre employé des postes... contée en code !

## 2. La programmation asynchrone : exemple basé des aventures de notre employé de spostes à la blanchisserie

Voici le code qui raconte ses (més)aventures :

```javascript
// Notre promesse représente le processus de nettoyage du pull à la blanchisserie
const nettoyerPull = new Promise((resolve, reject) => {
    // Ici, nous simulons le processus de nettoyage du pull. Pour simplifier, disons que le nettoyage prend 2 secondes.
    setTimeout(() => {
        // Nous utilisons Math.random() pour simuler la possibilité que le nettoyage échoue ou réussisse.
        const chanceDeReussite = Math.random();
        
        // Disons que 80% du temps, le nettoyage est réussi.
        if (chanceDeReussite > 0.2) {
            resolve(`Pull nettoyé avec succès !`);
        } else {
            // Les 20% du temps restant, il y a un problème avec le nettoyage.
            reject(`Oops, il y a eu un problème lors du nettoyage du pull.`);
        }
    }, 2000);
});

console.log(`L'employé amène son pull à la blanchisserie...`);

nettoyerPull
    .then(message => {
        console.log(message); // Cela s'exécute si le pull est nettoyé avec succès.
        console.log(`L'employé paie et remercie la personne à l'accueil de la blanchisserie.`);
    })
    .catch(erreur => {
        console.log(erreur); // Cela s'exécute s'il y a eu un problème lors du nettoyage.
        console.log(`L'employé pète une durite à la blanchisserie.`);
    })
    .finally(() => {
        console.log(`L'employé quitte la blanchisserie.`);
    });
```

C'est joli, hein ? Tentons de décomposer, en commençons par la déclaration de notre promesse :

La ligne 35 est capitale : on crée une variable qui est une promesse, ici un cosntructeur, écrit en PascalCase, "new Promise", qui prend deux paramètres : le cas de figure de réussite ou le cas de figure de l'échec ! 

La ligne 37 met en place un timeout pour simuler la durée;

La ligne 39 met en place un générateur de nombre aléatoire entre 0 et 1, avec une constante

A partir de la ligne 42, les choses deviennent intéressantes : on met en place une alternative : si notre constante est supérieure à 0,2 : le pull a  été nettoyé ! Mais il y a 20% de chances que la blanchisserie se soit loupée.  

Maintenant, on passe à l'exécution de la promesse, qui va nous permettre de voir le .then(); le .catch(); et le .finally(); qui sont trois méthodes qui s'appliquent à notre promesse, en fonction des cas de figure !

Ligne 54, on est dans le cas de figure où dans 80% des cas, le pull est nettoyé, on utilise alors la **méthode .then();**. Dans ce cas, on écrit une focntion fléchée, qui va afficher deux choses :

* D'abord, la mention "Nettoyé avec succès !", qui nous vient de la ligne 43, de la déclaration de notre promesse !
* Puis le emssage avec la réaction de notre employé qui paie.

La ligne 58, avec la **méthode .catch();** aborde le cas de figure inverse :

* D'abord on rappelle l'erreur contenue ligne 46, "Oops, il y a eu un problème lors du nettoyage du pull"
* Puis, il y a affichage de la réaction de notre empoyé.

Bon certes, notre employé réagit différement en fonction de si la blanchisserie a fait son taff correctement. Mais dans tous les cas, à un moment, il va bien devoir partir, qu'il soit satisfait ou non. C'est ici que rentre en jeu la **méthode .finally();**, ligne 62, qui mentionne le départ de notre employé !

## 3. Assync/await : une autre synthaxe pour les promesses

Reprennons l'histoire de notre employé de poste. Ce que je ne vous ai pas dit, c'est qu'il 'naime aps attendre des jours pour récupérer son pull. Or, il a etendu aprler d'une nouvelle entreprise, *Bulles et Brouillard* qui utilise une technologie d'avant-garde pour nettoyer les vêtements : un espèce de champ de brouillard dans lequel els vêtements sont plongés, et quasi-instantanément, on récupère des vêtements propres. Génial, non ? Oui, mais il y a une entourloupe ! Il existe en effet 20 % de chance que el vêtement disparaisse avec les taches !

Voyons ça de plus près avec le code :

```javascript
// La fameuse blanchisserie "Bulles & Brouillard"
function bullesEtBrouillard(item) {
    return new Promise((resolve, reject) => {
        // Nous générons une chance aléatoire entre 0 et 100.
        const randomChance = Math.floor(Math.random() * 100);

        // Si la chance est supérieure ou égale à 20, cela signifie que nous avons 80% de chance de réussir.
        if (randomChance >= 20) {
            resolve(`Votre ${item} est impeccable grâce à Bulles & Brouillard!`);
        } else {
            // Sinon, il y a 20% de chance que quelque chose tourne mal.
            reject("Erreur chez Bulles & Brouillard : Votre " + item + " a disparu dans le brouillard !");
        }
    });
}

async function visiteBullesEtBrouillard() {
    try {
        // Nous attendons la résolution de la promesse. 
        // Quand elle est résolue, le résultat est stocké dans "result".
        const result = await bullesEtBrouillard('pull');
        console.log(result);
        console.log("L'employé paie avec le sourire et remercie l'équipe de Bulles & Brouillard.");
    } catch (error) {
        // Si la promesse est rejetée, nous entrons dans le bloc "catch".
        console.log(error);
        console.log("L'employé, déconcerté, envisage de trouver une autre blanchisserie.");
    }
}

visiteBullesEtBrouillard();
```

Y'a pas à dire, l'async function a une tête bizarre ! Regardons un template vide :

```javascript
async function nomDeLaFonction(parametre1, parametre2) {
    try {
        // Code à exécuter ici, par exemple :
        // const resultat = await uneAutreFonctionAsync(parametre1);
    } catch (erreur) {
        // Gestion des erreurs ou des rejets de promesse ici
        console.error("Une erreur s'est produite:", erreur);
    } finally {
        // Ce bloc est optionnel et sera exécuté qu'il y ait eu une erreur ou non.
        // Code à exécuter après avoir traité la promesse ou l'erreur
    }
}
```

Tout d'abord, intéressons-nous à **"await"**. Il s'agit de la période de temps pour que notre employé des postes boive un café pour que le nettoyage par brouillard soit terminé. 

Ensuite, on peut le subdiviser en trois parties, la troisième représente le "finaly" qu'on a déjà vu, donc pas besoin de s'attarder dessus. 

Déjà, le "await" se trouve dans le "try". Pourquoi ? je ne saurais pas le dire avec certitude, mais j'ai l'impression que c'est parce qu'on part du principe que la promesse donnera une réponse qui répond à nos attentes, d'où l'idée de mettre le code de réussite dans le "try".

"catch", enfin, traite le cas où le résultat produit par notre promesse est négatif, avec le code associé. 

Dit autrement, la async function (qui comprend toujours en son sein dans "try" "await") attend tranquilou le résultat de notre fonction de promesse. Quand il l'a, on prend en compte le résultat stocké dans la variable de résultat. S'il matche avec nos attentes, c'est le code de "try" qui s'applique, mais si le résultat qu'on a n'est pas l'attendu, il est rejeté, et c'est alors "catch" qui prend la relève !

La synthaxe est un peu déroutante en imbriquant le "await" dans la partie "try", mais dans un premier temps, petit conseil, comme avc le DOM quand on désignait les éléments à rendre itnerractif, ne vous focussez pas sur les structures a priori spécieuses pour se concentrer sur votre code à vous en fonction de la réponse attendue... ou non ;-)

## 3.1 Pourquoi travailler avec les async/await ?

Avant toute chose, les deux approches sont bonnes, il y en a pas une qui est meilleure que l'autre. Mais async/await est plus lisible que des suites de .then(); .catch();. Si on compare la structure .then()/.catch() :

```javascript
bullesEtBrouillard('chemise')
    .then(response => {
        console.log(response); // Si tout va bien
    })
    .catch(error => {
        console.error(error); // Si votre chemise est portée disparue
    });
```

A la structure async/await :

```javascript
async function nettoyerVetement() {
    try {
        // Tente de laver les vêtements
        const result = await bullesEtBrouillard('chemise');
        console.log(result); // Si tout va bien
    } catch (error) {
        console.error(error); // Si votre chemise est portée disparue
    }
}

nettoyerVetement();
```

La seconde ressemble plus à de la programmation synchrone, et peut faire penser à des else/if plutôt qu'à des applications de promesses qui elles peuvent rappeler le DOM.

Dès lors, si on a le choix, quelle structure utiliser ? Ça va dépendre de vous et de vos envies, il n'y a pas "une bonne approche", mais de ce qui vous convient. La structure avec la suite de méthodes vous plait ? EN avant pour le .then()/.catch() ! Les promesses vous destabilisent ? Préférez alors l'async/await pour rester dans une structure similaire à des choses connues ! 

## 4. Résolution ou rejet, et critères de résolution.

Pour aller un peu plus loin dans la programmation asynchrone, nous devons faire un détour pour la terminologie. Vu que les promesses laissent toujours un délai passer pour s'exécuter (fut-il minime), nous distinguons deux étapes : le début et, plus tard, la fin. Le début, quand on écrit <return new Promise>, on **crée** notre promesse et elles est en *attente*, en **pending**. Ensuite, une fois le délai/condition de résolution rencontré, on dit que la promesse est **fullfiled**, c'est à dire "*résolue* dans la langue de Voltaire. Mais ici nous rentrons dans une logique un peu particulière.

Pour la comprendre, partons du "if/else" qui prévoyait une série d'alternatives qui étaient "côte à côte". Avec les promesses, il y a une petite subtilité en plus : la valeur **fullfiled** correspon en fait à une promesse résolue... et qui a rencontré les conditions de validations que nous avons mis plus tôt ! Mais si après résolution, notre promesse n'est pas accomplie, elle passe sous le régime du **rejected**, elle est *"rejettée"* !

Quand aux conditions d'accomplissement des promesses, dans les exemples de notre employé de poste, on a vu que la probabilité :

```javascript
function bullesEtBrouillard(item) {
    return new Promise((resolve, reject) => {
        const randomChance = Math.floor(Math.random() * 100);
        if (randomChance >= 20) {
            resolve(`Votre ${item} est impeccable grâce à Bulles & Brouillard!`);
        } else {
            reject("Erreur chez Bulles & Brouillard : Votre " + item + " a disparu dans le brouillard !");
        }
    });
}
```

Mais on pourait tout aussi bien mettre un timer : au delà d'un certain temps, ce ne sont aps les taches et odeurs, maiss es vêtements qui disparaissent !

```javascript
function bullesEtBrouillard(item) {
    return new Promise((resolve, reject) => {
        const maxTime = 3000; // 3 secondes
        const cleaningTime = Math.floor(Math.random() * 5000); // Temps aléatoire pour nettoyer entre 0 et 5 secondes

        setTimeout(() => {
            if (cleaningTime <= maxTime) {
                resolve(`Votre ${item} est impeccable grâce à Bulles & Brouillard!`);
            } else {
                reject(`Erreur chez Bulles & Brouillard : Votre ${item} a disparu dans le brouillard !`);
            }
        }, cleaningTime);
    });
}
```

Et il existe tout un tas de critères de vérification, dont les fetch(); qui nous servent pour les requêtes html, que nous traitons dans la prochaine section !

## 5. Protocoles HTTP et .fetch();

Maintenant que nous avons compris ce qu'est une promesse, laissons notre employé des postes à ses pulls mal lavés, et revenons au fil rouge de tous nos tutos, le web development, et le .fetch(); est le point de rencontre entre promesses et web ! Mais pour voir et comprendre tout ça, n va commencer par refair eun point sur les protocoles HTTP.

### 5.1. Le protocole HTTP/HTTPS

Internet se compose d'ordinateurs qui communiquent entre eux, soit directement, soit via des serveurs. Ces communications sont souvent constituées de requêtes. Par exemple, lorsque nous accédons à www.google.com depuis notre navigateur, nous envoyons en réalité une requête au serveur hébergeant www.google.com : "Salut, peux-tu me fournir les fichiers de Google.com pour que je puisse les afficher dans le navigateur ? Ce serait top de ta part !". En réalité, nous lui demandons de traduire le nom de domaine en une adresse IP, qui est une série de chiffres représentant l'emplacement du serveur. C'est le rôle du DNS, un immense annuaire, de faire cette conversion.

Et quand on cherche `www.google.com`, en réalité on cherche `http://www.google.com` et ici, le http:// est le **protocole** la *méthode* si on peut dire par laquelle on se connecte depuis son ordi au serveur, plus précisément dont on fait nos requêtes !

Et ces requêtes peuvent se faire entre un ordinateur et un serveur, entre deux ordinateurs... voire entre deux sites webs de manière automatisée grâce à l'AJAX !

###### Et le HTTPS ?

C'est aussi un protocole de requêtes à un serveur ou autre ordinateur, le même que le HTTP, a ceci près qu'il est crypté et sécurisé. De ce fait, on ne peut pas comprendre els demandes que vous faites à un seveur donné, et donc on ne peut pas les modifier à son avantage. 

### 5.2. AJAX : Asynchronous Javascript And XML

Comme le titre l'indique, AJAX est un acronyme. De tous les termes qui le composent, nous connaissons déjà Asynchronous, Javascript (fatalement), mais quid du XML ? C'est un ancien markup language, qui comme html et markdown, sert à décrire, structurer des informations; et qui servait dans l'échange de données entre informations.

Mais il y a une astuce ici : ça fait un bout de temps que plus personne, hormis quelques hermites et profs de fac proches de la retraite utilisent XML, tout le monde est passé au JSON, dont nous avons déjà parlé dans le localStorage ! Mais le nom "AJAX" n'a aps été modifié pour autant en "AJAJ", vu que le principe reste sesiblement le même : faire des requêtes de données avec JS de manière asynchrone en utilisant le format d'échange de données JSON, qui compacte en un format le html, le css et le JS !

#### 5.2.1. Le JSON, une introduciton succinte

Comme expliqué dans notre tuto sur le localStorage, Le JSON est une manière standardisée de formater des données, peu importe leur type. L'avantage réside dans le fait que ce format est simple dans sa structure et sa manipulation, ce qui le rend utilisable y compris dans d'autres langages de programmation, comme le Python.

Nous proposons l'analogie suivante : à la poste, ce serait une boîte colis standardisée, n'existant qu'en un seul format, mais dans laquelle on peut tout mettre. C'est comme si notre boîte à colis était acceptée partout (et là, on retrouve notre joueyx employé des postes !). 

Dans le tuto sur le localStorage, nous nous étions concentrés sur la conversions de données autre que string en string et de la conversion des strings vers le type d'origine. 

Pour aller plus loin, nous avions vu que JSON voulait dire "JavaScript Object Notation". L'astuce, c'est qu'un JSON ressemble à un objet JAvaScript, mais seulement de loin, c'est en quelque sorte un "objetoïde" !

Regardons ça de plus près, avec ici un livre dans le registre d'une bibliothèque, sous forme d'objet :

```javascript
const book = {
    title: "Le Petit Prince",
    author: "Antoine de Saint-Exupéry",
    publishedYear: 1943,
    isAvailable: true
};
```

Et là sous forme de JSON :

```JSON
{
    "title": "Le Petit Prince",
    "author": "Antoine de Saint-Exupéry",
    "publishedYear": 1943,
    "isAvailable": true
}
```

Vous voyez ? Dans la paire clé-valeur, la clé est systématiquement entre guillemets, sous format string !

Et pour convertir notre book en format JSON, il faut utiliser la méthode suivante sur l'objet JSON :

```javascript
const myJson = JSON.stringify(myObj);

console.log(myJson);
```

VOus remarqueez ? QUand on passe en JSON, on "stringifie" otre objet !

Et pour repasser du JSON à l'objet, voici le code à écrire :

```javascript
console.log(JSON.parse(myJson));
```

Là, on retire le string !

Et les valeurs acceptées pour une coversion au format JSON sont els string, bool, numbers, null, array et object.

### 5.3. Les API

On attaque ici un autre domaien ultra-large ! API, c'est l'achronyme pour **"Application Programming Interface"** ! Ça, vous en voyez tout le temps. Par exemple, si vous allez sur les fils d'infos des médias en ligne, vous verrez souvent apparaitre dedans des publications Facebook, des Tweets,d es vidéos Youtube, ... le tout dans les formats originaux Facebook, Twitter ou Youtube, comme si on avait une "fenêtre" dans la apge web de notre média vers ces sites !

Et pour trouver ces "bouts de sites", on va bien être obligés dd'aller cherhcer des URL, liens correspondant de manière... automatisée et en utilisant... Javascript... et en transmettant les données du site dont on va chercher le "morceau" en format JSON !

Maintenant qu'on sait ce qu'on cherche, encore faut-il-savoir où le chercher, parce que, bon, entre, par exemple, http://www.youtube.com et le player Youtube intégré dans un site web, il y a de la marge ! Déjà il faut avoir *quelle video* partager, et donc pour ça, il faut faire aborder une nouvelle notion, le endpoint !

#### 5.3.1. Les endpoints et un retour sur les queryString

Nous avons déjà vu dans le tuto sur le DOM que les URLs ont des queryStrings par rapport à ce qu'on recherche. Pour rappel, nous avions cet URL :

https://www.newbalance.be/fr/hommes/chaussures/made-in-uk-us/?prefn1=mensFootwearSize&prefv1=38.5&prefn2=refinementColor&prefv2=Beige

Que nous décomposions comme suit pour avoir les queryString des infos recherchées :

https://www.newbalance.be/fr/hommes/chaussures/made-in-uk-us/

?prefn1=mensFootwearSize --> le queryString du type de chaussures

&prefv1=38.5 --> le queryString de la pointure

&prefn2=refinementColor --> le queryString du choix de la couleur

&prefv2=Beige --> la couleur proprement dite

Eh bien, ce qui est au "début", avant le "?" qui marque la querystring, est le endpoint :

https://www.newbalance.be/fr/hommes/chaussures/made-in-uk-us/

Ici, le endpoint, c'est que nous cherchons les chaussures "Made in UK" ou "Made in US", le queryString venant après pour spécifier nos préférences !

Un site peut contenir beaucoup d'endpoints, et certains endpoints de sites sont déjà des API en soi, qui vont vous fournir des informations aléatoires sous format JSON, après requêtes, même si vous n'en aurez formulé aucune, comme pour ce site qui vous donne des anecdotes random sur les chats :

https://catfact.ninja/fact

Mais pourquoi donc n'obtient-on pas un joli JSON avec toutes les chaussures Made in UK et Made in US de chez New Balance avec ce lien : https://www.newbalance.be/fr/hommes/chaussures/made-in-uk-us/ ?

Là, une autre notion entre en ligne de compte, celle du **point de terminaison d'API** :

https://www.newbalance.be/fr/hommes/chaussures/made-in-uk-us/ est une page web à visiter normalement, comme tout site traditionnel.

https://catfact.ninja/fact, a en revanche,  pour vocation, de nous renvoyer des données au format JSON quand on entre l'URL dans le navigateur.

Et pourquoi cela ? Car, quand on rentre une URL dans un navigateur, on fait une requête HTTP de type "GET". Avec toutes les informations contenues dans cette URL, on dit au serveur : "Va me chercher ça et affiche-le moi dans le navigateur !". Parfois, la réponse sera une page web complète construite avec des fichiers HTML, CSS, et JavaScript. D'autres fois, ce seront des données au format JSON, prêtes à être utilisées comme on le souhaite.

#### 5.3.1.1. Un approfondissement sur les queryString

Maintenant que les choses commencent à se préciser sur la manière dont on utilisera la programmation asynchrone, revenons un peu sur les queryStrings, histoire de préciser une chose ou l'autre. 

Vous vous rappelez de ce lien ?

https://www.newbalance.be/fr/hommes/chaussures/made-in-uk-us/?prefn1=mensFootwearSize&prefv1=38.5&prefn2=refinementColor&prefv2=Beige

En fait vous pourriez reprendre la queryString et la coller n'importe où, ça ne changerait rien. Tant qu'un site n'est aps paramétré pour y répondre, aucun changement n'aura lieu. 

Regardez, je prends https://www.lemonde.fr et vais y coller ma queryString :

https://www.lemonde.fr/?prefn1=mensFootwearSize&prefv1=38.5&prefn2=refinementColor&prefv2=Beige

Si vous cliquez dessus, vous verrez bien que la page d'accueil du Monde se contrefout qu'on lui mette une queryString pour des chaussures, parce que le serveur sur lequel se trouve https://www.lemende.fr n'est pas paramétré pour prendre en compte de telles queryString. 

Maintenant essayons autre chose : sur le site de Mozilla, on va entrer une queryString manuellement et onv a chercher "chicken". 

https://developer.mozilla.org/en-US/search?q=chicken

Aussi improbalbe que ce soit, on a des résultats ! Ça veut dire que Mozilla a prévu qu'on puisse faire des requêtes GET, mais qu'on plus, le mot "chicken" apparait sur leur site ! 

Et maintenant, si on supprime "chicken" de l'url, va-t-on tomber sur un 404 ? 

https://developer.mozilla.org/en-US/search?q=

Pas dans ce cas ! Les développeurs de Mozilla ont prévu le coup avec une page d'erreur qui permet de ester sur le site !

Et si on tente par ailleurs l'endpoint improbable "swag" sur le site du Monde :

https://www.lemonde.fr/swag

On va aussi tomber sur une page d'erreur ! 

Et ce genre de rpatique tend à s'étendre. Est-ce que ça veut dire que le 404 error va disparaitre ? Non, loin de là, en lui-même, il a encore de beaux jours devant lui ! C'est la réponse par rapport à celui-ci qui change : on essaie de ne plus laisser els utilisateurs le bec dans l'eau.

#### 5.3.2. Les API, requêtes HTTP et verbes

Bon, on va maintenant rentrer dans un sujet encore un brin plus technique, et aborder vite fait ce qu'est une requête. Dans le tuto sur le DOM, on avait parlé de requêtes, "méthodes", pour rappel, avec "GET" et "POST". EN fait, je vois ai menti ! "GET" et "POST" ne sont pas des requêtes, mais des **verbs**, élément constitutif d'une requête HTTP qui décrit son action, comme un verbe décrit souvent une action en français !

Par exemple "mangeons" tout seul, en partant du principe qu'one st au présent de l'indicatif, ça nous fait une belle jambe. Mais dire "Alice et moi mangeons une fondue" a bien plus de sens ! 

Eh bien en requêtes HTTP c'est pareil ! Dire "GET" tout seul ne nous avance à rien, il faut un contexte : quoi ? où ça ? comment ? par quel procédé ? Ça va dans une requête plus large que nous étudierons avec le back end ;-)

Toujours est-il qu'une requête API, qui va chercher un JSON, à un point de terminaison API, à votre avis, votre requête, elle aura quel verbe ? C'est ça, un GET ! Ça a l'alure d'un eendroit où on va chercher des infos, ça distribue des infos sous le meilleur format ? Donc c'est du GET !

#### 5.3.3. Les codes de statuts 

404 not found. On a tous lu ça un jour sur un site internet, quand on arrivait sur une page d'un site qui n'existe pas; dit autrement, un endpoint qui n'existe pas sur le site qu'on visite. Ou alors vous avez déjà vu le "403 access forbiden" où pour le coup vous arriviez sur une page qui existe... mais qui ne vous concerne pas ! 

Des pages comme ça, il y en a des tas, on appelle ça des **status codes**, et ce sont des réponses à des requêtes HTTP, et il y en a de différentes sortes :

* Informations responses (dans la tranche des 100)
* Successful responses (dans la tranche des 200)
* Redirection messages (dans la tranche des 300)
* Client error responses (dans la tranche des 400)
* Server error responses (dans la tranche des 500)

Les deux qui nous intéressent sont les Client et Server error responses, en d'autres mots, soit que le client a fait une mauvaise requête, ou sur quelque chose qui n'existe pas, ou non autorisée (un utilisateur lambda ne peut aps faire de requêtes avec certains verbes à certains endroits), soit que le serveur ne sait pas répondre à la requête. 

Je sais pas pour vous, mais je sens déjà la requête http(s) API asynchrone avec le verbe GET à un endpoint qui serait un point de terminaison API, et qui sera "try" ou "catch" ;-) Vous trouvez que j'insiste lourdement avec ces remarques ? Eh ben je fais exprès de répéter lourdement tous ces termes techniques pour que vous vous y familiarisiez !

#### 5.3.4. Les queryStrings, un approfondissement 

Maintenant que les choses commencent à se préciser sur la manière dont on utilisera la programmation asynchrone, revenons un peu sur les queryStrings, histoire de préciser une chose ou l'autre. 

Vous vous rappelez de ce lien ?

https://www.newbalance.be/fr/hommes/chaussures/made-in-uk-us/?prefn1=mensFootwearSize&prefv1=38.5&prefn2=refinementColor&prefv2=Beige

En fait vous pourriez reprendre la queryString et la coller n'importe où, ça ne changerait rien. Tant qu'un site n'est aps paramétré pour y répondre, aucun changement n'aura lieu. 

Regardez, je prends https://www.lemonde.fr et vais y coller ma queryString :

https://www.lemonde.fr/?prefn1=mensFootwearSize&prefv1=38.5&prefn2=refinementColor&prefv2=Beige

Si vous cliquez dessus, vous verrez bien que la page d'accueil du Monde se contrefout qu'on lui mette une queryString pour des chaussures, parce que le serveur sur lequel se trouve https://www.lemende.fr n'est pas paramétré pour prendre en compte de telles queryString. 

Maintenant essayons autre chose : sur le site de Mozilla, on va entrer une queryString manuellement et onv a chercher "chicken". 

https://developer.mozilla.org/en-US/search?q=chicken

Aussi improbalbe que ce soit, on a des résultats ! Ça veut dire que Mozilla a prévu qu'on puisse faire des requêtes GET, mais qu'on plus, le mot "chicken" apparait sur leur site ! 

Et maintenant, si on supprime "chicken" de l'url, va-t-on tomber sur un 404 ? 

https://developer.mozilla.org/en-US/search?q=

Pas dans ce cas ! Les développeurs de Mozilla ont prévu le coup avec une page d'erreur qui permet de tester sur le site !

Et si on tente par ailleurs l'endpoint improbable "swag" sur le site du Monde :

https://www.lemonde.fr/swag

On va aussi tomber sur une page d'erreur ! 

Et ce genre de rpatique tend à s'étendre. Est-ce que ça veut dire que le 404 error va disparaitre ? Non, loin de là, en lui-même, il a encore de beaux jours devant lui ! C'est la réponse par rapport à celui-ci qui change : on essaie de ne plus laisser les utilisateurs le bec dans l'eau. Dit autrement, une réponse "de rechange" automatique est prévue en cas de réponse négative ! 

#### 5.3.5. Les requête et headers HTTP

Nous continuons dans les sujets techniques et obscurs (pour le moment) en abordant la question des headers HTTP, qui sont partie intégrantes des requêtes. 

Si on récapitule, on commence à aborder ici des quesitons qui touchent au back end. La première fois c'était via les manipulations du DOM avec les forms, et ci, on aborde une question franchement plus orienté back. SI on ne parle aps encore de comment développer en back-end, on doit bien se rendre comtpe qu'on a déjà vu le front, vu tout ce qu'on peut faire potentiellement, et là, on est en réalité en traind e voir de  la théorie en liena vec des chsoes qu'on va manipuler.

L'étape suivante est de parler de l'en-tête des requêtes. On a vu jusqu'à rpésent que, quand on est sur un site et qu'on interragit avec, rien que pour y aller, one st en dialogue avec son serveur. Petit point supplémentaire, ce n''est pas aprce qu'on ne fait pas une interraction "explicite" (en cliquant, remplissant un champ, ou autre) qu'il n'y a pas des requêtes faites automatiquement par notre navigateur vers le site (ou pour être plus précis, son serveur), ou du site vers un autre. Et bien entendu, chaque reuête reçoit une réponse.

Le point qu'il faut aborder ici, c'est comment ces requêtes sont formulées, formalisées d'une manière précsie. 

Elles se composent de toris aprties :

* Ligne de requête, qui comprend la méthode HTTP/le verbe de la requête; le chemin de la ressource remandée; et enfin le protocole précis par lequel on fait cette demande (HTTP 1.1, 2, ...)
* le **Header**, qui comprend des paires clé-valeurs qui donne des précisions sur ce que l'on veut
* Enfin, un éventuel corps, dans le genre de cas où on serait dans du POST et que l'on voudrait entrer des informations, comme dans un formulaire.

Dans le **header**, on va reprendre toutes les infos concernant la requête, qu'on peut trouver en ouvrant l'inspecteur sur une page web, onglet "network". Ce qui va nous intéresser ici,c 'est surtout dans le "request header" ce qu'on accepte. Par défaut ce sera du html, css, de simages, ... bref, tout ce qu'il faut pour faire tourner notre site côté client !

Enfin, ça c'est pour une page normale ! Parce que, sur, certaines pages, des endpoint qui sont des points de terminaison API on peut retourner... des JSON, bien vu ! 

Et pour jour avec ça, on peut utliser POSTMAN, un service en ligne, qui, sur base d'un url dans un workplace, ermet de tester différents types de requêtes vers le serveur cotnenant un site, pour voir, entre autre, ce qu'il est capable de renvoyer. Par exemple, si on va dans l'onglet "headers" de POSTMAN et qu'on ajoute en dessous des paires déjà présentes dans le header la paire accept - application/json, certain sites renverront des JSON, vu que ce sont des API !

### 5.4 Le .fetch()

Nous voici arrivés au coeur de ce que l'on veut faire ! On a d'abord vu dans ce tuto la programmation asynchrone, puis on a abordé les différents types de requête. Avec un peu de logique, on comprend facilement que les requêtes client/navigateur vers el serveur doivent nécessairement assynchrone, pour ne pas bloquer le chargement de la page, comme ce serait le cas, si tout était synchrone : les échanges avec els erveur peuvent rendre un certain temps, et même si ce sont des dixièmes, centièmes de secondes, s'il y a en a beaucoup, ça peut rendre la navigation moins fluide et donc moins agréable, ce qui eprmet bien de réaliser une chose : si les requêtes API ont plus avoir avec le front que le back, leur nécessaire assynchronicité relève d'une logique front !

Donc, revenons à nos chères lignes de code, et dans une perspective front où l'API a été codée par un back sociopathe vivant dans sa cave (du moins, dans la perspective du font plutôt artiste), où dans un endpoint (en français, point de terminaison) API, on veut récupérer certaines infos, on va utiliser la fameuse méthode .fetch() !

Cette méthode va faire des deamndes à nos endpoint API asynchrones pour eux raisons :

1) Comme on a dit, pour éviter qu'on dialogue client/navigateur avec le serveur un peu trop long ne "freeze" temporairement le code
2) Comme on peut le deviner, aprce u'one  tpas certain que l'endpoint API nous renverra ce qu'on veut.

Ainsi ona  les deux dimensions de la la promesse : lancer des opérations après coup et tenir compte de l'éventualité d'une réponse négative. 

Mais quelle est donc la "recette" pour avoir un .fetch(); qui tient la route ? C'est ce que nous allons voir maintenant !

### 5.4.1 Décompasition du .fetch();

Pour bien comprendre le début, il faut se dire qu'en anglais "to fetch" signifie "aller chercher", et toute .fetch(); commecera fatalement comme ceci :

```javascript
fetch("https://catfact.ninja/fact");
```

On va chercher sur le site catfact.ninja/fact les infos voulus sous format JSON et on complète le code comme suit :

```javascript
fetch("https://catfact.ninja/fact");
  .then((response) => {
    console.log(response);
  })
  .catch((error) => {
    console.log(error);
  });
```

Et si on lance ce code dans la console de l'inspecteur de Chrome, on obtient :

```
Response {type: 'basic', url: 'https://catfact.ninja/fact', redirected: false, status: 200, ok: true, …}

body : (...)
bodyUsed : false
headers : Headers {}
ok : true
redirected : false
status : 200
statusText : ""
type : "basic"
url : "https://catfact.ninja/fact"
[[Prototype]] : Response
```

Bref, un objet "response" qui prend en comtpe TOUTES les informations que renvoie le serveur, et pas jsute le fun fact aatendu ! 

On retrouve les headers, la paire "ok - true" qui nous sert à savoir si on a bien reçu une réponse, le status qui nous renvoie comme rpévu 200, ... 

Le soucis, c'est qu'on a aps notre "fact" sur les chats ! Soit il faut creuser pour le retrouver, soit l'objet "response" l'a zappé, car se cocnentre trop sur le fait d'avoir eu les headers en réponse de la aprt du serveur, un peu comme un avion qui partirait une fois que les passagers ont montré leur ticket, mais en oubliant les bagages à l'aéroport...

Pour avoir la répons equ'on souhaite, il faut repasser notre objet dans un JSON ! En gros, on ait json --> objet "response" --> json !

On peut faire ça comme ça :

```javascript
fetch("https://catfact.ninja/fact")
  .then(response => response.json())
  .then(data => {
    console.log(data);
  })
  .catch(error => {
    console.log(error);
  });
```

Remarquez que la conversion sera aussi une promesse/asynchrone, vu qu'il faut *d'abord* récupérer l'objet response avant de le reconvertir !

Si vous voulez faire la même avec async/await :

```javascript
const fetchData = async () => {
  const res = await fetch("https://catfact.ninja/fact");
  const data = await res.json();
  console.log(data);
};
```

Dernière astuce, .ftech(); peut prendre deux arguments : d'abord l'url où chercher des infos, mais aussi un objet où on peut spécifier tout un tas de paramètres sur notre réponse : le type de requête, le format de réponse souhaité, ... car certaines API proposent un éventail bien plus large de méthodes leur étant applicable/le format souhaité pour els réponses à ces requêtes. 

Exeple :

```
fetch("https://catfact.ninja/fact", {
  method: "GET",
  headers: {
    "Content-Type": "application/json",
  }, // etc.
});
```