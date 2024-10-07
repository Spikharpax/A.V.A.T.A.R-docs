# The intent

Comme nous avons commencé à l'aborder dans les chapitres précédents, pour valider une règle vocale A.V.A.T.A.R utilise des _intentions_ et des _actions_ qui sont des fonctions simples qui reçoivent un état et le renvoient avec une composition interne.

- `Intention`: La fonction que vous souhaitez attacher à une phrase.
- `Action`: Une fonction d'action qui sera appelée si l'intention est résolue de manière satisfaisante.

Nous pouvons nous le représenter graphiquement comme ceci:

``` mermaid
graph LR
  A[Phrase]
  A --> B{NLP};
  B --> C[Intention<br>plugin 1];
  B --> L[Intention<br>plugin x...];
  C --> F{résolu ?};
  F --> |Oui| G[Action<br>plugin 1]; 
  F --> |Non| I[Sortie]; 
  H[Propriétés<br>plugin 1] --> C;
  G --> J[Fichier Script<br>plugin 1];
  H --> G;
  H --> J;
  J --> K[Methode action];
```

## Les méthodes de résolution
### Par la syntaxe de la phrase
#### `helpers.syntax(sentence, terms)`
* `sentence` string - La phrase `state.sentence` (_en anglais_).
* `terms` array - Collection de termes (_en anglais_) 

Si un des termes est validé alors l'intention est résolue. 

=== "intent.<_plugin_>.js"
    - `Ligne 6`: Boucle sur les tableaux de termes de l'objet `rules`
    - `Ligne 7`: Si la syntaxe est validée, sortie de la boucle
    - `Ligne 13`: Ajout de `rule` dans l'objet `state`
    - `ligne 14`: Appel de l'action associé

    ```js linenums="1"
    import {default as _helpers} from '../../ia/node_modules/ava-ia/helpers/index.js'

    export default async function (state, actions) {
        if (state.isIntent) return (0, _helpers.resolve)(state)
        
        for (var rule in Config.modules.myFirstPlugin.rules) {	 
            var match = (0, _helpers.syntax)(state.sentence, Config.modules.myFirstPlugin.rules[rule])
            if (match) break
        }
        
        if (match) {
            state.isIntent = true
            state.rule = rule
            return (0, _helpers.factoryActions)(state, actions)
        } else 
            return (0, _helpers.resolve)(state)
    };
    ```

=== "<_plugin_>.prop"
    
    ```json linenums="1"
    {
        "modules": { 
            "myFirstPlugin": {
                "version": "1.0",
                "name": "My first plugin",
                "rules": {
                    "test": ["test * (command|order)"]
                }
            }
        }
    }
    ```

### Par un terme dans la phrase
#### `helpers.intersect(terms, tokens)`
* `terms` array - Collection de termes.
* `tokens` array - Les tokens de la phrase `state.sentence` (_en anglais_).

Si un des tokens est validé alors l'intention est résolue. 

=== "intent.<_plugin_>.js"
    - `Ligne 6`: Boucle sur les tableaux des tokens de l'objet `rules`
    - `Ligne 7`: Si un token est validé, sortie de la boucle
    - `Ligne 12`: Ajout de `rule` dans l'objet `state`
    - `ligne 13`: Appel de l'action associé

    ```js linenums="1"
    import {default as _helpers} from '../../ia/node_modules/ava-ia/helpers/index.js'

    export default async function (state, actions) {
        if (state.isIntent) return (0, _helpers.resolve)(state)
        
        for (var rule in Config.modules.myFirstPlugin.rules) {	 
          var match = (0, _helpers.intersect)(Config.modules.myFirstPlugin.rules[rule], state.tokens) 
          if (match) break
        }

        if (match) {
            state.isIntent = true
            state.rule = rule
            return (0, _helpers.factoryActions)(state, actions)
        }  else 
            return (0, _helpers.resolve)(state)
    };
    ```

=== "<_plugin_>.prop"
    ```json linenums="1"
    {
        "modules": { 
            "myFirstPlugin": {
                "version": "1.0",
                "name": "My first plugin",
                "rules": {
                    "test": ["1", "one"]
                }
            }
        }
    }
    ```

??? info
    Le tableau `state.tokens` est composé **automatiquement** avec la phrase (_en anglais_) **avant** l'appel de l'intention (voir le [chapitre précédent](tutorial-nlp.md "Traitement Naturel du Langage"))

    Pour la phrase "what time is it",  l'objet `state.tokens` est :

    ```js
    state.tokens: [ 'what', 'time', 'is', 'it' ]
    ```

## L'intention simple
La validation de l'intention proposée par la création automatique d'un plugin par [_Plugin Studio_](tutorial-plugin-studio.md "Plugin Studio") est une résolution simple de la phrase dans les tableaux de l'objet `rules` du fichier de propriétés du plugin. Cette validation couvre généralement 90% des besoins.


## L'intention personnalisée

La mise en forme d'une intention n'est pas figée. C'est vous qui choisissez en fonction de ce que vous voulez réaliser. Par exemple, un tableau d'objet peut renvoyer vers un autre tableau d'objet du fichier de propriétés de votre plugin où sont définis les règles et des propriétés nécessaires à l'exécution de l'action.

