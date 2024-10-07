# Weather forecast - Part 2

Dans la 2ème partie, nous allons ajouter une fenêtre fille de l'interface A.V.A.T.A.R construite avec le framework [Electron](https://www.electronjs.org/docs/latest/)

Développer une fenêtre pour un plugin est facilité par A.V.A.T.A.R. Il n'est pas nécessaire de construire une application Electron complète, vous utilisez seulement les méthodes de l'API. 

## Comprendre les fichiers

Pour construire une fenêtre, plusieurs fichiers sont nécessaires:

### Main process
Le fichier "main process" est le fichier script node.js du plugin (coté serveur web).  
Dans ce fichier sont effectuées toutes les actions qui sont en relation avec A.V.A.TA.R et le script du plugin, comme par exemple la création d'une fenêtre fille.

### Pre load


Le fichier "pre load" est une passerelle entre le "main process" et le "renderer process" (coté client Chromium).  
Dans ce fichier sont définies les "méthodes passerelles" qui enverront ou recevront des informations entre le "main process" et le "renderer process" via ce processus de communication.

### Renderer process

???+ Info "Important"
    Les processus de rendu d'Electron sont exécutés dans un bac à sable.  
    Ils se comportent de la même manière qu'un processus de rendu Chrome normal. Un moteur de rendu en bac à sable n'aura donc pas d'environnement Node.js initialisé. Plus simplement, vous ne pouvez pas utiliser node.js dans un processus de rendu.

    Le bac à sable limite les dommages que peut causer un code malveillant en limitant l'accès à la plupart des ressources du système  et ne peuvent utiliser librement que les cycles de l'unité centrale et la mémoire.


Le "renderer process" comprend 3 principaux fichiers:

#### javascript

Dans ce fichier sont reçus ou envoyées toutes les communications du "main process" (en passant par le "pre load") mais aussi, peut effectuer toutes les actions javascript de l'interface web (coté client Chromium).

???+ note
    Il n'est pas nécessaire d'avoir un fichier javascript si aucune communication entre le "main process" et le "renderer process" n'est effectuée, ni aucune fonction javascript pour la page web.

#### HTML
Le fichier "html" est le fichier de rendu de la page web. Le fichier javascript est appelé dans ce fichier.

#### CSS
Le fichier "css" est le fichier de mise en forme de la page web. il est appelé dans le fichier "html".

### En résumé

|Type|SubType | language | Obligatoire|Format|Commentaire |
|:-----|:------|:---:|:---:|:----------|:----------|
|Main process||node.js<br>>= ES6|**oui**|<plugin\>.js|Le script du plugin|
|Pre load||node.js<br>CommonJS|non|<plugin\>-preload.js|Le fichier passerelle.<br>Obligatoire seulement si une communication d'information est nécessaire entre le Main proccess et le Renderer process|
|Renderer|javascript|>= ES6|non|<plugin\>-renderer.js|Obligatoire seulement si une communication d'information est nécessaire avec le Main proccess|
|Renderer|html|html|**oui**|<plugin\>.html|Le fichier de rendu de la page web|
|Renderer|css|css|non|<plugin\>.css|Le fichier de mise en forme de la page web|

## Créer les fichiers du plugin

### Le fichier Pre load

1. Ouvrez un terminal et déplacez-vous dans le répertoire du plugin
    ```
    cd <A.V.A..T.A.R>/resources/app/cores/plugin/weather
    ```
2. Créez un fichier _weather-preload.js_
3. Copiez le template de fichier pre load ci-dessous dans le fichier

    ``` js title="weather-preload.js"
    const { contextBridge, ipcRenderer } = require('electron')

    contextBridge.exposeInMainWorld('electronAPI', {
    
    })
    ```

### Le fichier HTML

1. Dans le répertoire du plugin, créez un fichier _weather.html_
2. Copiez le template de fichier html ci-dessous dans le fichier

    **Note:** Les import du _weather.css_ et du _weather-renderer.js_ sont inclus

    ``` html title="weather.html"
    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="UTF-8">
            <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'; script-src-elem 'self'; style-src 'self' 'unsafe-inline'">
            <meta http-equiv="X-Content-Security-Policy" content="default-src 'self'; script-src 'self'"/>
            <link href="./weather.css" rel="stylesheet" type="text/css" />
        </head>
        <body>
        
            <script src="./weather-renderer.js"></script>
        </body>
    </html>
    ```

### Le fichier javacript

