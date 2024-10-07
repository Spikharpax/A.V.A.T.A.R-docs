# Client settings

Client settings are accessed via its name in the application interface.

![](img/client-menu.png){width="250"}

1. Left-click on the client's name to display its contextual menu

    /// note
    Left-click is deliberate for compatibility with graphic tablets without keyboard 
    ///

2. Click on _Settings_

**NOTE:** All the parameters below are described in the order in which they appear in the window.    

## Settings tab

![](img/client-properties.png){width="400"}

### `Show all informations in the console`
Select whether you want all information to be displayed in the console.  
This can be very useful if you want to know whether or not a plugin loads correctly on startup.

### `Application language`
Defines the language used for all graphical interfaces and application messages in the console.

???+ info
    The language used for speech rules is defined in the `Dialog` tab.

2 langages sont proposés avec l'application :

2 languages are offered by default with the application:

* English
* French
* \+ By default
    * The system's current language

    ???+ warning
        Setting `By default` assumes that the localization file for the current language exists 

You can translate a localization file into the language of your choice and add it to the application's languages: 

1. Copy one of the localization files to _app/locales/_, naming it with the country code (2 letters)
2. Modify the values of all keys

    ???+ warning
        The $$ characters are replaced by dynamic values when the message is displayed. Do not delete them.  

3. Add an entry in _app/locales/BCP47.loc_ to show the new language in the properties and use it.

    * For example, suppose a new location file _pt.loc_ is created
    * The new entry in the file will be:

    ```json
    {
        "1": {
            "tag": "en",
            "region": "English (US English)"
        },
        "2": {
            "tag": "fr",
            "region": "French (especially in France)"
        },
        "3": {
            "tag": "pt",
            "region": "Portuguese (Portugal)"
        }
    }
    ```

### `Client name`
The client name. 

### `Server search range`
The server's UDP search range.  
You can enter a search range in the form:

* XXX.XXX.XXX.XXX-XXX
* For example: for _192.168.2.100-110_, the client will look for the server in address 192.168.2.100 to 110.

You can also enter the server address directly

* For example: 192.168.2.100

### `Server communication port (HTTP)`
The communication port with the A.V.A.T.A.R. server.

??? warning
    This HTTP port number must be the same on the server and each client 

### `Server search port (UDP)`
The UDP port to search for the server.

* Default value: 3333

???+ warning
    This UDP port number must be the same on the server and each client

### `Delay between server searches`
Time in seconds between each network scan to find the server in the specified search range.

???+ Tip
    If the server can't be found, for example if a wrong address range has been set, you'll need to modify the property manually:

    1. Open the file _app/core/Avatar.prop_ in a text editor
    2. Find the `target` property and change its value
    3. Save the file and restart the client


### `Client communication port (HTTP)`
The communication port with the A.V.A.T.A.R. server.  

### `Client communication route`

