# Folders and files

## Folders

When launched, the application searches for plugins in the following folder:

``` hl_lines="5"
A.V.A.T.A.R
    ├── resouces
        ├── app
            ├── core
                ├── plugins
```

=== "Server"

    | <div style="width:95px">Folder</div>| Auto<br>creation(1)| <div style="width:20px">Mandatory<div> | Description|
    |----------------------------|:---:|:---:| ------------------------------------|
    | `assets` | yes  | yes   | Includes:<br>- The **mandatory** information file `ìnfos.md` or `infos_<application language>.md` in [Markdown](https://www.markdownguide.org/basic-syntax/) format appearing in the plugin's `Ìnformation` tab in _Plugin Studio_. This file must be modified to add the information required to use the plugin.<br><br>- A **non-mandatory** `images` folder for plugin images. A creation by _Wigdet Studio_ stores the image file `<myPlugin>.png`. If the `images` folder does not exist, a default image is displayed.|
    | `documentation`           | yes               | no   | Includes:<br>- The `documentation.ini` documentation configuration file.<br>- Place any html documentation you can create in this folder.|
    | `locales`                 | yes               | no   | If you're locating plugin messages, create your `<lang>.pak` files to this folder.<br>Creation by _Wigdet Studio_ automatically deposits a `<lang>.pak` file for the current language.|        
    | `node_modules`            | no               | no   | Folder automatically created if you add npm modules to your plugin.|
    | `...`                     | N/A               | no   | Any other folder useful to your plugin.|


    (1) Folder created automatically if you use _Plugin Studio_ to create the plugin

=== "Client"

    | <div style="width:95px">Folder</div>| <div style="width:20px">Mandatory<div> | Description|
    |----------------------------|:---:| ------------------------------------|
    | `assets`                  |  yes   | Includes:<br>- The **mandatory** information file `ìnfos.md` or `infos_<application language>.md` in [Markdown](https://www.markdownguide.org/basic-syntax/) format appearing in the plugin's `Ìnformation` tab in _Plugin Studio_. This file must be modified to add the information required to use the plugin.<br><br>- A **non-mandatory** `images` folder for plugin images. A creation by _Wigdet Studio_ stores the image file `<myPlugin>.png`. If the `images` folder does not exist, a default image is displayed.|
    | `locales`                 | no   | If you're locating plugin messages, create your `<lang>.pak` files to this folder.
    | `node_modules`            |  no   | Folder automatically created if you add npm modules to your plugin.|
    | `...`                     | no   | Any other folder useful to your plugin.|



???+ note
    If you decide to create the folders yourself (whether mandatory or not), use the same writing convention.



## Files

=== "Server"

    |Type|<div style="width:115px">File</div>|Auto<br>creation(1)| Mandatory | Description|
    |:---:|:-----------                |:-:              |:-:          | :------------------------------------|
    |Intent| `intent.<plugin>.js`    | yes | no | Intent allows to check whether the sentence you are speaking validates a voice rule defined for the plugin|
    || `intents.<name>.js`    | no | no |It is possible to have several _intent_ files. For example, if you want to break down the check into several files.<br><br>Let's take the example of a TV plugin that has 2 _intents_ files, one for TV channel rules and another for controls (volume, on/off, etc.), , the _intents_ files will be:<br>- _intents.tvChannels.js_<br>- _intents.tvCommands.js_|
    |Action| `action.<plugin>.js`    | yes               | no   | If the sentence is verified by the _intent_ file and validates a voice rule, then the associated _action_ file is called.<br>The _action_ file is a pre-processor that gathers information sent by the _intent_ file and calls the plugin's _script_ file|
    | | `actions.<name>.js`    | no  | no   |If you have chosen to have multiple _intent_ files, then there must be the same number of _actions_ files.<br><br>Let's take the same example of a TV plugin (see above) that has 2 _intents_ files, one for TV channel rules and another for controls (volume, on/off, etc.), the _actions_ files will be:<br>- _actions.tvChannels.js_<br>- _actions.tvCommands.js_ |        
    |Script| `<plugin>.js`| yes | yes | Main script file in which the plugin is developed|
    |Properties| `<plugin>.prop`| yes | yes |The plugin properties file in `json` format|
    |Package| `package.json`| yes | no |`npm` package control file|
    |Language| `locales/<lang>.pak` | yes | no |Language localization file|
    |Information| `assets/infos.md`<br>or<br> `infos_<application language>.md`| yes | yes |Information file in [Markdown](https://www.markdownguide.org/basic-syntax/) format to be displayed on the plugin information page in _Plugin Studio_.<br><br>_application language_: Short code of the [application language](server-properties.md#application-language)<br>If this file exists, it is used otherwise `info.md` is used|
    |Image| `assets/<plugin>.png`| yes| no |Plugin image file in `.png` format|
    |...| `...` | N/A | no| Any other file useful to your plugin|

    (1) File created automatically if you use _Plugin Studio_ to create the plugin

=== "Client"

    |Type|<div style="width:95px">File</div>| Mandatory | Description|
    |:---:|:-----------                |:-:          | :------------------------------------|    
    |Script| `<plugin>.js`| yes | Main script file in which the plugin is developed|
    |Properties| `<plugin>.prop`| yes |The plugin properties file in `json` format|
    |Package| `package.json` |no |`npm` package control file|
    |Language| `locales/<lang>.pak`| no|Language localization file|
    |Information| `assets/infos.md`<br>or<br> `infos_<application language>.md`| yes |Information file in [Markdown](https://www.markdownguide.org/basic-syntax/) format to be displayed on the plugin information page in _Plugin Studio_.<br><br>_application language_: Short code of the [application language](server-properties.md#application-language)<br>If this file exists, it is used otherwise `info.md` is used|
    |Image| `assets/<plugin>.png`|no |Plugin image file in `.png` format|
    |...| `...` | no | Any other file useful to your plugin|


<br><br>
[:material-skip-previous: Create a plugin](tutorial-create-plugin.md){ .md-button style="float:left;"}[Natural Language Processing :material-skip-next:](tutorial-nlp.md){ .md-button style="float:right;"}   
<br><br>