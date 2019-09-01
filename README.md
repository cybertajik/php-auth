# php-auth
2FA Authentication Web Login

PECL-Extension 'GnuPG' required!
Root access to the server required!

### Usage

Upload 'auth.php' to your webserver, follow the steps.

If pgp is used with a standard MySQL-based login, paste the code to the login form.
Start the session:
```bash
<?php
session_start();
?>
```
Then you have to include the auth class and create a new instance:
```bash
<?php
include('/path/to/auth.php');
$pgp = new auth();
?>
```
Now generate new secret code. The default length is string of 25 numbers.
Adjusted generation variables for your own needs.
After invoking this function, the unencrypted form of the secret is saved within the instance of the class for the next step, and a hashed and safe form of this secret is stored in the session:
```bash
<?php
$pgp->generateSecret();
?>
```
After generating the secret, you can encrypt it with PGP with a given Public Key:
(In most cases, the public key is stored in a MySQL database so you have to connect to your database and retrieve the public key for the user that is currently logging in)
```bash
<?php
$pgp_message = $pgp->encryptSecret($public_key);
?>
```
The complete code until now should look something like this:
```bash
<?php
session_start();

include('/path/to/auth.php');

$pgp = new auth();
$pgp->generateSecret();

$pgp_message = $pgp->encryptSecret($public_key);
?>
```
The $pgp_message variable contains the PGP message the user has to decrypt.
This message should be displayed together with an input where the user can type in the decrypted code.

To compare the user given code with the real code , just use compare() in your Form validation process:
```bash
<?php
if($pgp->compare($_POST['user-input'])){
  // Success!
}else{
  // Failure!
}
?>
```

### Install GnuPG PHP Extension
#### 1. Install required packages
```bash
apt-get install build-essential libssl-dev
apt-get install gnupg libgpg-error-dev libassuan-dev libgpgme11-dev
apt-get install php7.3-dev php-pear
```
#### 2. Download and build GPGME
Version 1.13.1 is the latest as of 1st September 2019, to check the version visit https://www.gnupg.org/ftp/gcrypt/gpgme/ 
```bash
wget https://www.gnupg.org/ftp/gcrypt/gpgme/gpgme-1.13.1.tar.bz2
```
Extract archive and cd to directory:
```bash
tar xfvj gpgme-1.13.1.tar.bz2
cd gpgme-1.13.1
```
Configure, make and install GPGME:
```bash
configure
make
make install
```
#### 3. Install the PHP extension:
```bash
pecl install gnupg
```
Open your php.ini and add 'extension=gnupg.so':
```bash
extension=gnupg.so
```
#### Done!