1. Dans le répertoire du plugin, créez un fichier _weather-renderer.js_
2. Pour l'instant, le fichier est vide

### Le fichier CSS

1. Dans le répertoire du plugin, créez un fichier _weather.css_
2. Copiez le template de fichier css ci-dessous dans le fichier

    ``` css title="weather.css"
    body, html {
        width: 100%;
        height: 100%;
        overflow: hidden;
        margin: 0;
        -webkit-app-region: drag;
    }
    ```

## Créer la fenêtre

Dans le script du plugin,

1. Ajoutez une variable globale _WeatherWindow_

    ``` js title="weather.js"  hl_lines="1-2"
    // Private
    let WeatherWindow; // weather forecast window
    let currentwidgetState;
    let periphInfo = []; // devices table
    let Locale; //language pak
    ```

2. Ajoutez les actions du bouton

    ``` js title="weather.js"
    export async function widgetAction (even) {
        // Save current state
        currentwidgetState = even.value.action === 'On' ? true : false;
        // If 'On', show window
        if (!WeatherWindow && even.value.action === 'On') return openWeatherWindow();
        // If 'Off', destroy window
        if (WeatherWindow && even.value.action === 'Off') WeatherWindow.destroy();
    }
    ```

2. Ajoutez dans le script du plugin la méthode suivante à la fin du fichier:

    ``` js title="weather.js" linenums="1"
    const openWeatherWindow = async () => {

        if (WeatherWindow) return WeatherWindow.show();

        let style = {
            parent: Avatar.Interface.mainWindow(),
            frame: false,
            movable: true,
            resizable: false,
            minimizable: false,
            alwaysOnTop: false,
            show: false,
            width: 150,
            height: 320,
            opacity : 1,
            icon: path.resolve(__dirname, 'assets', 'images', 'weather.png'),
            webPreferences: {
                preload: path.resolve(__dirname, 'weather-preload.js')
            },
            title: "Weather forecast"
        }

        WeatherWindow = await Avatar.Interface.BrowserWindow(style, path.resolve(__dirname, 'weather.html'), false);
        
        WeatherWindow.once('ready-to-show', () => {
            WeatherWindow.show();
        })

        WeatherWindow.on('closed', () => {
            WeatherWindow = null;
        })  
    }
    ```

4. Redémarrez A.V.A.T.A.R
5. Testez le widget bouton 
    * Vous pouvez aussi essayer de déplacer la fenêtre en faisant un drag&drop

### Explications 

Quelques explications sur les méthodes et objects de la création de la fenêtre dans la méthode _openWeatherWindow_ 

#### Avatar.Interface.mainWindow()

Returns the A.V.A.T.A.R main window

#### class BrowserWindow [#](https://www.electronjs.org/docs/latest/api/browser-window#class-browserwindow-extends-basewindow)

Create and control browser windows.  

