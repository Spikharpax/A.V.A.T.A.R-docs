# Le script

/// tip | Ceci est la **partie 7** du tutoriel
///

Le fichier Script est le point d'entrée pour développer un plugin.

## Les Méthodes

Ci-dessous les méthodes de plugin appelées automatiquement par A.V.A.T.A.R (par exemple, au démarrage ou pendant l'exécution du script). Certaines de ces méthodes sont créées automatiquement si vous créez le plugin par [Plugin Studio](tutorial-plugin-studio.md)

### `action(data, callback)`

Cette méthode **obligatoire** est le point d'entrée du plugin.  
Elle est **toujours** appelée après le fichier _action.<plugin\>.js_ ou directement si le plugin n'a pas de règles vocales.  
Retourne **obligatoirement** la fonction `callback`.

* `data` object - L'objet envoyé depuis le fichier action.<_plugin_\>.js
    * `client` string - Le client A.V.A.T.A.R qui a envoyé la phrase
    * `toClient` string - Le client (réel ou virtuel) qui est dans la phrase
    * `rawSentence` string - La phrase non traduite
    * `sentence` string - La phrase traduite en Anglais 
    * `language` string - Le code court du langage utilisé sur le client pour `rawSentence`
    * `tags` array - Les balises de la phrase traduite en anglais
    * `tokens` array - tokens de la phrase traduite en anglais
    * `relations` object - La relation trouvée entre l'objet et l'action de la phrase traduite en anglais
    * `action` object - L'object défini dans le fichier action.<_plugin_\>.js
* `callback` _null_ ou fonction de callback si le plugin est appelé par `Avatar.run()` ou `Avatar.call()` provenant d'un autre plugin avec une fonction de callback.

:flashlight:
Voir la section [Traitement Naturel du Langage](tutorial-nlp.md) pour plus de détails sur les objets `tags`, `tokens` et `relations`.

```js
export async function action(data, callback) {

	// Table of actions
	const tblActions = {
		// test (see rules table in the property file)
		doIt: () => doIt(data.client)					
	}
	
	// Writes info console
	info("test:", data.action.command, L.get("plugin.from"), data.client)
		
	// Calls the function that should be run
  	tblActions[data.action.command]()
  	
	// Returns callback
	callback()
 
}
```

### `init()`

Cette méthode optionnelle est exécutée au chargement du plugin pendant l'initialiation d'A.V.A.T.A.R.  
Utile pour charger des informations nécessaires au plugin, par exemple pour charger les fichiers `.pak` de langages:

```js
export async function init() {
    if (!await Avatar.lang.addPluginPak("myFirstPlugin")) {
        error('myFirstPlugin: unable to load language pak files')
        return
    }
}
```

### `cron()`

* `interval`: `2 heures` par défaut à la création du plugin par _Plugin Studio_.

Cette méthode optionnelle est appelée à interval régulier par l'application. L'interval est à définir ensuite plus précisément dans le fichier de propriétés _myPlugin.prop_.  

=== "Méthode"
    ```js
    export async function cron () {

        // Says Hello every 2 hours
        info ('hello from myPlugin !")

    }
    ```

=== "myPlugin.prop"
    ```json hl_lines="11-16"
    {
	"modules": { 
		"myPlugin": {
			"version": "1.0",
			"name": "My Plugin",
			"rules": {
				"test": ["test * (command|order)"]
			}
		}
	},
	"cron": {
		"myPlugin": { 
			"name": "myPlugin",
			"time": "0 * */2 * * *"
		}
	}
    ```
}

#### Syntaxe

```
- `*` Asterisks: Any value
- `1-3,5` Ranges: Ranges and individual values
- `*/2` Steps: Every two units
```

Plages de valeurs prises en charge:

```
field          allowed values
-----          --------------
second         0-59
minute         0-59
hour           0-23
day of month   1-31
month          1-12 (or names, see below)
day of week    0-7 (0 or 7 is Sunday, or use names)


Names can also be used for the 'month' and 'day of week' fields.  
Use the first three letters of the particular day or month (case does not matter).  
Ranges and lists of names are allowed.
Examples: "mon,wed,fri", "jan-mar".
```

### `onClose()`

Cette méthode optionnelle est appelée juste **avant** qu'A.V.A.T.A.R soit fermé ou redémarré.  
Utile pour sauvegarder des informations du plugin par exemple :

```js
export async function onClose () {
  
    return new Promise(resolve => {

        let propertyFile = path.resolve(__dirname, 'credentials/' + Config.modules['myPlugin'].authorization.credentials)
        fs.writeJsonSync(propertyFile, property)

        resolve()
    });
  
}   
```


### `mute(clientFrom, clientTo)`

* `clientFrom`: string - Le client A.V.A.T.A.R qui a envoyé la règle.
* `clientTo`: string - le client A.V.A.T.A.R qui doit exécuter la règle, identique à clientForm ou virtuel 

