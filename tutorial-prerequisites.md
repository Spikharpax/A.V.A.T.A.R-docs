# Prérequis

## Objectifs
Ce tutoriel vous guidera à travers le processus de développement.  
Les [exemples](developer-examples.md) et la documentation de l'[API serveur](server-API.md) et l'[API client](client-API.md) sont également des chapitres intéressants pour apprendre à développer un plugin.

## Hypothèses
Un plugin est une surcouche A.V.A.TA.R et est exécuté dans un environnement [Node.js](https://en.wikipedia.org/wiki/Node.js). Par conséquent, développer un plugin suppose que vous êtes complètement familiarisé avec Node, npm, le javascript et les bases du développement Web coté front. Si vous avez besoin de faire des lectures préliminaires sur le sujet avant de vous lancer, nous vous recommandons les ressources suivantes :

* [Premiers pas avec le Web (MDN Web Docs)](https://developer.mozilla.org/en-US/docs/Learn)
* [Introduction à Node.js](https://nodejs.org/en/learn/getting-started/introduction-to-nodejs)


## Bonne pratiques

Il est préférable d'avoir une orientation de développement avant de créer un plugin, celui-ci sera soit:

- Pour un usage personnel
- Ajouté à la bibliothèque d'A.V.A.T.A.R pour un usage public

Si c'est une démarche personnelle, vous serez seul à l'utiliser et vous pouvez donc ignorer ce paragraphe.  
Par contre, si vous faites le choix de faire profiter vos plugins à la communauté, il convient de respecter certaines conventions:

- **Localisez le plugin**: A.V.A.T.A.R est multi-langages, localisez au minimum la langue courante afin de prévoir la traduction d'un pack de langue existant si besoin.
- **Ajoutez une documentation**: Cette partie est toujours négligée mais elle est pourtant nécessaire pour l'utilisation du plugin. Suivant la complexité de la documentation, vous avez le choix entre :
    - Ajouter une page d'information au format [Markdown](https://www.markdownguide.org/basic-syntax/) qui sera affichée dans la page d'information du plugin dans _Plugin Studio_
    - Ajouter une document HTML qui pourra être afficher dans un navigateur internet.

    Notez que dans les 2 cas, il est préférable de l'écrire en Anglais.

- **Pensez multiroom**: A.V.A.T.A.R est une application client-serveur, pensez à faire en sorte que le plugin soit toujours fonctionnel même si le client n'est pas installé sur la même plateforme que le serveur.
    - Par exemple, une erreur classique est d'utiliser l'application `ffplay` du serveur alors que la musique doit être jouée sur un client.
- **Pensez multiplateforme**: A.V.A.T.A.R fonctionne sous les plateformes Windows, Linux et MacOS. Il existe certaines particularités entre les systèmes, vérifiez toujours que les conditions particulières à chaque plateforme soient respectées si des applications spécifiques doivent être utilisées. Si vous ne pouvez pas savoir si le plugin est fonctionnel sur une plateforme, notifiez-le dans la documentation.


## Plugin client

En plus du serveur, il est possible de créer des plugins sur chaque client A.V.A.T.A.R.  
Un plugin client est généralement utilisé pour réaliser des actions propres au client qui ne peuvent pas être effectuées avec les API depuis un plugin du serveur ou encore utilisé pour ajouter des wigdets boutons dans l'interface du client.

???+ note "Rappel"
    Comme nous l'avons vu, il n'est pas possible de créer un plugin par le _Plugin Studio_ d'un client.

    Pour créer un plugin pour un client :

    1. Créer le plugin par _Plugin Studio_ sur le serveur 
    2. Copier ensuite le plugin sur le client dans le répertoire _plugins_


## Outils nécessaires
### Éditeur de code
Vous aurez besoin d’un éditeur de texte pour écrire votre code. Nous vous recommandons d’utiliser [Visual Studio Code](https://code.visualstudio.com/), mais vous pouvez bien sur choisir celui que vous préférez.


### GitHub
GitHub est une plate-forme de développement collaborative construite sur Git. Bien que ni l’un ni l’autre ne soit strictement nécessaire pour créer un plugin, gérer les versions de vos codes sources est indispensable. De plus nous utilisons les "releases" des projets GitHub (au format A.V.A.T.A.R seulement) pour les afficher dans la bibliothèque de plugins. 

Par conséquent, si vous n'avez pas de compte GitHub, [Créer un compte GitHub](https://github.com/signup) maintenant.
Vous pouvez ensuite utiliser l’application [GitHub Desktop](https://desktop.github.com/) pour gérer vos projets si vous préférez utiliser une interface visuelle à la place de la ligne de commande.

??? note
    Si vous n'êtes pas **contributeur**, les autres utilisateurs ne voient pas vos projets et ne peuvent pas les télécharger par la bibliothèque de plugins A.V.A.T.A.R. Vos projets GitHub A.V.A.T.A.R. vous resteront personnels et seront affichés dans votre bibliothèque **uniquement**. 

    Veuillez noter que pour devenir un contributeur à usage public, vous devez déposer une demande. Vous serez alors ajouté en tant que contributeur et les utilisateurs pourront utiliser vos plugins.


### nodejs et npm
Bien que le framework [Electron](https://www.electronjs.org/) intègre [node.js](https://nodejs.org/) dans son exécutable et que par conséquent, il n'est théoriquement pas nécessaire d'installer nodejs et npm, pour A.V.A.T.A.R et pour développer un plugin, cette installation est nécessaire.  

Cette étape est réalisée pendant l'installation des [prérequis](introduction.md#prerequisites) de l'installation du serveur et des clients.  

## Package npm 

Développer un plugin suppose que vous allez utiliser des paquets npm dans votre application.  
Certains de ces paquets sont accessibles par défaut dans node.js, d'autres depuis les paquets déjà installés dans A.V.A.T.A.R mais vous pouvez aussi installer des paquets propres à votre plugin.

1. Vérifiez d'abord si le package n'est pas un module inclu dans node.js
    - Si celui-ci existe, vous pouvez l'importer dans votre plugin :
    ```js
    import * as path from 'node:path';
    ```
1. Ouvrez ensuite le fichier _A.V.A.T.A.R/resources/app/package.json_ dans un éditeur de texte
    - Faites une recherche du module qui vous intéresse
    - Si celui-ci existe, vous pouvez l'importer dans votre plugin :
        ```js
        import _ from 'underscore';
        ```
- Si celui-ci n'existe pas :
        - Ouvrez un terminal
        - Déplacez-vous dans le répertoire de votre plugin
            ``` hl_lines="6"
            A.V.A.T.A.R (client ou serveur)
                ├── resouces
                    ├── app
                        ├── core
                            ├── plugins
                                ├── Votre plugin
            ```
        - Installez le paquet dans le répertoire du plugin
            ``` hl_lines="6"
            > npm install nom_du_paquet
            ```
        -  Vérifiez l'installation du paquet
            ``` hl_lines="6"
            A.V.A.T.A.R
                ├── resouces
                    ├── app
                        ├── core
                            ├── plugins
                                ├── Votre plugin
                                    ├── node_modules
                                        ├── nom_du_paquet
                                    ├── package.json
            ```

            /// note
            Si vous avez créé le plugin par [Plugin Studio](tutorial-plugin-studio.md), un fichier _package.json_ est automatiquement ajouté dans son répertoire. Le module est ajouté dans le fichier après son installation.
            ///

/// danger
N'installez jamais un paquet dans les modules d'A.V.A.T.A.R client ou serveur !
///


## Terminal de développement et débogage 

Pendant un développement, on préférera démarrer/arrêter le serveur (ou le client) plus rapidement depuis un terminal.  
On préférera aussi utiliser des _console.log()_ pour afficher des informations utiles et les messages d'erreurs.  

1. Ouvrez un terminal
2. Déplacez-vous dans le répertoire _app_ de l'application 
    ``` hl_lines="3"
    A.V.A.T.A.R
        ├── resouces
            ├── app
    ```
3. Démarrez l'application par npm
    ``` hl_lines="1"
    > npm start .
    ```
    ![](img/electron-start.png){width="550"}

4. VVous disposez maintenant d'une console npm pour démarrer/arrêter et consulter les messages.

<br>
[Créer un plugin :material-skip-next:](tutorial-plugin-studio.md){ .md-button style="float:right;"}   
<br><br>