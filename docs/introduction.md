# Introduction

The A.V.A.T.A.R. installation consists of 2 parts:

* [Server installation](server-install.md)
* [Installing a client](client-install.md)
    * The client can be installed either on the same workstation as the server, or on a dedicated workstation.
    * For several clients, you can repeat the installation or zip the 1st installation to copy it to the other workstations and only do the necessary parts.
    * If you choose to install the server and a client on the same workstation, check if there is enought disk space and memory.

Below are the installation requirements:

|Type| RAM | Disk space |
|:-----|:---:|:---:|
|Server|4Go mini / 8Go preferred|1 Go|
|Client|4Gb mini / 8Go preferred|2 Go|


## Prerequisites
To install, [Node.js and npm](https://nodejs.org/) must be installed on each platform.

* `Windows`: You can use the [Prebuild Installer](https://nodejs.org/en/download/prebuilt-installer)  
* `Linux`: You can use the [Package Manager](https://nodejs.org/en/download/package-manager) and choose `Linux` et `nvm`
* `MacOS`: You can use the [Package Manager](https://nodejs.org/en/download/package-manager) and choose `MacOS` et `nvm`

Follow the installation procedure associated with the choice

After installation, open a terminal and check that you have the latest version of `Node.js` and `npm` in the system PATH:

```
# verifies the right Node.js version is in the environment
node -v # should print `v20.15.1` or newer

# verifies the right npm version is in the environment
npm -v # should print at least `10.7.0` or newer
```

<br>
[Server installation :material-skip-next:](server-install.md){ .md-button style="float:right;"}   
<br><br>