Voici un exemple de résolution d'une intention légèrement plus complexe pour un projet de domotique. Supposons que nous voulons récupérer toutes les informations nécessaires à une action en validant une phrase qui peut être identique à plusieurs périphériques avec les phrases suivantes :

```
- allume|éteint la lumière dans le Salon
- allume|éteint la lumière dans la cuisine
```    

Nous pouvons comprendre que l'ID du périphérique est différent en fonction de chaque pièce et la valeur à passer au périphérique pour l'action aussi, ce qui donne: 

    - ID Salon: 1151500
    - ID Cuisine: 2664444
    - valeur Off: 0
    - valeur On: 100

Nous pouvons définir le diagramme simplifié suivant pour l'exemple (phrase traduite en anglais) _switch on the light in the Lounge_:

``` mermaid
graph LR
  A[Sentence]
  A --> B[for<br>intents<br>objects];
  B --> |key1: switch| J[for<br>ruleGroups.key1.value];
  B --> |key1: temperature| J;
  B --> |key1: tv| J;
  J --> |value1: Lounge switch| C[for<br>ruleGroups.key1];
  J --> |value1: Kitchen switch| C;
  C --> |key2: 0| F{resolve ?};
  F --> |no| I[none]
  C --> |key2: 100| G{resolve ?};
  G --> |yes| H[state.command = ruleGroups.key1.command<br>state.value = ruleGroups.key1.key2<br>state.answer=ruleGroups.key1.answer<br>state.periph=clients''clientTo''.value1];
```

Prenons le temps d'étudier le diagramme en association avec les fichiers _intent.<plugin\>.js_ et _<plugin\>.prop_:


=== "intent.<_plugin_>.js"
    ``` js
    var match, command, type, value, periph, answer
    var tbl = Config.modules.myFirstPlugin.intents

    // Searches for the room for which the rule is executed
    // - sentence: switch on the light in the Lounge
    // - return: Lounge
    const clientTo = Avatar.Socket.currentClient(state.client, state.rawSentence)

    for (var i=0 ; i < tbl.length && !match; i++) {
      for (value in Config.modules.myFirstPlugin.ruleGroups[tbl[i]]) {
        if (value !== 'command' && value !== 'answer') {

          match = (0, _helpers.syntax)(state.sentence, Config.modules.myFirstPlugin.ruleGroups[tbl[i]][value])

          if (match) {
              command = Config.modules.myFirstPlugin.ruleGroups[tbl[i]].command 
              ? Config.modules.myFirstPlugin.ruleGroups[tbl[i]].command 
              : false

              answer = Config.modules.myFirstPlugin.ruleGroups[tbl[i]].answer 
              ? Config.modules.myFirstPlugin.ruleGroups[tbl[i]].answer 
              : false

              type = tbl[i]
              _.map(Config.modules.myFirstPlugin.intents[type], num => { 
                if (Config.modules.myFirstPlugin.clients[clientTo][num]) {
                  periph = Config.modules.myFirstPlugin.clients[clientTo][num]
                }	
              })
              break;
          }
        }
      }
    }

    if (match) {
        state.isIntent = true
        state.command = command
        state.periph = periph ? periph : false
        state.value = value ? value : false
        state.answer = answer ? answer : false
        return (0, _helpers.factoryActions)(state, actions)
    } else 
        return (0, _helpers.resolve)(state)
    ```

=== "<_plugin_>.prop"
    ``` json
    {
      "modules":{
        "myFirstPlugin":{
          "name":"myFirstPlugin",
          "ruleGroups":{
            "switch":{
              "0":["turn off * light","extins * light"],
              "100":["turn on * light","light * light"],
              "command":"set",
              "answer":"magic!"
            },
            "temperature":{
              "command":"get",
              "get":["give * temperature"]
            },
            "tv":{
              "0":["turn off * tv","off * tv","tv * off"],
              "100":["turn on * tv","turne on * tv"],
              "command":"set"
            }
          },
          "clients":{
            "Lounge":{
              "Lounge switch":"1151500",
              "Temperature Lounge":"152764",
              "Lounge TV jack":"1982673",
            },
            "Kitchen":{
              "Kitchen switch":"2664444"
            },
            "Bedroom":{
              "Bedroom TV jack":"483087"
            }
          },
          "intents":{
            "switch":["Lounge switch","Kitchen switch"],
            "temperature":["Temperature Lounge"],
            "tv":["Lounge TV jack","Bedroom TV jack"]
          }
        }
      }
    }
    ```

:flashlight:
Nous pouvons voir que dans cet exemple que l'intention est vérifiée et que plusieurs valeurs en relation sont récupérées et ajoutées à l'objet `state`

## Que faut-il retenir ?
1. Choisissez bien le type d'intention que vous voulez pour votre plugin. Sauf cas spécifique, la validation par la syntaxe de la phrase est la plus utilisée.
2. Rien n'est figé. Vous pouvez modifier le fichier _intent.myFirstPlugin.js_ comme vous voulez. **L'important est de valider l'intention !**

<br>
[:material-skip-previous: Traitement Naturel du Langage](tutorial-nlp.md){ .md-button style="float:left;"}[L'action :material-skip-next:](tutorial-action.md){ .md-button style="float:right;"}   
<br><br>