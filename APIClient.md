# API Client

### `Config`

Object globale regroupant la configuration du client.

### `Config.modules`

Object regroupant la configuration des plugins. Cet objet est généralement utilisé dans les plugins pour récupérer une valeur de clé de propriété.

=== "<Plugin\>.prop"
    ```json
     {
        "modules": { 
            "myFirstPlugin": {
                "version": "1.0",
                "name": "my first plugin",
                "rules": {
                    "test": ["test * (command|order)"]
                }
            }
        }
    }
    ```
=== "<Plugin\>.js"
    ```js
     const name = Config.modules.myFirstPlugin.name;
    ```

## Message loggers

Logger methods are used to display messages in the A.V.A.T.A.R client console.

Possible data types for all loggers:
```js
const str = 'a string';
const bool = true;
const arr = ['one', 'two', 3];
const obj = {key: "value"};
const f = () => console.log('hello');
```

### `info(...message)`

Affiche un message d'information dans la console.

```js
info(str, bool, arr, obj, f);
// returns in the console: 
// info: a string true ["one","two",3] {"key":"value"} () => console.log('hello')
```

### `infoGreen(...message)`

Affiche un message d'information  en <span style="color:green">vert</span> dans la console.

### `infoOrange(...message)`

Affiche un message d'information  en <span style="color:orange">orange</span> dans la console.


### `warn(...message)`

Affiche un message d'avertissement en <span style="color:orange">orange</span> dans la console.

```js
warn(str, bool, arr, obj, f);
// returns in the console: 
// warn: a string true ["one","two",3] {"key":"value"} () => console.log('hello')
```

### `error(...message)`

Affiche un message d'erreur en <span style="color:red">rouge</span> dans la console 

```js
error(str, bool, arr, obj, f);
// returns in the console: 
// error: a string true ["one","two",3] {"key":"value"} () => console.log('hello')
```

### `infoConsole(...message)`

