# HTTPS certificate

## Prerequisites

### OpenSSL

`Windows`: 

Download the latest version of OpenSSL

* For example, from [fireDeamon](https://www.firedaemon.com/download-firedaemon-openssl)
* Install OpenSSL in a folder

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

Install OpenSSL with [Homebrew](https://brew.sh/)
```
brew install openssl
```

## Create the CA (Certificate Authority)
Instead of having the certificate validated by a paying authority, we'll generate our own CA.

???+ warning
    We assume that openssl is in the system PATH, otherwise add the path to openssl to all the following commands

1. Open a terminal and create a _/certificates/CA_ folder
2. Move to this folder
3. Generate the private key
    ```
    openssl genrsa -des3 -out ./avatarCA.key 2048
    ```

    During generation, a passphrase is requested. This will be used to sign host certificates.

    ```
    Generating RSA private key, 2048 bit long modulus (2 primes)
    ........................................................................+++++
    ..........................................+++++
    e is 65537 (0x010001)
    Enter pass phrase for avatarCA.key:
    Verifying - Enter pass phrase for avatarCA.key:
    ```

4. Generate the root certificate in `.pem` format

    ```
    openssl req -x509 -new -nodes -key ./avatarCA.key -sha256 -days 10000 -out ./avatarCA.pem
    ```

    The certificate is generated for a period of 10,000 days.  
    At the time of generation, the passphrase of the previous key is requested.  
    A number of questions are asked, which are answered as shown here:

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

5. Generate the root certificate in `crt` format

    ```
    openssl x509 -in ./avatarCA.pem -inform PEM -out ./avatarCA.crt
    ```

    We now have 3 files in the _certificates/CA_ folder:

    - avatarCA.key => La clé privée
    - avatarCA.pem => Certificat racine au format pem
    - avatarCA.crt => Certificat racine au format crt

6. Deploy the root certificate in the certificate store

    `Windows`: 

    * In the taskbar, search for `certificate`
    * Click on `Manage user certificates`
    * In the left-hand menu, click on `trusted root certification authorities` and then on `Certificates`
    * Right-click on `Certificates` then `All tasks` and `Import`
    * In the 1st window, click on `next`
    * Import the root certificate _avatarCA.crt_
    * Click on `next`
    * The certificate store must be `trusted root certification authorities`
    * Click on `next`
    * Confirm with `yes` to all other windows
    * The message `Import successful` is displayed
    * The root certificate is now displayed in the certificate list

    `Linux`:

    * If it doesn't exist, create the _/usr/local/share/ca-certificates/_ folder (as root)
    * Check its permissions, which must be 755
    * Create a folder _/usr/local/share/ca-certificates/avatar_ (as root)
    * Copy the file _avatarCA.pem_ (or _avatarCA.crt_ or both) to _/usr/local/share/ca-certificates/avatar/_
    * Update certificates 

        ```
        update-ca-certificates
        ```

    * The command returns:

        ```
        Updating certificates in /etc/ssl/certs...
        1 added, 0 removed; done.
        Running hooks in /etc/ca-certificates/update.d...
        done.
        ```

 7. Add the certificate to Chrome

    `Windows`: 
    
    No action required. Chrome for Windows uses system-level certificates  

    `Linux`: 

    * Search for the local database `cert9.db` containing the certificates for the user profile
    ```
    find ~ -name "cert9.db"
    ```

    * My search returns :
    ```
    /home/avatar/.pki/nssdb/cert9.db
    /home/avatar/.mozilla/firefox/va572v31.default/cert9.db
    ```
    
    The _.pki_ folder is used by Chrome

    * Add the certificate to the database
    ```
    certutil -A -n "avatar" -t "C," -i /usr/local/share/ca-certificates/avatar/avatarCA.pem -d sql:$HOME/.pki/nssdb/
    ```

    ??? note "Note for Linux/MacOS"
        If the `certutil` command doesn't exist, install the `libnss3-tools` package

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

        Install OpenSSL with [Homebrew](https://brew.sh/)
        ```
        brew install libnss3-tools
        ```
    * Installed certificates can be checked with:
        ```
        certutil -d sql:$HOME/.pki/nssdb -L
        ```

## Create a certificate for the host client
Now that we have an in-house CA installed on the server, we can generate a certificate for the client host and sign it with the CA.

/// warning 
In this example, the server is **sb-portable** (the name of my server).  
This name will be used throughout the procedure. Change **sb-portable** to **your** server name.
///

1. Create a _/certificates/hote_ folder
2. In the terminal, move to this folder
3. Generate the private key
    ```
    openssl genrsa -out ./sb-portable.key 2048
    ```
4. Generate the CSR (Certificate Signing Request)
    ```
    openssl req -new -key ./sb-portable.key -out ./sb-portable.csr
    ```

    A number of questions are answered.
    You must enter the server name as it is called from the outside in the CN (Common Name) field:

    For me, **sb-portable**, for you, **your** server name

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

5. Create the configuration file for the subdomain

    * Edit a _sb-portable.ext_ file
    * Add these values to the file, setting DNS.1 to the server name

    For me, **sb-portable**, for you, **your** server name

    ```
    authorityKeyIdentifier=keyid,issuer
    basicConstraints=CA:FALSE
    keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
    subjectAltName = @alt_names
    [alt_names]
    DNS.1 = sb-portable
    ```

6. Sign the certificate with the CA, valid for 10,000 days 

    ```
    openssl x509 -req -in ./sb-portable.csr -CA ../CA/avatarCA.pem -CAkey ../CA/avatarCA.key -CAcreateserial -out ./sb-portable.crt -days 10000 -sha256 -extfile ./sb-portable.ext
    ```
    
    The CA's passphrase is requested to validate the certificate signature:

    ```
    Certificate request self-signature ok
    subject=C=FR, ST=ile de france, L=Paris, O=avatar, OU=avatar, CN=sb-portable
    Enter pass phrase for ../CA/avatarCA.key:
    ```

    We now have 4 files (for me **sb-portable**, for you, **your** server name):

    - sb-portable.key => The private key of the subdomain certificate
    - sb-portable.csr => Certificate signature request
    - sb-portable.crt => Subdomain certificate in `crt` format
    - sb-portable.ext => Subdomain configuration file


Certificate generation is now complete. You can now return to client installation

<br><br>
[:material-skip-previous: Back to Client installation - HTTPS certificate](client-install.md/#https-certificate){ .md-button style="float:left;"}
<br><br>