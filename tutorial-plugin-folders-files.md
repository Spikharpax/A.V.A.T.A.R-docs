# Les répertoires et les fichiers

## Les répertoires

Lors de son lancement, l'application recherche automatiquement les plugins dans le répertoire suivant:

``` hl_lines="5"
A.V.A.T.A.R
    ├── resouces
        ├── app
            ├── core
                ├── plugins
```

=== "Serveur"

    | <div style="width:95px">Répertoire</div>| <div style="width:10px">Création auto(1)</div>| <div style="width:20px">Obligatoire<div> | Description|
    |----------------------------|:---:|:---:| ------------------------------------|
    | `assets`                  | oui               | oui   | Contient:<br>- Le fichier d'information **obligatoire** `ìnfos.md` ou `infos_<application language>.md` au format [Markdown](https://www.markdownguide.org/basic-syntax/) apparaissant dans l'onglet `Ìnformation` du plugin dans _Plugin Studio_. Ce fichier est à modifier ensuite pour y ajouter les informations nécessaires à l'utilisation du plugin.<br><br>- Un répertoire **non obligatoire** `images` pour les images du plugin. Une création par _Wigdet Studio_ y dépose automatiquement le fichier image `<myPlugin>.png` choisi. Si le répertoire `images` n'existe pas, une image par défaut est affichée.|
    | `documentation`           | oui               | non   | Contient:<br>- Le fichier `documentation.ini` de configuration de la documentation.<br>- Déposez la documentation html que vous pouvez créer dans ce répertoire.|
    | `locales`                 | oui               | non   | Si vous localisez les messages du plugin, créez ce répertoire et déposez vos fichiers `<lang>.pak` dans ce répertoire.<br>Une création par _Wigdet Studio_ y dépose automatiquement un fichier `<lang>.pak` du langage courant.|        
    | `node_modules`            | non               | non   | Répertoire automatiquement créé si vous ajouter des modules npm dans votre plugin.|
    | `...`                     | N/A               | non   | Tout autre répertoire utile à votre plugin.|


    (1) Répertoires créés automatiquement si vous utilisez _Plugin Studio_ pour créer le plugin

=== "Client"

    | <div style="width:95px">Répertoire</div>| <div style="width:20px">Obligatoire<div> | Description|
    |----------------------------|:---:| ------------------------------------|
    | `assets`                  |  oui   | Contient:<br>- Le fichier d'information **obligatoire** `ìnfos.md` ou `infos_<application language>.md` au format [Markdown](https://www.markdownguide.org/basic-syntax/) apparaissant dans l'onglet `Ìnformation` du plugin dans _Plugin Studio_. Ce fichier est à modifier ensuite pour y ajouter les informations nécessaires à l'utilisation du plugin.<br><br>- Un répertoire **non obligatoire** `images` pour les images du plugin. Si le répertoire `images` n'existe pas, une image par défaut est affichée.|
    | `locales`                 | non   | Si vous localisez les messages du plugin, créez ce répertoire et déposez vos fichiers `<lang>.pak` dans ce répertoire.
    | `node_modules`            |  non   | Répertoire automatiquement créé si vous ajouter des modules npm dans votre plugin.|
    | `...`                     | non   | Tout autre répertoire utile à votre plugin.|



???+ note "Attention"
    Si vous décidez de créer vous-même les répertoires (obligatoires ou non), utilisez la même convention d'écriture.
///


## Les fichiers

=== "Serveur"

    |Type|<div style="width:95px">Fichier</div>|Création<br>auto(1)| Obligatoire | Description|
    |:---:|:-----------                |:-:              |:-:          | :------------------------------------|
    |Intention| `intent.<myPlugin>.js`    | oui               | non   | Intent permet de vérifier si la phrase que vous prononcez valide une règle vocale définie pour le plugin. <details class="note"><summary>Note</summary><p style="font-size: 12px">Il est possible d'avoir plusieurs fichiers d'intention pour un plugin. Voir développement avancé pour plus de détails.</p></summary></details>|
    |Action| `action.<myPlugin>.js`    | oui               | non   | Si une règle vocale est résolue par le fichier d'intention alors le fichier _action_ associé est appelé.<br>Le fichier _action_ est un pré-traitement qui rassemble des informations puis appelle le fichier _script_ du plugin.<details class="note"><summary>Note</summary><p style="font-size: 12px">Il est possible d'avoir plusieurs fichiers d'actions pour un plugin. Voir développement avancé pour plus de détails.</p></summary></details>|        
    |Script| `<myPlugin>.js`           | oui               | oui   | Fichier script principal dans lequel le plugin est développé.|
    |Propriétés| `<myPlugin>.prop`         | oui               | oui   |Le fichier de propriétés du plugin au format `json`.|
    |Package| `package.json`            | oui               | non   |Fichier des packages `npm`.|
    |Language| `locales/<language>.pak`            | oui               | non   |Fichier de localisation de langage.|
    |Information| `assets/info.md` <br>ou<br> `infos_<application language>.md`| oui | oui   |Fichier d'information au format [Markdown](https://www.markdownguide.org/basic-syntax/) qui sera affiché dans la page d'information du plugin dans _Plugin Studio_.<br><br>_application language_: Code court du [langage de l'application](server-properties.md#application-language)<br>Si ce fichier existe alors il est utilisé sinon `info.md` est utilisé|
    |Image| `assets/<plugin>.png`            | oui               | non   |Fichier image du plugin au format `.png`.|
    |...| `...`                     | N/A               | non   | Tout autre fichier utile à votre plugin.|

    (1) Fichiers créés automatiquement si vous utilisez _Plugin Studio_ pour créer le plugin

=== "Client"

    |Type|<div style="width:95px">Fichier</div>| Obligatoire | Description|
    |:---:|:-----------                |:-:          | :------------------------------------|    
    |Script| `<myPlugin>.js`           |  oui   | Fichier script principal dans lequel le plugin est développé.|
    |Propriétés| `<myPlugin>.prop`         |  oui   |Le fichier de propriétés du plugin au format `json`.|
    |Package| `package.json`            | non   |Fichier des packages `npm`.|
    |Language| `locales/<language>.pak`            |  non   |Fichier de localisation de langage.|
    |Information| `assets/info.md`            |  oui   |Fichier d'information au format [Markdown](https://www.markdownguide.org/basic-syntax/) qui sera affiché dans la page d'information du plugin dans _Plugin Studio_.|
    |Image| `assets/<plugin>.png`            |  non   |Fichier image du plugin au format `.png`.|
    |...| `...`                     |  non   | Tout autre fichier utile à votre plugin.|


<br><br>
[:material-skip-previous: Créer un plugin](tutorial-plugin-studio.md){ .md-button style="float:left;"}[Traitement Naturel du Langage:material-skip-next:](tutorial-nlp.md){ .md-button style="float:right;"}   
<br><br>