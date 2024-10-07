# Plugin Studio

Chaque plugin a un fichier de propriétés (_.prop_) au format `json` dans lequel toutes ses propriétés sont définies (voir [Les fichiers du plugin](tutorial-plugin-folder-files.md#plugin-files) pour plus de détails). 

_Plugin Studio_ vous offre la possibilité de gérer les propriétés des plugins beaucoup plus facilement que par un éditeur de texte.

En plus de [gérer les propriétés](#gestion-des-proprietes) des plugins, _Plugin Studio_ vous permet :

- D'afficher la [page d'information](#page-dinformation) des plugins
- D'[activer/désactiver](#activerdesactiver-un-plugin) un plugin
- D'ouvrir la [documentation HTML](#documentation-html) d'un plugin
- De [supprimer un plugin](#supprimer-un-plugin) 
- De [Transférer a plugin vers un client](#transferer-un-plugin) 
- De [crypter/Décrypter](#crypterdecrypter-une-chaine-de-caracteres) une chaîne de caractères (utile pour les mots de passes)
- De [traduire une phrase](#traduire-une-phrase) en Anglais (uniquement le _Plugin Studio_ serveur)


=== "Serveur"
    1. Cliquez sur le node du serveur pour ouvrir le menu contextuel
    2. Cliquez sur **`Edition`** -> **`Plugin Studio`**

    ![Screenshot](img/Plugin-Studio-server.png){width="350"}
    
=== "Client"
    1. Cliquez sur le nom du client pour ouvrir le menu contextuel
    2. Cliquez sur **`Edition`** -> **`Plugin Studio`**

    ![Screenshot](img/Plugin-Studio-client.png){width="350"}


 ![](img/plugin-studio-app.png){width="450"}


## Page d'information

La page d'information est généralement utilisée comme présentation et manuel d'utilisation d'un plugin.  

1. A gauche, ouvrez la liste des plugins et cliquez sur le plugin concerné
2. La page d'information s'affiche par défaut

???+ note
    Si le plugin est trop important, une documentation HTML est préconisée.


## Activer/Désactiver un plugin

1. A gauche, ouvrez la liste des plugins et cliquez sur le plugin concerné
2. L'onglet _Plugins_ se ferme, ouvrez de nouveau l'onglet _Plugins_ et cliquez une 2ème fois sur le plugin pour faire apparaître son menu contextuel
3. Suivant son état courant, cliquez sur _Activate_ ou _Desactivate_


## Documentation HTML

Certain plugin peuvent avoir une documentation trop importante pour être affichée dans la page d'information.  
Si le plugin dispose d'une documentation HTML, le menu _Documentation_ apparaîtra automatiquement dans son menu contextuel

1. A gauche, ouvrez la liste des plugins et cliquez sur le plugin concerné
2. L'onglet _Plugins_ se ferme, ouvrez de nouveau l'onglet _Plugins_ et cliquez une 2ème fois sur le plugin pour faire apparaître son menu contextuel
3. Cliquez sur _Documentation_


## Supprimer un plugin

1. A gauche, ouvrez la liste des plugins et cliquez sur le plugin concerné
2. L'onglet _Plugins_ se ferme, ouvrez de nouveau l'onglet _Plugins_ et cliquez une 2ème fois sur le plugin pour faire apparaître son menu contextuel
3. Cliquez sur _Delete_
4. Cliquez sur _Delete_ dans la fenêtre de confirmation

/// danger
Attention! supprimez un plugin est définitif ! 
///



## Gestion des propriétés

1. A gauche, ouvrez la liste des plugins et cliquez sur le plugin concerné
2. Cliquez sur l'onglet _Properties_ pour afficher les propriétés du plugin

Le menu de gestion est accessible par un click gauche à gauche de chaque ligne comme dans l'exemple ci-dessous: 

![](img/plugin-studio-menu.png){width="450"}

### Ajouter une propriété

Pour ajouter une propriété, cliquez sur une ligne puis utilisez _Append_ ou _Insert_ suivant ce que vous voulez faire:

- `Insert`: Ajoute une entrée **avant** la ligne sélectionnée
- `Append`: Ajoute une entrée **après** la ligne sélectionnée

/// Tip
Choisissez bien la ligne en fonction de l'emplacement où vous voulez ajouter une entrée.  

Un bon moyen de savoir si vous ajoutez l'entrée correctement est de réduire ou développer l'object ou le tableau parent par sa petite flêche (à gauche de son nom), la nouvelle entrée doit apparaître ou disparaître avec lui.
///

Sélectionnez ensuite le type d'entrée que vous désirez dans le sous-menu associé à la commande.  
Tableau des types d'entrées:

|Type| Description | Saisie| Résultat |
|:-----|:---|:---|:---|
|String|Chaîne de caractère|1. Entrez une clé<br>2. Entrez la valeur|La valeur apparaît en <span style="color:green;">vert</span><br>field : <span style="color:green;">By default</span>|
|Number|Valeur entière ou flottante|1. Cliquez sur _String_<br>2. Entrez une clé<br>3. Ajoutez une valeur<br>4. Ouvrez le sous-menu de l'entrée et cliquez sur _Type_<br>5. Cliquez sur _Auto_ ![](img/auto.png){width="15"}|La valeur apparaît en <span style="color:red;">rouge</span><br>field :<span style="color:red;"> 20</span><br>field :<span style="color:red;"> 20.98</span>|
|Boolean|Valeur boolean (_true_ ou _false_) |1. Cliquez sur _String_<br>2. Entrez une clé<br>3. Ajoutez une valeur _true_ ou _false_<br>4. Ouvrez le sous-menu de l'entrée et cliquez sur _Type_<br>5. Cliquez sur _Auto_ ![](img/auto.png){width="15"}|La valeur apparaît en <span style="color:orange;">orange</span> avec une petite case à cocher <br>![](img/boolean.png){width="120"}|
|Array|[ ] Tableau de valeurs|1. Cliquez sur _Array_<br>2. Entrez une clé<br>3. Ouvrez le sous-menu de l'entrée _(new array)_ et cliquez sur _Append_ puis sélectionnez le type d'entrée à ajouter au tableau<br>4. Pour ajouter une autre entrée, sélectionnez la dernière ligne créée et répetez l'opération|Les valeurs du tableau apparaissent de la couleur suivant le type|
|Object|{ } Collection de clés/valeurs|1. Cliquez sur _Array_<br>2. Entrez une clé<br>3. Ouvrez le sous-menu de l'entrée _(new object)_ et cliquez sur _Append_ puis sélectionnez le type d'entrée à ajouter à l'object<br>4. Pour ajouter une autre entrée, sélectionnez la dernière ligne créée et répetez l'opération|Les valeurs de l'object apparaissent de la couleur suivant le type|

### Supprimer une propriété

1. Cliquez sur la ligne à supprimer
2. Ouvrez le menu
3. Cliquez sur _Remove_

/// warning
L'action supprime l'entrée avec son contenu. Si le type est Array ou Object, toutes les entrées dessous seront supprimées
///

### Sauvegarder les propriétés

1. Cliquez dans l'interface des propriétés pour faire apparaitre le menu contextuel
2. Cliquez sur _Save_

### Recharger les propriétés

Utilisez cette commande si vous voulez recharger les propriétés sans redémarrer A.V.A.T.A.R

1. Cliquez dans l'interface des propriétés pour faire apparaitre le menu contextuel
2. Cliquez sur _Reload_

/// note
A la fermeture de _Plugin Studio_, l'application remarquera que vous avez modifié les propriétés et vous demandera si vous voulez redémarrer pour les recharger. Puisque vous l'avez fait manuellement, vous pouvez alors cliquez sur _Later_
///

## Crypter/Décrypter une chaîne de caractères

Cette interface de cryptage/décryptage d'une chaîne de caractères est utile pour sauvegarder dans le fichier de propriétés une valeur cryptée, comme par exemple un mot de passe.

1. Cliquez dans l'interface des propriétés pour faire apparaitre le menu contextuel
2. Cliquez sur _Encrypt/Decrypt_

### Crypter

1. Entrez une valeur **décryptée** à crypter dans le champ _Decrypted string_
2. Cliquez sur _Encrypt_
3. La valeur cryptée apparaît dans le champ _Encrypted string_

    ![](img/encrypt-test.png){width="350"}

4. Copiez la valeur cryptée (_CTRL-C_) et collez-là dans un champ valeur d'une propriété du plugin

![](img/encrypt-result.png){width="600"}

5. Sauvegardez les propriétés

### Décrypter

1. Entrez une valeur **cryptée** à décrypter dans le champ _Encrypted string_
2. Cliquez sur _Decrypt_
3. La valeur décryptée apparaît dans le champ _Decrypted string_


## Traduire une phrase

1. Cliquez dans l'interface des propriétés pour faire apparaitre le menu contextuel
2. Cliquez sur _Translate_

Copiez ensuite la phrase traduite pour la copier dans un champ valeur d'une propriété du plugin  
Si vous voulez, modifiez ensuite la phrase pour y ajouter une [correspondance](plugin-rules.md#correspondances)

<br><br>
[:material-skip-previous: Pour commencer](getting-started.md){ .md-button style="float:left;"} 
<br><br>