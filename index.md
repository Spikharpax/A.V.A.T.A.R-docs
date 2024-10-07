# Welcome to A.V.A.T.A.R.
<figure markdown="span">
  ![](img/avatar.jpg)
</figure>

A.V.A.T.A.R. is an open source client-server web-based speech recognition assistant dedicated to the design of intelligent homes and the Internet of Things.  
A.V.A.T.A.R. is developed using the [Electron](https://www.electronjs.org/) framework, which integrates [Chromium](https://www.chromium.org/chromium-projects/) and [Node.js](https://nodejs.org/) into its executable.

<figure markdown="span">
  ![](img/logo.png)
</figure>

## Principle of use

The principle is to dictate a rule vocally via a client, which is then sent to the server. The server translates it into English and analyzes it using [Natural Language Processing (NLP)](https://en.wikipedia.org/wiki/Natural_language_processing).  
If an intention is found, the server triggers the associated script. These scripts (or plugins) developed in [Node.js](https://nodejs.org/) then interact with all the connected objects that can be controlled (home automation box, TV box, TV, Wi-Fi speaker, Sonos, lamp, gadget...), Open Data (TV programs, cinema schedules, world weather, wikipedia...) or even Google Assistant.

<figure markdown="span">
  ![](img/schema.jpg){width="600"}
</figure>

A.V.A.T.A.R. is the “core” of your applications, so all you have to do is develop the plugins that interoperate with what you want to drive, and A.V.A.T.A.R. takes care of the rest. To this end, A.V.A.T.A.R. offers you a development API and a host of tools for easily creating and managing your plugins, as well as a library of plugins made available by the community of contributors.  
If you want to dispense with voice recognition, A.V.A.T.A.R. also offers a tool for creating and managing button widgets in server and client interfaces. You can also add your own JS/HTML/CSS windows to the application's interfaces.


## Supported platforms

|System| Version  | Server |Client| Comment |
|:-----|:---|:----------:|:----------:|:----------|
|Windows| >= 10 | ![ok](img/ok.png){width="12"} | ![ok](img/ok.png){width="12"} | The ultimate platform for voice choices|
|Linux| Debian >= 12<br>Fedora >= 32<br>Ubuntu >= 18.04|  ![ok](img/ok.png){width="12"} | ![ok](img/ok.png){width="12"} | Available voices by `espeak`|
|Mac0S| Catalina et supérieur|![ok](img/ok.png){width="12"} | ![ok](img/ok.png){width="12"} | Available voices |


A.V.A.T.A.R. was developed on Windows 10 and Linux (Debian 12).  
The following platforms were tested:

|System| Version | arch | Test serveur OK? |Test<br>client OK?| Comment |
|:-----|:---:|:---:|:----------:|:----------:|:----------|
|Windows| 10 | x64 | ![ok](img/ok.png){width="12"} | ![ok](img/ok.png){width="12"} ||
|Windows| 11| x64 | ![ok](img/ok.png){width="12"} | ![ok](img/ok.png){width="12"} |  |
|Debian| 12| x64 |![ok](img/ok.png){width="12"} | ![ok](img/ok.png){width="12"} | |
|MacOS| | x64 |![ok](img/ok.png){width="12"} | ![ok](img/ok.png){width="12"} | |
|Raspberry Pi| Raspberry Pi OS| arm | ![ok](img/ok.png){width="12"}| ![ok](img/ko.png){width="12"}| No voice recognition available, no voice available|

### Alternative architectures

* RHEL / CentOS
* Manjaro

[Electron](https://www.electronjs.org/) is compatible with these architectures. It is therefore theoretically possible to install A.V.A.T.A.R. on these platforms.


<br><br>

