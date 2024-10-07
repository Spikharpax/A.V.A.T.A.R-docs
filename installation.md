# Introduction

L'installation d'A.V.A.T.A.R. se compose de 2 parties :

* [L'installation du serveur](server-install.md)
* [L'installation d'un client](client-install.md)
    * Le client peut être installé, au choix, sur le même poste que le serveur ou sur un poste dédié.
    * Pour plusieurs clients, vous pouvez répétez l'installation ou zipper la 1ère installation pour la copier sur les autres postes et ne faire que les parties nécessaires.
    * Si vous choisissez d'installer le serveur et le client sur le même poste, vérifiez qu'il y a suffisament d'espace disque et de mémoire.

Ci-dessous les prérequis d'installation :

|Type| RAM | Espace disque |
|:-----|:---:|:---:|
|Serveur|4Go mini / 8Go préféré|1 Go|
|Client|4Gb mini / 8Go préféré|2 Go|


## Prérequis
Pour procéder à l'installation, [Node.js et npm](https://nodejs.org/) doivent être installés sur chaque plateforme.

* `Windows`: Vous pouvez utiliser le [Prebuild Installer](https://nodejs.org/en/download/prebuilt-installer)  
* `Linux`: Vous pouvez utiliser le [Package Manager](https://nodejs.org/en/download/package-manager) et choisir `Linux` et `nvm`
* `MacOS`: Vous pouvez utiliser le [Package Manager](https://nodejs.org/en/download/package-manager) et choisir `MacOS` et `nvm`

Suivez la procédure d'installation associée au choix

Après l'installation, ouvrez un terminal et vérifiez que vous disposez de `Node.js` et `npm` à la dernière version dans le PATH du système:

```
# verifies the right Node.js version is in the environment
node -v # should print `v20.15.1` or newer

# verifies the right npm version is in the environment
npm -v # should print at least `10.7.0` or newer
```

<br>
[Installation du serveur :material-skip-next:](server-install.md){ .md-button style="float:right;"}   
<br><br>