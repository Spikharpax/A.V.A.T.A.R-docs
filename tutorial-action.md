# L'action

/// tip | Ceci est la **partie 6** du tutoriel
///

Le fichier _action_ est un pré-traitement qui rassemble des informations puis appelle le fichier _script_ du plugin.

## L'action basique

Si l'intention est résolue de manière satisfaisante, l'action proposée par la création d'un plugin par [_Plugin Studio_](tutorial-plugin-studio.md) couvre généralement 90% des besoins.  

- `Ligne 5`: Temporisation en milli-secondes pour exécuter le script (Propriétés _synchronisation des actions_, par défaut 0).
- `Ligne 7`: Ajout de `module` dans `state.action`.
- `Ligne 8`: Ajout de `command` dans `state.action`.
- `Ligne 10`: Appel du script associé avec en paramètre l'objet `state`.
    
```js linenums="1"
import {default as _helpers} from '../../ia/node_modules/ava-ia/helpers/index.js'

export default function (state) {
    return new Promise((resolve) => {
        setTimeout(() => { 
            state.action = {
                module: 'myFirstPlugin',
                command: state.rule
            };
            resolve(state);
        }, Config.waitAction.time);
    });
};
```
## L'action personnalisée

Les valeurs possibles de `state.action`:

|Object|Type |Obligatoire|Description|
|:-----|:---:|:----------:|:----------|
|`module` |string| non   | Le nom du plugin à appeler.<br><br>Si `module` n'est pas défini:<br>- `tts` doit être présent.<br>- Le fichier script doit être présent mais ne sera pas exécuté.
|`command` |string| non | La méthode à exécuter.<br>La valeur `module`doit être présente.|
|`tts` |string<br>ou<br>array| non | Une phrase ou un tableau de phrases (dont une sera choisie aléatoirement) qu'A.V.A.T.A.R va prononcer **avant** d'appeler le script.  |
|`end` |boolean|non|`true` par défaut.<br>`true`: Après avoir prononcé la phrase `tts`, l'écoute sur le client est restituée.<br>`false`: Après avoir prononcé la phrase `tts`, l'écoute sur le client n'est pas restituée.|

La mise en forme d'une action n'est pas figée. C'est vous qui choisissez en fonction de la complexité de votre projet.
Il est tout à fait possible d'exécuter un pré-traitement avant l'appel du script, voir tout faire dans le fichier action, comme dans l'exemple d'un petit plugin `time` ci-dessous dans lequel il faut juste faire prononcer l'heure à A.V.A.T.A.R.

=== "Action"
    ``` js
    import moment from 'moment'
    import * as _helpers from '../../ia/node_modules/ava-ia/helpers/index.js'

    /** 
     * basic location of messages
     * not a good way, this is just for the example
     * prefer language.pak files for the location messages
     * @private
     */
    const msg = {
        'en':  {
            'sentence': 'it is',
            'hour': 'hour'
        },
        'fr': { 
            'sentence': 'il est',
            'hour': 'heure'
        }
    }

    /** 
     * @private
     * @param {string} lang - short language code
     * @return {object} messages
     */
    function getValues (lang) {
        return { ...msg[lang] }
    }

    export default function (state) {
        return new Promise(async (resolve) => {
            moment.locale(Config.language)
            const tts = getValues(Config.language)
            const hour = moment().format(`[${tts.sentence}] H [${tts.hour}] m`)

            setTimeout(() => {
                state.action = {
                    tts: hour,
                    end: true
                }
                resolve(state);
            }, Config.waitAction.time)
        });
    };
    ```

=== "Script"
    ``` js
    export function action (data, callback) {
        callback()
    }
    ```

<br><br>
[:material-skip-previous: L'intention](tutorial-intention.md){ .md-button style="float:left;"}[Le script :material-skip-next:](tutorial-script.md){ .md-button style="float:right;"}   
<br><br>