Affiche un message dans la console de développement de Chromium (cliquez sur F10 dans l'interface du client).

/// warning
Du fait de la mise en [bac à sable](https://www.electronjs.org/docs/latest/tutorial/sandbox) de Chromium, le rendu d'une fonction est impossible dans la console de développement.  
Afficher une fonction génèrera une erreur. Tous les autres types de données sont possibles.
///

```js
infoConsole(str, bool, arr, obj);
```
![infoConsole](img/infoConsole.png){width="350"}


## Dialog

### `Avatar.speak(tts[,callback, end, voice, volume, speed, pitch])`

Exécute un tts sur le système audio (enceinte du client ou autre système déclaré).

| paramètre| type| Obligatoire | Default value| Values | <div style="width:300px">Description</div>|
|:----------|:---:|:-----------:|:-----------|----------- |-----------|
|`tts`|string<br>object|**oui**| | |Le texte ou un tableau de textes à vocaliser. Peut être un texte simple, plusieurs textes séparés par un pipe ('\|') ou un tableau de textes (les textes peuvent être séparés par un pipe ('\|')). Une des valeurs sera choisie aléatoirement.|
|`callback`|function|non| | |Fonction de callback exécutée après le speak|
|`end`|boolean|non| true | true<br>false| Définie si la restitution de l'écoute sur le client est effectuée après le speak<br>**true**: restitue l'écoute<br>**false**: ne restitue pas l'écoute|
|voice|string|non| Config<br>.voices<br>.current[Config.voices.type]| |Voix à utiliser|
|volume|number|non|Config.voices.volume | |Volume de la voix|
|speed|number|non|Config.voices.speed | |Vitesse de la voix|
|pitch|number|non|Config.voices.pitch | |Intonation de la voix|

Exemples :  

1. Vocalise le tts et restitue l'écoute
    ```js
    Avatar.speak('Hello, what can i do for you?')
    ```
2. Vocalise un des tts aléatoirement et restitue l'écoute
    ```js
    Avatar.speak('Hello, what can i do for you?|Hi, can I help you?')
    ```
3. Vocalise un des tts aléatoirement et ne restitue pas l'écoute
    ```js
    Avatar.speak('Hello, what can i do for you?|Hi, can I help you?', null, false)
    ```
4. Vocalise un des tts aléatoirement, restitue l'écoute et exécute un callback ensuite
    ```js
    Avatar.speak(['Hello, what can i do for you?', 'Hi, can I help you?'], () => {
        // Do stuff
    })
    ```
5. Vocalise le tts, ne restitue pas l'écoute et exécute un callback ensuite
    ```js
    Avatar.speak('Hello, what can i do for you?', () => {
        // Do stuff
    }, false)
    ```
6. Vocalise un des tts aléatoirement, ne restitue pas l'écoute et exécute un callback ensuite
    ```js
    Avatar.speak(['Hello, what can i do for you?|Hi, can I help you?', 'I\'m home. Can I get you anything?'], () => {
        // Do stuff
    }, false)
    ```
7. Vocalise le tts, restitue l'écoute avec une définition de voix
    ```js
    // definition: voice name, volume, speed, pitch
    Avatar.speak('Hello, what can i do for you?',null, null, "Microsoft Julie - French (France)", 100, 80, 1)

    //Another definition: voice name and speed
    Avatar.speak('Hello, what can i do for you?', null, null,"Microsoft Julie - French (France)", null, 70)
    ```

### `Avatar.Listen.end([activateListen])`

Réactivation de l'écoute.

| <div style="width:100px">paramètre</div>| type| Obligatoire | Default value| Values | Description|
|----------|:---:|:-----------:|:-----------:|----------- |-----------|
|`activateListen`|boolean|non| true| true<br>false| Définie si la restitution de l'écoute est effectuée.<br> Dans tous les cas, la methode [`unmute`](tutorial-script.md#unmuteclientfrom-clientto) est exécutée|

Exemples: 

1. Réactivation de l'écoute
    ```js
    // Reactivation of listening
    Avatar.Listen.end();
    
    // Do stuff
    ```

2. tts sans la réactivation de l'écoute (end = false), callback et restitution de l'écoute 
    ```js
    Avatar.speak('Hello, what can i do for you?', () => {
        // Do Stuff
        // .......

        // Then reactivation of listening
        Avatar.Listen.end();
    }, false) 
    ```


### `Avatar.Listen.stop(forced)`

Arrête l'écoute du client

| <div style="width:100px">paramètre</div>| type| Obligatoire | Description|
|----------|:---:|:-----------:|:-----------|
|`forced`|boolean|non|Force l'arrêt de l'écoute même si l'écoute est déjà normalement arrêtée|

```js
Avatar.Listen.stop(true);
```

### `Avatar.Listen.start(forced)`

redémarre l'écoute du client

| <div style="width:100px">paramètre</div>| type| Obligatoire | Description|
|----------|:---:|:-----------:|:-----------|
|`forced`|boolean|non|Force le démarrage de l'écoute même si l'écoute est déjà normalement démarrée|

```js
Avatar.Listen.start(true);
```

### `Avatar.Listen.startListenAction()`

Déclenche un dialogue (identique au mot clé déclencheur).

```js
Avatar.Listen.startListenAction();
```

### `Avatar.Listen.stoptListenAction([forced])`

Stoppe le dialogue en cours

| <div style="width:100px">paramètre</div>| type| Obligatoire | Default value| Values | Description|
|----------|:---:|:-----------:|:-----------:|----------- |-----------|
|`forced`|boolean|non| true| true<br>false| Définie si la restitution de l'écoute sur le client est effectuée.<br> Dans tous les cas, la methode [`unmute`](tutorial-script.md#unmuteclientfrom-clientto) est exécutée|

```js
Avatar.Listen.stoptListenAction(true);
```



## Music play

### `Avatar.play(music[, type, end, callback])`

Joue un fichier son sur le client

| <div style="width:100px">paramètre</div>| type| Obligatoire | Default value| Values | Description|
|----------|:---:|:-----------:|:-----------:|----------- |-----------|
|`music`|string|**oui**|||Le fichier son à jouer. Peut être un fichier, une adresse web locale ou distante.<br>_\__dirname_ est remplacé par _<A.V.A.T.A.R\>/resources/app/core_|
|`type`|string|non|local|local<br>url|- _local_: Fichier sur le client<br>- _url_: Lien http local ou distant|
|`end`|string|non|after|before<br>after|Défini si la restitution de l'écoute sur le client est faite avant ou après de jouer le fichier son|
|`callback`|function|non|||Fonction de callback exécutée après le fichier son|

Exemples: 

1. Absolute path on the client, reactivates listening before playing, no callback 
    ```js
    Avatar.play('C:/music/siren.mp3', 'before'); 
    ```
2. _<A.V.A.T.A.R\>/resources/app/core_ path on the client, reactivates listening after playing and a callback   
    ```js
    Avatar.play('__dirname/music/siren.mp3', 'local', 'after', () => {
       // Do stuff
    }); 
    ```
3. Music on the web, reactivates listening after playing and a callback     
    ```js
    Avatar.play("https://ccrma.stanford.edu/~jos/mp3/cello.mp3", 'url', () => {
        // Do stuff
    });
    ```
4.  HTTP static folder on the client, reactivates listening after playing
    ```js
    const music = `http://${Config.http.client.ip}:${Config.http.client.port}/Love_Is_a_Battlefield.mp3`;
    
    // sets static local folder
    Avatar.static.set('C:/music', () => {
        // Plays music
        Avatar.play(music, 'url');
    });
    ```

### `Avatar.stop([callback])`

Stoppe le fichier son sur le client

| <div style="width:100px">paramètre</div>| type| Obligatoire | Description|
|----------|:---:|:-----------:|:----------- |
|`callback`|function|non|Fonction de callback exécutée après l'arrêt du fichier son|

Exemple :

```js
Avatar.stop();
```

## Files & folders

### `Avatar.static.set(folder[, calbback])`

Définit un dossier statique sur le client.

| <div style="width:100px">paramètre</div>| type| Obligatoire | Description|
|----------|:---:|:-----------:|:----------- |
|`folder`|string|**oui**|Dossier à rendre static|
|`callback`|function|non|Fonction de callback|

Exemple: 

```js
// sets static folder on the client
Avatar.static.set('C:/music', () => {
     // Do stuff
});
```


## Encrypt/decrypt a value

Les méthodes [_Avatar.encrypt_](API.md#avatarencryptvalue) et [_Avatar.decrypt_](API.md#avatardecryptvalue) sont identiques aux méthodes du serveur.


## HTTP request

Accès aux plugins par requète HTTP.

**Format:**  http://<_IP address_\>:<_Port_\>/avatarclient-<_route_>/<_Plugin_\>?_key=value_&_key=value_

|Paramètre| Obligatoire| Description |
|:-----|:----:|:---|
|IP address|**oui**|L'adresse IP du client|
|Port|**oui**|Le port de communication du client|
|route|**oui**|La route HTTP définie dans les propriétés pour le client|
|Plugin|**oui**|Le nom du plugin à exécuter|
|key=value |non|Les paramètres de l'object _data_ à passer à la méthode _action_ du plugin<br>Si une clé de paramètre est égale à `command` alors l'object _data_ sera _{action: {command: value, key: value\}_ sinon _{key: value\}_|

/// Tip
La configuration du port HTTP et de la route doivent être faites dans les propriétés du Plugin

![routehttp](img/routehttp.png){width="350"}
///

Exemples:

```js
/** Client IP address: 192.168.2.35
// Port: 4100
// Route: 'livingroom'
// Plugin: myPlugin
// keys: command=updateInfo&id=225665
// Object data reçu dans le plugin: {action: {command: updateInfo, id: 225665}}
*/
try {
    const response = await axios (url: 'http://192.168.2.35:4100/avatarclient-livingroom/myPlugin?command=updateInfo&id=225665');
    if (response.status !== 200) {
        throw new Error ('status '+response.status);
    }
} catch (err) {
    error ('HTTP error:', err);
}
```

```js
/** Serveur IP address: 192.168.2.35
// Port: 4100
// Route: 'Living'
// Plugin: myPlugin
// keys: id=225665
// Object data reçu dans le plugin: {id: 225665}
*/
try {
    const response = await axios ('http://192.168.2.35:4100/avatarclient-livingroom/myPlugin?id=225665');
    if (response.status !== 200) {
        throw new Error ('status '+response.status);
    }
} catch (err) {
    error ('HTTP error:', err);
}
```


## Interface

### `Avatar.Interface.mainWindow()`

Retourne l'instance de [BrowserWindow](https://www.electronjs.org/fr/docs/latest/api/browser-window) de la fenêtre principale d'A.V.A.T.A.R.

**NOTE:** Voir la méthode [Avatar.Interface.mainWindow()](API.md#avatarinterfacemainwindow) de l'API serveur pour plus de précision.

### `Avatar.Interface.BrowserWindow(options, html[, isMenu])`

Crée une nouvelle `BrowserWindow` avec les propriétés natives définies par les options.

**NOTE:** Voir la méthode [Avatar.Interface.BrowserWindow()](API.md#avatarinterfacebrowserwindowoptions-html-ismenu) de l'API serveur pour plus de précision.

### `Avatar.Interface.ipcMain()`

Permet de communiquer de manière asynchrone depuis le processus main avec les processus de rendu.  
Retourne le module ipcMain d'A.V.A.T.A.R.

**NOTE:** Voir la méthode [Avatar.Interface.ipcMain()](API.md#avatarinterfaceipcmain) de l'API serveur pour plus de précision.

### `Avatar.Interface.Menu()`

Retourne le module [Menu](https://www.electronjs.org/fr/docs/latest/api/menu)

**NOTE:** Voir la méthode [Avatar.Interface.Menu()](API.md#avatarinterfacemenu) de l'API serveur pour plus de précision.

### `Avatar.Interface.dialog()`

Retourne le module [dialog](https://www.electronjs.org/fr/docs/latest/api/dialog)

**NOTE:** Voir la méthode [Avatar.Interface.dialog()](API.md#avatarinterfacedialog) de l'API serveur pour plus de précision.

### `Avatar.Interface.shell()`

Retourne le module [shell](https://www.electronjs.org/fr/docs/latest/api/shell)

**NOTE:** Voir la méthode [Avatar.Interface.shell()](API.md#avatarinterfaceshell) de l'API serveur pour plus de précision.

### `Avatar.Interface.showRestartBox(msg)`

Affiche une boite de dialogue de redémarrage dans l'interface du client

**NOTE:** Voir la méthode [Avatar.Interface.showRestartBox()](API.md#avatarinterfaceshowrestartboxmsg) de l'API serveur pour plus de précision.

<br><br>
[:material-skip-previous: Developer Guide](developer-guide.md){ .md-button style="float:left;"} 
<br><br>