???+ Warning "Important"
    This class is controled by A.V.A.T.A.R. You have to use the [_Avatar.Interface.BrowserWindow()_](API.md#avatarinterfacebrowserwindowoptions-html-ismenu) to create a new browser window.

* line 23: Create a new browser window
``` js
WeatherWindow = await Avatar.Interface.BrowserWindow(style, path.resolve(__dirname, 'weather.html'), false);
```

#### win.show() event method [#](https://www.electronjs.org/docs/latest/api/browser-window#winshow)

Shows and gives focus to the window. 

* line 3: Creates window only if the WeatherWindow variable is undefined.
``` js
if (WeatherWindow) return WeatherWindow.show();
```

* line 26: Show the window when the event `ready-to-show` is emitted 
``` js hl_lines="2"
WeatherWindow.once('ready-to-show', () => {
    WeatherWindow.show();
})
```

#### Event methods

**Event: 'ready-to-show' [#](https://www.electronjs.org/docs/latest/api/browser-window#event-ready-to-show)**  
Emitted when the web page has been rendered (while not being shown) and window can be displayed without a visual flash.

``` js hl_lines="1"
WeatherWindow.once('ready-to-show', () => {
    WeatherWindow.show();
})
```

**Event: 'closed' [#](https://www.electronjs.org/docs/latest/api/browser-window#event-closed)**  
Emitted when the window is closed. After you have received this event you should remove the reference to the window and avoid using it any more.

``` js hl_lines="1"
WeatherWindow.on('closed', () => {
    WeatherWindow = null;
})  
```

## window position

### Save

La sauvegarde de la position de la fenêtre est effectuée lorsque A.V.A.T.A.R est redémarré ou quitter.

1. Ajoutez `fs-extra`dans les imports

    ``` js hl_lines="2"
    import * as path from 'node:path';
    import fs from 'fs-extra';
    import * as url from 'url';
    ```

2. Ajoutez dans la méthode _onClose()_ le test de sauvegarde comme ci-dessous

    ``` js hl_lines="8-27"
    export async function onClose (widgets) {
        // Save widget position
        if (Config.modules.weather.widget.display === true) {
            await Widget.initVar(widgetFolder, widgetImgFolder, null, Config.modules.weather);
            if (widgets) await Widget.saveWidgets(widgets);
        }

        // Save meteo forecast position
        if (WeatherWindow) { // The window is displayed
            // Get window instance position
            let pos = WeatherWindow.getPosition();
            // Writes position and the window state (displayed or closed)
            fs.writeJsonSync(path.resolve(__dirname, 'assets', 'style.json'), {
            x: pos[0],
            y: pos[1],
            start: true,
            });
        } else {  // The window is closed
            // If the backup file exists then keeps the position first
            let prop = {};
            if (fs.existsSync(path.resolve(__dirname, 'assets', 'style.json'))) {
                prop = fs.readJsonSync(path.resolve(__dirname, 'assets', 'style.json'), { throws: false });
            }
            // Writes the window state (closed)
            prop.start = false;
            fs.writeJsonSync(path.resolve(__dirname, 'assets', 'style.json'), prop);
        }
    } 
    ```

### restore

La restitution de la position de la fenêtre est effectuée à la création de la fenêtre.

1. Ajoutez dans la méthode _openWeatherWindow()_ la restitution de la position comme ci-dessous

    ``` js title="weather.js" hl_lines="23-29"
    const openWeatherWindow = async () => {

        if (WeatherWindow) return WeatherWindow.show();

        let style = {
            parent: Avatar.Interface.mainWindow(),
            frame: false,
            movable: true,
            resizable: false,
            minimizable: false,
            alwaysOnTop: false,
            show: false,
            width: 150,
            height: 320,
            opacity : 1,
            icon: path.resolve(__dirname, 'assets', 'images', 'weather.png'),
            webPreferences: {
                preload: path.resolve(__dirname, 'weather-preload.js')
            },
            title: "Weather forecast"
        };

        if (fs.existsSync(path.resolve(__dirname, 'assets', 'style.json'))) {
            let prop = fs.readJsonSync(path.resolve(__dirname, 'assets', 'style.json'), { throws: false });
            if (prop) {
                style.x = prop.x;
                style.y = prop.y;
            }
        }

        WeatherWindow = await Avatar.Interface.BrowserWindow(style, path.resolve(__dirname, 'weather.html'), false);
        
        WeatherWindow.once('ready-to-show', () => {
            WeatherWindow.show();
        })

        WeatherWindow.on('closed', () => {
            WeatherWindow = null;
        })  
    }
    ```

## Restore window state

La restitution de l'état de la fenêtre est effectuée lorsque A.V.A.T.A.R a terminé d'afficher les widgets

1. Ajoutez dans la méthode _readyToShow()_ la restitution de l'état comme ci-dessous

    ``` js title="weather.js" hl_lines="2-14"
    export async function readyToShow () {
        // If a backup file exists
        if (fs.existsSync(path.resolve(__dirname, 'assets', 'style.json'))) {
            let prop = fs.readJsonSync(path.resolve(__dirname, 'assets', 'style.json'), { throws: false });
            // Set currentwidgetState global variable
            currentwidgetState = prop.start;
            // currentwidgetState = true : creates and shows new window
            if (currentwidgetState) openWeatherWindow();
        } else 	
            // not displayed if the window is closed
            currentwidgetState = false;

        // Refreshs information of the button widget regarding window state ('On' or 'Off' image)
        Avatar.Interface.refreshWidgetInfo({plugin: 'weather', id: "808221"});	
    }
    ```
2. Ajoutez la variable currentwidgetState à `false` dans l'event `closed` comme ci-dessous

    ``` js title="weather.js" hl_lines="2-3"
    WeatherWindow.on('closed', () => {
        // not displayed if the window is closed
        currentwidgetState = false;
    
        WeatherWindow = null;
    })  
    ```
    
3. Redémarrez A.V.A.T.A.R
4. Testez la sauvegarde/restore de la position et de l'état de la fenêtre 

## Ajoutez la météo

Vous pouvez récupérer un widget météo d'une ville sur plusieurs sites. Soyez prudent sur le choix du site, certains récupèrent beaucoup d'information de connexion (comme weatherwidget.org), d'autres sont carrément répertoriés comme très dangereux (comme widget-meteo.com). Le plus simple est d'utiliser un site sécurisé (comme le site de [météo France](https://meteofrance.com/widgets) qui donne aussi la météo dans tous les pays)

???+ warning 
    Souvenez-vous aussi de la création du fichier html. Ce fichier dû à l'exécution du rendu dans le bac à sable limite les dommages que peut causer un code malveillant mais impose aussi une validation des liens externes. Pour certains sites qui exécutent du code javascript, il vous faudra autoriser le site dans les [security policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src)

1. Ouvrez un navigateur et connectez-vous sur le site de [météo France](https://meteofrance.com/widgets) ou le site de votre choix
2. Récupérez le code html
3. Copiez-le dans le fichier html comme ci-dessous

    ``` html title="weather.html" hl_lines="10"
    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="UTF-8">
            <meta http-equiv="Content-Security-Policy" content="script-src 'self'; script-src-elem 'self'; style-src 'self' 'unsafe-inline'">
            <meta http-equiv="X-Content-Security-Policy" content="default-src 'self'; script-src 'self'"/>
            <link href="./weather.css" rel="stylesheet" type="text/css" />
        </head>
        <body>
            <iframe id="widget_autocomplete_preview"  width="150" height="300" frameborder="0" src="https://meteofrance.com/widget/prevision/441090##3D6AA2" title="Prévisions Nantes par Météo-France"> </iframe>
            <script src="./weather-renderer.js"></script>
        </body>
    </html>
    ```
4. Changez la taille de l'élément _iframe_ pour width="100%" et height="100%"

    ``` html title="weather.html" hl_lines="1"
    <iframe id="widget_autocomplete_preview"  width="100%" height="100%" frameborder="0" src="https://meteofrance.com/widget/prevision/441090##3D6AA2" title="Prévisions Nantes par Météo-France"> </iframe>
    ```
5. Autorisez le site dans les security policy en l'ajoutant dans la police `default-src` comme ci-dessous
    ``` html title="weather.html" hl_lines="1"
    <meta http-equiv="Content-Security-Policy" content="default-src 'self' https://meteofrance.com; script-src 'self'; script-src-elem 'self'; style-src 'self' 'unsafe-inline'">
        
    ```
6. Redémarrez A.V.A.T.A.R

    ![meteo-displayed](img/meteo-displayed.png){width="250"}

???+ tip
    Si vous constatez un problème de taille de fenêtre, vous pouvez l'ajuster dans le script et l'objet _style.width_ et _style.height_

    
## Transférer des informations entre les Main et Renderer processus

Nous pourrions nous arrêter là puisque le plugin est fonctionnel mais pour l'exemple, nous allons transférer des informations entre le main process et le renderer process.

Il existe plusieurs types de communication inter process (IPC):

**Type 1:** Processus principal (Main) vers moteur de rendu (Renderer)  
**Type 2:** Moteur de rendu vers le processus principal (unidirectionnel)  
**Type 3:** Moteur de rendu vers le processus principal (bidirectionnel)

???+ tip
    Pour vous familiariser avec ces concepts, vous pouvez lire la documentation Electron sur [La communication inter-processus](https://www.electronjs.org/fr/docs/latest/tutorial/ipc)

Dans notre exemple, nous allons d'abord utiliser le types 1 pour envoyer un message depuis le main process au moteur de rendu pour qu'il exécute une fonction puis ensuite le type 3 pour que le moteur de rendu demande une information au processus principal et recevoir la réponse, enfin nous utiliserons le type 2 pour que le moteur de rendu envoie un ordre d'exécution de fermeture de la fenêtre au processus principal.

### Type 1: Envoyer un message au moteur de rendu

L'envoi d'un message au moteur de rendu s'effectue via son instance [WebContents](https://www.electronjs.org/fr/docs/latest/api/web-contents), cette instance de WebContents contient une méthode [send](https://www.electronjs.org/fr/docs/latest/api/web-contents#contentssendchannel-args) qui peut être utilisée.

1. Ajoutez dans le script du plugin la méthode `send`

    #### contents.send(channel, ...args)

    * channel string
    * ...args any[]

    Envoye un message asynchrone et un certain nombre d'arguments au processus de rendu via channel

    ??? warning
        NOTE: L'envoi de types Javascript non standards tels que des objets DOM ou des objets spéciaux déclenchera une exception.

    Pour notre exemple, nous devons signifier au moteur de rendu qu'il doit exécuter une action de mise à jour d'un elément `label` **après** son initialisation complète (afin que tous les eléments du DOM soient accessibles). Le moyen de faire ca est de placer la méthode `send` dans l'event `ready-to-show`

    ``` js title="weather.js" hl_lines="35"
    const openWeatherWindow = async () => {

        if (WeatherWindow) return WeatherWindow.show();

        let style = {
            parent: Avatar.Interface.mainWindow(),
            frame: false,
            movable: true,
            resizable: false,
            minimizable: false,
            alwaysOnTop: false,
            show: false,
            width: 150,
            height: 320,
            opacity : 1,
            icon: path.resolve(__dirname, 'assets', 'images', 'weather.png'),
            webPreferences: {
                preload: path.resolve(__dirname, 'weather-preload.js')
            },
            title: "Weather forecast"
        };

        if (fs.existsSync(path.resolve(__dirname, 'assets', 'style.json'))) {
            let prop = fs.readJsonSync(path.resolve(__dirname, 'assets', 'style.json'), { throws: false });
            if (prop) {
                style.x = prop.x;
                style.y = prop.y;
            }
        }

        WeatherWindow = await Avatar.Interface.BrowserWindow(style, path.resolve(__dirname, 'weather.html'), false);
        
        WeatherWindow.once('ready-to-show', () => {
            WeatherWindow.show();
             WeatherWindow.webContents.send('onInit-weather');
        })

        WeatherWindow.on('closed', () => {
            // not displayed if the window is closed
            currentwidgetState = false;

            WeatherWindow = null;
        })  
    } 
    ```
    
2. Exposition de `ipcRenderer.on` via l'ajout dans le script de pre load

    #### ipcRenderer.on(channel, listener)

    * channel string
    * listener Function
        * event IpcRendererEvent
        * ...args any[]

    Ecoute sur channel et listener sera appelé ainsi: listener(event, args...) à la réception d'un nouveau message.

    ``` js title="weather-preload.js" hl_lines="4"
    const { contextBridge, ipcRenderer } = require('electron')

    contextBridge.exposeInMainWorld('electronAPI', {
        onInitWeather: (callback) => ipcRenderer.on('onInit-weather', (_event, value) => callback(value))
    })    
    ```

    Après avoir chargé le script de préchargement, votre processus de rendu aura accès à la fonction d’écoute `window.electronAPI.onInitWeather()`.

3. Ajoutez la fonction d’écoute dans le moteur de rendu

    ``` js title="weather-renderer.js"

    async function setElementLabel() {
    
    }

    window.electronAPI.onInitWeather( _event => {
        setElementLabel();
    })
    ```

 **En résumé :**  
 Nous avons envoyé un message au rendu par la méthode `send` via le script de pre load par `ipcRenderer.on` et le rendu le reçoit via la fonction d'écoute `onInitWeather`

 Il nous reste maintenant à demander la valeur d'un elément `label`, pour ce faire nous établir une communication via le type 3 Moteur de rendu vers le processus principal (bidirectionnel)

### Type 3: Le rendu demande une information au processus principal et attend la réponse (bidirectionnel)

Une communication bidirectionnel est l'appel d'un module du processus principal à partir du code du processus de rendu avec l'attente d'un résultat. Cela peut être fait en utilisant [ipcRenderer.invoke](https://www.electronjs.org/fr/docs/latest/api/ipc-renderer#ipcrendererinvokechannel-args) dans le script de pre load jumelé avec [ipcMain.handle](https://www.electronjs.org/fr/docs/latest/api/ipc-main#ipcmainhandlechannel-listener) dans le main process.


1. Ajoutez un module d'écoute

    #### ipcMain.handle(channel, listener)
    * channel string
    * listener Function<Promise<any> | any>
        * event IpcMainInvokeEvent
        * ...args any[]

    Ajoute un module d'écoute sur _channel_ dans le main process. Ce gestionnaire est appelé chaque fois que le processus de rendu appelle `ipcRenderer.invoke(channel, ...args)`.

    Si listener renvoie une Promise, le résultat final de la promesse sera retourné en réponse à l'appelant distant. Sinon, la valeur de retour de l'écouteur sera utilisée comme valeur de la réponse.

    ???+ warning
        * The IPC main is controled by A.V.A.T.A.R. You have to use the [_Avatar.Interface.ipcMain()_](API.md#avatarinterfaceipcmain) for returning the A.V.A.T.A.R ipcMain.
        * Un handle pour le _channel_ ne peut être déclaré qu'une seule fois. Il doit **obligatoirement** être supprimé par un [ipcMain.removeHandler](https://www.electronjs.org/fr/docs/latest/api/ipc-main#ipcmainremovehandlerchannel) avant d'être déclaré de nouveau.

    * Ajoutez dans la méthode _openWeatherWindow_ l'ajout et la suppression du gestionnaire comme ci-dessous

    ``` js title="weather.js" hl_lines="38-39 45-46"
    const openWeatherWindow = async () => {

        if (WeatherWindow) return WeatherWindow.show();

        let style = {
            parent: Avatar.Interface.mainWindow(),
            frame: false,
            movable: true,
            resizable: false,
            minimizable: false,
            alwaysOnTop: false,
            show: false,
            width: 150,
            height: 320,
            opacity : 1,
            icon: path.resolve(__dirname, 'assets', 'images', 'weather.png'),
            webPreferences: {
                preload: path.resolve(__dirname, 'weather-preload.js')
            },
            title: "Weather forecast"
        };

        if (fs.existsSync(path.resolve(__dirname, 'assets', 'style.json'))) {
            let prop = fs.readJsonSync(path.resolve(__dirname, 'assets', 'style.json'), { throws: false });
            if (prop) {
                style.x = prop.x;
                style.y = prop.y;
            }
        }

        WeatherWindow = await Avatar.Interface.BrowserWindow(style, path.resolve(__dirname, 'weather.html'), false);
        
        WeatherWindow.once('ready-to-show', () => {
            WeatherWindow.show();
            WeatherWindow.webContents.send('onInit-weather');
        })

        // returns the localized message defined in arg
        Avatar.Interface.ipcMain().handle('weather-msg', async (_event, arg) => {return Locale.get(arg)});

        WeatherWindow.on('closed', () => {
            // not displayed if the window is closed
            currentwidgetState = false;

            // Removes the handler `weather-msg` when the window is closed
            Avatar.Interface.ipcMain().removeHandler('weather-msg');
            
            WeatherWindow = null;
        })  
    }
    ```

2. Exposition de `ipcRenderer.invoke` via l'ajout dans le script de pre load

    #### ipcRenderer.invoke(channel, ...args)
    * channel string
    * ...args any[]
    
    Retourne une Promise<any> - qui se résout avec la réponse du processus principal.

    Envoie un message au processus principal via channel et attend un résultat asynchrone.

    ``` js title="weather-preload.js" hl_lines="5"
    const { contextBridge, ipcRenderer } = require('electron')

    contextBridge.exposeInMainWorld('electronAPI', {
        onInitWeather: (callback) => ipcRenderer.on('onInit-weather', (_event, value) => callback(value)),
        getMsg: (value) => ipcRenderer.invoke('weather-msg', value)
    })    
    ```

    Le module d'écoute `weather-msg` est appelé chaque fois que la fonction `window.electronAPI.getMsg()` du processus de rendu appelle `ipcRenderer.invoke()` 

3. Ajoutez la fonction _getMsg()_ dans le processus de rendu

    ``` js title="weather-renderer.js" hl_lines="1-10 13"
    async function Lget (target, ...args) {
        if (args) {
            target = [target];
            args.forEach(arg => {
                target.push(arg);
            })
        } 

        return await window.electronAPI.getMsg(target);
    }

    async function setElementLabel() {
        document.getElementById('quit').innerHTML = await Lget("message.quit");
    }

    window.electronAPI.onInitWeather( _event => {
        setElementLabel(); 
    })  
    ```

4. Localisez le message

    * Modifiez le fichier _weather/locales/en.pak_ comme ci-dessous

    **NOTE:** Consultez [_<lang\>.get()_](API.md#lang-pakgetkey) pour plus d'information

    ``` json title="en.pak"
    {
        "message": {
            "quit":"X"
        }
    }
    ```

5. Ajoutez un elément `label` dans le fichier html

    ``` html title="weather.html" hl_lines="11"
    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="UTF-8">
            <meta http-equiv="Content-Security-Policy" content="script-src 'self'; script-src-elem 'self'; style-src 'self' 'unsafe-inline'">
            <meta http-equiv="X-Content-Security-Policy" content="default-src 'self'; script-src 'self'"/>
            <link href="./weather.css" rel="stylesheet" type="text/css" />
        </head>
        <body>
            <iframe id="widget_autocomplete_preview"  width="150" height="300" frameborder="0" src="https://meteofrance.com/widget/prevision/441090##3D6AA2" title="Prévisions Nantes par Météo-France"> </iframe>
            <label id="quit" class="quit"></label>
            <script src="./weather-renderer.js"></script>
        </body>
    </html>
    ```
6. Ajoutez un style dans le fichier css

    ``` css title="weather.css" hl_lines="9-22"
    body, html {
        width: 100%;
        height: 100%;
        overflow: hidden;
        margin: 0;
        -webkit-app-region: drag;
    }

    .quit{
        position: fixed;
        top: 0px;
        right: 2px;
        -webkit-app-region:no-drag;
        font: 13px helvetica neue, helvetica, arial, sans-serif;
        font-weight: bold;
        color: rgb(255, 255, 255);
        z-index: 2;
    }
    .quit:hover {
        color: rgb(247, 5, 5);
        cursor: pointer;
    }
    ```

7. Redémarrez A.V.A.T.A.R et vérifiez l'ajout de l'elément label **X** dans le coin haut droit

    ![meteo-displayed-X](img/meteo-displayed-X.png){width="150"}

### Type 2: Le rendu envoie une information au processus principal (unidirectionnel)

Une communication unidirectionnel est l'appel d'un module du processus principal à partir du code du processus de rendu. Cela peut être fait en utilisant [ipcRenderer.send](https://www.electronjs.org/fr/docs/latest/api/ipc-renderer#ipcrendererinvokechannel-args) dans le script de pre load jumelé avec [ipcMain.on](https://www.electronjs.org/fr/docs/latest/api/ipc-main#ipcmainonchannel-listener) dans le main process.

1. Ajoutez un module d'écoute

    #### ipcMain.on(channel, listener)
    * channel string
    * listener Function
        * event IpcMainEvent
        * ...args any[]

    Ecoute sur channel et listener sera appelé ainsi: listener(event, args...) à la réception d'un nouveau message.

    ???+ warning
        * The IPC main is controled by A.V.A.T.A.R. You have to use the [_Avatar.Interface.ipcMain()_]() for returning the A.V.A.T.A.R ipcMain.
        * Un Listener pour le _channel_ ne peut être déclaré qu'une seule fois. Il doit **obligatoirement** être supprimé par un [ipcMain.removeAllListeners](https://www.electronjs.org/fr/docs/latest/api/ipc-main#ipcmainremovealllistenerschannel) avant d'être déclaré de nouveau.

    Pour notre exemple, nous devons signifier au processus principal qu'il doit exécuter la fermeture de la fenêtre par un click de l'elément label **X**.  

    **NOTE:** Cette fermeture est réalisée par la méthode d'instance [_win.destroy()_](https://www.electronjs.org/fr/docs/latest/api/base-window#windestroy)

    * Ajoutez dans la méthode _openWeatherWindow_ l'ajout et la suppression du module d'écoute comme ci-dessous

    ``` js title="weather.js" hl_lines="38-45 57-58"
    const openWeatherWindow = async () => {

        if (WeatherWindow) return WeatherWindow.show();

        let style = {
            parent: Avatar.Interface.mainWindow(),
            frame: false,
            movable: true,
            resizable: false,
            minimizable: false,
            alwaysOnTop: false,
            show: false,
            width: 150,
            height: 320,
            opacity : 1,
            icon: path.resolve(__dirname, 'assets', 'images', 'weather.png'),
            webPreferences: {
                preload: path.resolve(__dirname, 'weather-preload.js')
            },
            title: "Weather forecast"
        };

        if (fs.existsSync(path.resolve(__dirname, 'assets', 'style.json'))) {
            let prop = fs.readJsonSync(path.resolve(__dirname, 'assets', 'style.json'), { throws: false });
            if (prop) {
                style.x = prop.x;
                style.y = prop.y;
            }
        }

        WeatherWindow = await Avatar.Interface.BrowserWindow(style, path.resolve(__dirname, 'weather.html'), false);
        
        WeatherWindow.once('ready-to-show', () => {
            WeatherWindow.show();
            WeatherWindow.webContents.send('onInit-weather');
        })

        // Destroys window by a click on the 'X' label 
        Avatar.Interface.ipcMain().one('weather-quit', () => {
            // Closed window
            WeatherWindow.destroy();

            // refresh widget button on window closed
            Avatar.Interface.refreshWidgetInfo({plugin: 'weather', id: "808221"});
        });
        
        // returns the localized message defined in arg
        Avatar.Interface.ipcMain().handle('weather-msg', async (_event, arg) => {return Locale.get(arg)});

        WeatherWindow.on('closed', () => {
            // not displayed if the window is closed
            currentwidgetState = false;

            // Removes the `weather-msg` handler when the window is closed
            Avatar.Interface.ipcMain().removeHandler('weather-msg');

            // Removes the `weather-quit` listener when the window is closed
            Avatar.Interface.ipcMain().removeAllListeners('weather-quit');
            
            WeatherWindow = null;
        })  
    }
    ```

2. Exposition de `ipcRenderer.send` via l'ajout dans le script de pre load

    #### ipcRenderer.send(channel, ...args)
    * channel string
    * ...args any[]

    Envoie un message asynchrone ainsi que des arguments au processus principal via channel.

    ``` js title="weather-preload.js" hl_lines="6"
    const { contextBridge, ipcRenderer } = require('electron')

    contextBridge.exposeInMainWorld('electronAPI', {
        onInitWeather: (callback) => ipcRenderer.on('onInit-weather', (_event, value) => callback(value)),
        getMsg: (value) => ipcRenderer.invoke('weather-msg', value),
        quit: () => ipcRenderer.send('weather-quit')
    })    
    ```

    Le listener `weather-quit` est appelé chaque fois que la fonction `window.electronAPI.quit()` du processus de rendu appelle `ipcRenderer.send()`

3. Ajoutez la fonction `window.electronAPI.quit()` dans le processus de rendu

    ``` js title="weather-renderer.js" hl_lines="1-10"
    window.onbeforeunload = async (e) => {
        e.returnValue = false;
        // Calls window closure in the main process 
        window.electronAPI.quit();
    }

    // A click on the label calls the 'beforeunload' event
    document.getElementById("quit").addEventListener("click", async (event) => {
        window.dispatchEvent(new Event ('beforeunload'));
    })

    async function Lget (target, ...args) {

        if (args) {
            target = [target];
            args.forEach(arg => {
                target.push(arg);
            })
        } 

        return await window.electronAPI.getMsg(target);
    }

    async function setElementLabel() {
        document.getElementById('quit').innerHTML = await Lget("message.quit");
    }

    window.electronAPI.onInitWeather( _event => {
        setElementLabel(); 
    })
    ```

4. Redémarrez A.V.A.T.A.R et vérifiez la fermeture de la fenêtre par un click de l'elément label **X**

## Finalisation 

Quelques petits ajouts pour finaliser le plugin, par exemple :

1. Que la taille de la fenêtre soient des paramètres
2. Que l'on puisse facilement ouvrir la console Chromium

**1:** Taille de la fenêtre en paramètres

* Ajoutez des propriétés pour le plugin par _Plugin studio_

``` json
win = {
    width: 150,
    height: 320,
    opacity: 1
}
devTools = false
```

![weather-new-properties](img/weather-new-properties.png){width="400"}

**2:** Modifiez l'objet `style` pour inclure les paramètres

``` js title="weather.js" hl_lines="9-11"
let style = {
    parent: Avatar.Interface.mainWindow(),
    frame: false,
    movable: true,
    resizable: false,
    minimizable: false,
    alwaysOnTop: false,
    show: false,
    width: Config.modules.weather.win.width,
    height: Config.modules.weather.win.height,
    opacity : Config.modules.weather.win.opacity,
    icon: path.resolve(__dirname, 'assets', 'images', 'weather.png'),
    webPreferences: {
        preload: path.resolve(__dirname, 'weather-preload.js')
    },
    title: "Weather forecast"
};

......
```    

**3:** Ajoutez l'ouverture de la console Chromium

``` js title="weather.js" hl_lines="4-5"
WeatherWindow.once('ready-to-show', () => {
    WeatherWindow.show();
    WeatherWindow.webContents.send('onInit-weather');
    // Displays the Chromium console if the devTools parameter is true
    if (Config.modules.weather.devTools) WeatherWindow.webContents.openDevTools();
})
```
    
4. Redémarrez A.V.A.T.A.R


Dans l'exemple suivant, nous allons tranférer ce plugin vers un client

<br><br>
[:material-skip-previous: Créer un widget bouton - Part 1](meteo.md){ .md-button style="float:left;"}[Tranférer le plugin vers un client - Part 3 :material-skip-next:](meteo-widget-client.md){ .md-button style="float:right;"}   
<br><br>