The port used to access client plugins via HTTP requests (see [HTTP request](client-API.md#http-request) in the [Client API](client-API.md) for more details).


### `Number of automatic client restarts per 24h`

* Default value: 0 (no restart)

To refresh the server by restarting it regularly, you can enter a value between 1 and 24. 

???+ Tip "To know"
    In order not to interfere with automatic programs that may be launched at fixed times, 7 minutes are added to the selected times.

### Screen saver

Activates a screen saver after the server has been fully loaded.  
Useful, for example, if you use A.V.A.T.A.R. in tablet mode to automatically switch off the screen.

`Windows` : Black screen by default. No screensaver file required.

* If you want to use a specific screensaver:
    * Rename _app/lib/screensaver/win32/screensaver.bat_ to _app/lib/screensaver/win32/noscreensaver.bat_. The application will then automatically use the _app/lib/screensaver/win32/screensaver.vbs_ file
    * Then select a screensaver to be passed as a parameter
        * Select _C:\Windows\\System32\scrnsave.scr_ to have a black screen

`Linux` - `macOS` : Black screen by default. No screensaver file required.

* If you want to use a specific screensaver, edit the _app/lib/screensaver/<platform\>/screensaver.sh_ file
* Then select a screensaver to be passed as a parameter

### Updates

Choose whether you want the application to check for updates on startup.  
If Yes, and if a new version of the application is available, the flashing message “New version available!” will be displayed in the interface.


## Dialog tab

![](img/client-dialog-properties.png){width="450"}

In this tab, you can define the properties of voice rules

### `Dialog language`

Select the language used for voice rules.

??? Info
    The languages displayed are defined in the _app/locales/BCP47-dialog.loc_ file.

### `Voice regognition module`

This parameter defines the folder where the speech recognition module is located.

    app /
        core /
            chrome /

???+ Info
    The client's speech recognition uses the [Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API/Using_the_Web_Speech_API) interface, which can be accessed by all browsers. As a result, a client embades a version of Google Chrome when it is installed to ensure perfect compatibility with the client's version at all times.

    It is possible to develop another speech recognition module based on another application.  
    For more details, leave a message on the application's github.

### `Trigger keywords`

List of keywords that trigger listening.


### `Word separators`

Several actions can be defined in the same rule.  
When A.V.A.T.A.R finds one of these selectors in a sentence, it will divide the sentence into as many actions and execute them one after the other.

For example:

|Separator| Sentence  | Action |
|:-----|:---|:----------|
|and<br>then|stop the music<br><br><br>stop the music and turn on the TV<br><br><br>stop the music and turn on the TV, then put on the 1st channel|1. stop the music<br><br><br>1. stop the music<br>2. allume la télé<br><br>1. stop the music<br>2. turn on the TV<br>3. put on the 1st channel |

### `Restart listening`
The list of sentences that can be said when a problem has blocked listening in order to restart it.

### `Dialogue redirection`
A plugin can be used to redirect dialogs to speakers other than those on the client PCs.

For example, to [Sonos](https://www.sonos.com/) speakers for which a [SonosPlayer](https://github.com/Spikharpax/Avatar-Plugin-SonosPlayer) plugin exists.  

If the dialog is redirected, the voice is selected in the redirection plugin. The current voice and the voice list are no longer used.

For this example, the parameter will be:

![](img/client-sonos-dialog.png){width="400"}

???+ Info
    This setting is client-specific.  
    One client may have its dialogs redirected to a dedicated speaker, while another uses the speaker on its PC.


### `Loop mode`

Select whether you want the dialog to loop after each dialog.  

Take the following example:  
<span style="color:green;">You:</span> Jarvis (_trigger keyword_)  
<span style="color:orange;">A.V.A.T.A.R:</span> I'm listening  
<span style="color:green;">You:</span> stop the music  
<span style="color:orange;">A.V.A.T.A.R:</span> It's done  
<span style="color:orange;">A.V.A.T.A.R:</span> Do you need anything else? (_loop mode answer_)  
<span style="color:green;">You:</span> turn on the TV     
<span style="color:orange;">A.V.A.T.A.R:</span> It's done  
<span style="color:orange;">A.V.A.T.A.R:</span> anything else? (_loop mode answer_)  
etc...  
etc...  
<span style="color:orange;">A.V.A.T.A.R:</span> Do you need anything else? (_loop mode answer_)  
<span style="color:green;">You:</span> that's it thanks (_terminates loop mode_)  
<span style="color:orange;">A.V.A.T.A.R:</span> At your service (_end of dialogue in loop mode_)  

???+ Info
    Loop mode is only effective when a dialog is opened by the trigger keyword alone, without a rule.  
    Loop mode is compatible with [word separators](client-properties.md#word-separators).
    
    Key phrases are defined in the [Rules](#rules-tab) tab.

### `Automatic dialog stop`

Enter a value in seconds to have the dialog automatically interrupted if no sentence is spoken when listening is triggered.


### `Time before and after each speak`

Enter a value in seconds to leave a delay before and after an A.V.A.T.A.R. response, for example if you find a synchronization problem.


## Voices Tab

![](img/client-voices-properties.png){width="350"}

### `Voice preference`

=== "Local voices"
    
    Displays system voices in the list of [available voices](client-properties.md#available-voices).
    
    /// note | About Linux
    No system voices are available for Linux.  
    Selecting local voices will display the `espeak` and `mbrola` voices.

=== "Remote voices"

    Displays Google Chrome's [Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API/Using_the_Web_Speech_API) voices in the [available voices](client-properties.md#available-voices) list.

    | Platform| Disponible  | Comment |
    |:-----|:---:|:----------|
    |Windows|![ok](img/ok.png){width="12"}|Male and female voices available|
    |Linux|![ko](img/ko.png){width="12"}|No voice available|
    |MacOS|![ko](img/ko.png){width="12"}|No voice available|

 
### `Current voice`

The current voice used for dialogues


### `Selected voices`

Voices selected and used by the [change voice](client-properties.md#change-voice) rule.  


### `Available voices`

Displays system or remote voices, depending on the voice preference selected.

* `Select button` : 
    * Adds the voice to the list of selected voices
* `Bouton Define current` : 
    * Adds the voice to the list of selected voices and sets it as current


### `Test sentence`

Enter a phrase to test the voice, then click on the `Test` button.


### `Voice settings`  

* `Voice volume`
* `Voice speed`
* `Voice pitch`: 
    * The tone of voice
    * **NOTE:** For remote voices only


### `Change voice`

Enter a rule that will be used to change voices.  
Voices in the [available voices](client-properties.md#available-voices) list will be selected one after the other each time a voice is changed.


## Rules tab

![](img/client-rules-properties.png){width="350"}

The `Rules` tab is divided into 2 parts:

* The 1st section at the top, the phrases you can say:
    * To end the [loop mode](client-properties.md#loop-mode)
    * To end the dialog if it is triggered without a prompt (e.g. a false positive)
* The 2nd section below shows what the customer responds to:
    * When the dialog is triggered by the keyword
    * When the dialog loops in [loop mode](client-properties.md#loop-mode)
    * when the client doesn't understand your answer during an askme (question/answer)
    * When you end the dialog in [loop mode](client-properties.md#loop-mode)
    * When you end the dialog without prompting (e.g. a false positive)

## Interface tab

![](img/client-interface-properties.png){width="350"}

Customer interface management properties are grouped by type: 

* Client name properties
* Console properties
* Visualizer properties



## Intercom tab

![](img/client-intercom-properties.png){width="350"}

### `List of rules`
In order, the 4 lists of rules group together the sentences that can be said to trigger the intercom:

* Rules for intercom with one client
* Rules for intercom with all clients
* The sentence the client says to indicate that the recording is starting 
* The sentence the client says to indicate that the message has been sent to client(s)

### `Silence time`

Silence time in seconds for recording to stop and client to send message to client(s).

### `Silence level to start recording`
The lower the level, the higher the noise.

### `Silence level to stop recording`

The lower the level, the higher the noise.

### `Recording driver` 

waveaudio` is normally the driver used to generate the `.wav` file.  
If you wish, you can modify this driver for another type of recording.

### `Recording device`

`default` is normally the device used for registration.  
If you wish, you can change this device.

### `Debug mode` 

If you notice a problem, you can view more information in the client console.
    

## Background tab

![](img/background-properties.png){width="350"}

Choose a wallpaper in `jpg` format for the client graphical interface.

By default, the application offers a number of wallpapers in the _app/assets/images/background_ folder.

/// note
You can choose a wallpaper from any folder.  
When saved, it will be copied to the _app/assets/images/background_ folder.
///

<br><br>
[:material-skip-previous: Server settings](server-properties.md){ .md-button style="float:left;"}  
<br><br>