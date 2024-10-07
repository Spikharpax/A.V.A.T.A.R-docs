# Certificate HTTPS 

## Prérequis

### OpenSSL

`Windows`: 

Téléchargez la dernière version de OpenSSL

* Par exemple depuis [fireDeamon](https://www.firedaemon.com/download-firedaemon-openssl)
* Installez OpenSSL dans un dossier

`Linux Debian / Ubuntu`:
    ```
    sudo apt-get update
    sudo apt-get install openssl
    ```

`Linux Fedora`:
    ```
    sudo yum update
    sudo yum install openssl
    ```

`MacOS`:

Installez OpenSSL avec [Homebrew](https://brew.sh/)
```
brew install openssl
```

## Créer le CA (Certificate Authority)
Au lieu de faire valider le certificat par une autorité payante, nous allons générer notre CA personnel.

???+ warning "Important"
    Nous prenons pour acquis que openssl est dans le PATH système sinon ajoutez le chemin d'accès à openssl dans toutes les commandes suivantes

1. Ouvrez un terminal et créez un dossier _/certificates/CA_
2. Déplacez-vous dans ce dossier
3. Générez la clé privée
    ```
    openssl genrsa -des3 -out ./avatarCA.key 2048
    ```

    Lors de la génération, il est demandé une passphrase. Elle nous servira pour signer les certificats des hôtes.

    ```
    Generating RSA private key, 2048 bit long modulus (2 primes)
    ........................................................................+++++
    ..........................................+++++
    e is 65537 (0x010001)
    Enter pass phrase for avatarCA.key:
    Verifying - Enter pass phrase for avatarCA.key:
    ```

4. Générez le root certificate (certificat racine) au format `.pem`

    ```
    openssl req -x509 -new -nodes -key ./avatarCA.key -sha256 -days 10000 -out ./avatarCA.pem
    ```

    Le certificat est généré pour une durée de 10.000 jours.  
    Au moment de la génération, il est demandé la passphrase de la clé précédente.  
    Un certain nombre de questions sont posées, y répondre comme mis en exemple ici :

    ```
    Enter pass phrase for avatarCA.key:
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:FR
    State or Province Name (full name) [Some-State]:Ile de france
    Locality Name (eg, city) []:Paris
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:avatar
    Organizational Unit Name (eg, section) []:
    Common Name (e.g. server FQDN or YOUR name) []:avatar
    Email Address []:
    ```

5. Générez le root certificate (certificat racine) au format `crt`

    ```
    openssl x509 -in ./avatarCA.pem -inform PEM -out ./avatarCA.crt
    ```

    Nous avons maintenant 3 fichiers dans le dossier _certificates/CA_ :

    - avatarCA.key => La clé privée
    - avatarCA.pem => Certificat racine au format pem
    - avatarCA.crt => Certificat racine au format crt

6. Déployez le certificat racine dans le magasin de certificats

    `Windows`: 

    * Dans la barre de tâches, recherchez `certificat`
    * Cliquez sur `Gérer les certificats utilisateurs`
    * Dans le menu à gauche, cliquez sur `trusted root certification authorities` puis sur `Certificates`
    * Faites un clic droit sur `Certificates` puis `All tasks` et `Import`
    * Dans la 1ère fenêtre, cliquez sur `suivant`
    * Importez le root certificate _avatarCA.crt_
    * Cliquez sur `suivant`
    * Le magasin de certificats doit être `trusted root certification authorities`
    * Cliquez sur `suivant`
    * Validez par `oui` à toutes les autres fenêtres
    * Le message `Import réussi` s'affiche
    * Le root certificate est maintenant affiché dans la liste des certificats

    `Linux`:

    * Si il n'existe pas, créez le dossier _/usr/local/share/ca-certificates/_ (en root)
    * Vérifiez ses permissions qui doivent être en 755
    * Créez un dossier  _/usr/local/share/ca-certificates/avatar_ (en root)
    * Copiez le fichier _avatarCA.pem_ (ou _avatarCA.crt_ ou les 2) dans _/usr/local/share/ca-certificates/avatar/_
    * Mettez à jour les certificats 

        ```
        update-ca-certificates
        ```

    * La commande retourne :

        ```
        Updating certificates in /etc/ssl/certs...
        1 added, 0 removed; done.
        Running hooks in /etc/ca-certificates/update.d...
        done.
        ```

 7. Ajoutez le certificat dans Chrome

    `Windows`: 
    
    Aucune action requise. Chrome pour Windows utilise les certificats définis au niveau système  

    `Linux`: 

    * Cherchez la base locale `cert9.db` contenant les certificats pour le profil utilisateur
    ```
    find ~ -name "cert9.db"
    ```

    * Chez moi, la recherche retourne :
    ```
    /home/avatar/.pki/nssdb/cert9.db
    /home/avatar/.mozilla/firefox/va572v31.default/cert9.db
    ```
    
    Le dossier _.pki_ est utilisé par Chrome

    * Ajoutez le certificat à la base
    ```
    certutil -A -n "avatar" -t "C," -i /usr/local/share/ca-certificates/avatar/avatarCA.pem -d sql:$HOME/.pki/nssdb/
    ```

    ??? note "Note pour Linux/MacOS"
        Si la commande `certutil` n'existe pas, installez le package `libnss3-tools`

        `Linux Debian / Ubuntu`:
        ```
        sudo apt-get update
        sudo apt-get install libnss3-tools
        ```

        `Linux Fedora`:
        ```
        sudo yum update
        sudo yum install libnss3-tools
        ```

        `MacOS`:

        Installez OpenSSL avec [Homebrew](https://brew.sh/)
        ```
        brew install libnss3-tools
        ```
    * On peut vérifier les certificats installés avec :
        ```
        certutil -d sql:$HOME/.pki/nssdb -L
        ```

## Créer un certificat pour le client hôte
Maintenant qu'on a un CA maison et installé sur le serveur, on va pouvoir générer un certificat pour le client hôte et le signer avec le CA.

/// warning 
Dans cet exemple, le serveur est **sb-portable** (le nom de mon serveur).  
Dans toute la procédure, ce nom sera utilisé. Changez **sb-portable** par le nom de **votre** serveur.
///

1. Créez un dossier _/certificates/hote_
2. Dans le terminal, déplacez-vous dans ce dossier
3. Générez la clé privée
    ```
    openssl genrsa -out ./sb-portable.key 2048
    ```
4. Générez le CSR (Certificate Signing Request)
    ```
    openssl req -new -key ./sb-portable.key -out ./sb-portable.csr
    ```

    On répond à un certain nombre de questions.
    Il faut bien mettre le nom du serveur tel qu'il est appelé de l'extérieur dans le champ CN (Common Name) :

    Pour moi, **sb-portable**, pour vous, le nom de **votre** serveur

    ```
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:FR
    State or Province Name (full name) [Some-State]:ile de france
    Locality Name (eg, city) []:Paris
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:avatar
    Organizational Unit Name (eg, section) []:avatar
    Common Name (e.g. server FQDN or YOUR name) []:sb-portable
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
    ```

5. Créez le fichier de configuration pour le sous domaine

    * Editez un fichier _sb-portable.ext_
    * Ajoutez ces valeurs dans le fichier en mettant dans DNS.1 le nom du serveur

    Pour moi, **sb-portable**, pour vous, le nom de **votre** serveur

    ```
    authorityKeyIdentifier=keyid,issuer
    basicConstraints=CA:FALSE
    keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
    subjectAltName = @alt_names
    [alt_names]
    DNS.1 = sb-portable
    ```

6. Signez le certificat avec le CA et pour une validité de 10.000 jours 

    ```
    openssl x509 -req -in ./sb-portable.csr -CA ../CA/avatarCA.pem -CAkey ../CA/avatarCA.key -CAcreateserial -out ./sb-portable.crt -days 10000 -sha256 -extfile ./sb-portable.ext
    ```
    
    La passphrase du CA est demandée pour valider la signature du certificat :

    ```
    Certificate request self-signature ok
    subject=C=FR, ST=ile de france, L=Paris, O=avatar, OU=avatar, CN=sb-portable
    Enter pass phrase for ../CA/avatarCA.key:
    ```

    Nous avons maintenant 4 fichiers (pour moi **sb-portable**) :

    - sb-portable.key => La clé privée du certificat du sous domaine
    - sb-portable.csr => La demande de signature du certificat
    - sb-portable.crt => Certificat du sous domaine au format crt
    - sb-portable.ext => Fichier de configuration du sous domaine


La génération des certificats est terminée. Vous pouvez revenir à l'installation du client

<br><br>
[:material-skip-previous: Retour à Installation du client - Certificats HTTPS](client-install.md/#certificats-https){ .md-button style="float:left;"}
<br><br>