Cette méthode optionnelle est appelée si le client qui transmet la règle est en mode **loop** juste après le mot-clé déclencheur ou en mode **question/réponse (askme)**.  
Permet d'effectuer une action telle que la mise en sourdine d'un appareil (TV ou autre) pendant le dialogue.

=== "Exemple 1"

    * Conditions préalables : 
        * La chambre à coucher est un client virtuel de Salon.
        * Le client Salon est en mode **loop** ou **askme**

    ```
    règle du client Salon: allume la lumière dans la chambre à coucher 
    * clientFrom: Salon 
    * clientTo: Chambre à coucher
    ```

=== "Exemple 2"

    * Conditions préalables : 
         * La chambre à coucher n'est pas un client virtuel de Salon.
        * Le client Salon est en mode **loop** ou **askme**

    ```
    règle du client Salon: allume la lumière dans la chambre à coucher 
    * clientFrom: Salon 
    * clientTo: Salon
    ```

=== "Exemple 3"

    * Conditions préalables : 
        * La chambre à coucher n'est pas un client virtuel de Salon.
        * Le client Salon n'est pas en mode **loop**
        * Le dialogue est pendant un **askme**

    ```
    règle du client Salon: allume la lumière dans la chambre à coucher 
    * clientFrom: Salon 
    * clientTo: Salon
    ```


### `unmute(clientFrom, clientTo)`

* `clientFrom`: string - Le client A.V.A.T.A.R qui a envoyé la règle.
* `clientTo`: string - le client A.V.A.T.A.R qui doit exécuter la règle, identique à clientForm ou virtuel 

Cette méthode optionnelle est appelée si le client qui transmet la règle est en mode **loop** juste après la fin d'un dialogue ou en mode question/réponse.  
Permet d'effectuer une action, telle que remettre le son d'un appareil (TV ou autre) après le dialogue.

:flashlight: **Remarque:** Cette méthode est généralement utilisée en association avec la méthode `mute`

=== "Exemple 1"

    * Conditions préalables : 
        * La chambre à coucher est un client virtuel de Salon.
        * Le client Salon est en mode **loop** ou **askme**

    ```
    règle du client Salon: allume la lumière dans la chambre à coucher 
    * clientFrom: Salon 
    * clientTo: Chambre à coucher
    ```

=== "Exemple 2"

    * Conditions préalables : 
        * La chambre à coucher n'est pas un client virtuel de Salon.
        * Le client Salon est en mode **loop** ou **askme**

    ```
    règle du client Salon: allume la lumière dans la chambre à coucher 
    * clientFrom: Salon 
    * clientTo: Salon
    ```

=== "Exemple 3"

    * Conditions préalables : 
        * La chambre à coucher n'est pas un client virtuel de Salon.
        * Le client Salon n'est pas en mode **loop**
        * Le dialogue est pendant un **askme**

    ```
    règle du client Salon: allume la lumière dans la chambre à coucher 
    * clientFrom: Salon 
    * clientTo: Salon
    ```

### `subclassSpeak()`

Cette méthode optionnelle est exécutée au chargement du plugin pendant l'initialiation d'A.V.A.T.A.R.  
Permet de surclasser la méthode `Avatar.speak()` pour utiliser un système de son autre que celui du PC client pour les dialogues

:flashlight: **Remarque:** Cette méthode est appelée **automatiquement** et **uniquement** si le paramètre de redirection des dialogues est activé sur le client (voir Client --> paramètres --> Dialogue). Un client peut donc rediriger ses dialogues sur une autre enceinte alors qu'un autre où ce paramètre n'est pas activé, utilisera l'enceinte de son PC.


```js
export async function subclassSpeak() {
	
    // If you wish, you can save the default Avatar.speak function for your own use.  
    let defaultSpeak = Avatar.speak;
    
    Avatar.speak = () => {

        // Do stuff

    }
}
```


### `subclassPlay()`

Cette méthode optionnelle est exécutée au chargement du plugin pendant l'initialiation d'A.V.A.T.A.R.  
Permet de surclasser la méthode `Avatar.play()` pour utiliser un système de son autre que celui du PC client pour les fichiers de musiques et sons.

:flashlight: **Remarque:** Cette méthode est appelée **automatiquement** et **uniquement** si le paramètre de redirection des dialogues est activé sur le client (voir Client --> paramètres --> Dialogue). Un client peut donc rediriger ses dialogues sur une autre enceinte alors qu'un autre où ce paramètre n'est pas activé, utilisera l'enceinte de son PC.

```js
export async function subclassPlay() {
	
    // If you wish, you can save the default Avatar.play function for your own use.  
    let defaultPlay = Avatar.play;
    
    Avatar.play = () => {

        // Do stuff

    }
}
```


<br><br>
[:material-skip-previous: L'action](tutorial-action.md){ .md-button style="float:left;"}
<br><br>