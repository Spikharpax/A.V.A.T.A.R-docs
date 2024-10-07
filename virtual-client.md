# Le client virtuel

Un client virtuel est un client qui n'existe pas réellement mais qui peut être interprété dans une règle comme un vrai client.  
Si une règle vocale contient un client virtuel, il sera automatiquement retourné dans les plugins par la variable _data.toClient_.

Prenons la règle suivante passée depuis le client `Living room` et où `Kitchen` est un client virtuel:

```
allume la lumière dans la cuisine
```

La fonction _action_ du plugin aura dans son object `data` les valeurs suivantes :

```json
{
  client: 'Living room',
  toClient: 'Kitchen',
  ... // Other parameters
}
```

_data.toClient_ pourra alors être utilisé directement dans une action:

```
turn on the light in ${data.toClient}
```

## Créer un client virtuel

Un client virtuel est obligatoirement créé depuis un vrai client.  
Pour créer un client virtuel:

1. Faite un clic gauche sur un client dans l'interface du serveur 
2. Sélectionnez le menu _Settings_ 
3. Entrez un nom de client virtuel

    ![](img/virtual-client.png){width="400"}

4. Cliquez sur _Save_
5. Redémarrez A.V.A.T.A.R
6. Placez le node du client virtuel dans l'interface

/// note
La position du node est automatiquement sauvegardé quand vous quittez A.V.A.T.A.R
///

Le client virtuel a une image par défaut.  
Pour sélectionner une autre image :

1. Faite un clic gauche sur le client virtuel
2. Cliquez sur _Settings_

    ![](img/virtual-client-interface.png){width="300"}

3. Choisissez une image au format `.png`
4. Testez dans l'interface ou/et cliquez sur _Save_

    ![](img/virtual-client-image.png){width="300"}


/// note
Vous pouvez choisir une image au format `.png` dans n'importe quel répertoire.  
A la sauvegarde, il sera copié dans le répertoire _app/assets/images/rooms_
///


<br><br>
[:material-skip-previous: Pour commencer](getting-started.md){ .md-button style="float:left;"} 
<br><br>