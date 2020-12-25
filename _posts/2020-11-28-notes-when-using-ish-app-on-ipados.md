---
layout: post
title:  "Notes When Using Linux Shell iSH app on iPadOS"
date:   2020-11-28 14:34:25
categories: it
tags: app
---

iSH app brings a linux shell to iPad. I mainly use it for `git push` and some `python3` scripts. 

# iSH is now available on App Store

Just in case you are still using **iSH** through **TestFight**, it is to inform you that it is now available on App Store.

# Mount other app dir on iPad dir to iSH

```
mount -t ios . /mnt
```
Then choose a folder through **Files** app. Then we can `cd /mnt` and use it as a `pwd`
# Alias commands or run commands on startup

Create a new profile file at:
```bash
nano /etc/profile.d/profile.sh
```
Then add your commands
For example:

```bash
alias ll="ls -alh"
alias gitt="git add .;git commit -m 'auto git push';git push"
cd /mnt/
echo "You are now in /mnt/, be mindful! Change mount dir with:"
echo "mount -t ios . /mnt"
```
#  Python3 server with upload function 

Simple Python HTTP Server with Upload: [SimpleHTTPServerWithUpload.py](https://gist.github.com/UniIsland/3346170)

`python3 SimpleHTTPServerWithUpload.py`

# Trick to run iSH background

If you need to run iSH in background, for example with python module `http.server`, try this:

+ Create and chmod executable file `runBackground.sh`
+ Add these lines:
```bash
cat /dev/location > /dev/null &
python3 -m http.server ~/www/
```
`~/www/` is the path of your website.

+ Now turn on your device Location service in the device setting, and allow iSH use your location while running.
+ Run `sh runBackground.sh`
+ Now open Safari and access: `http://localhost:8000/`
Change the port `8080` accordingly to your case. You can now browse with Safari while iSH is still running in the background.

**Note**: 

  - Using `cat /dev/location` and turned on location service will consume more battery.
  - Simply `Split View` iSH over Safari to achieve the same effect.
  - You can also use `phpwin` app on iPad to view off-line websites.

# Install PHP and composer on iSH

### To install php

```bash
apk add php7 php7-phar php7-mbstring php7-json php7-openssl
```
### To install composer on iSH

Create and chmod 777 `installComposer.sh` with the below bash script:

``` bash
# This snippet is from: https://getcomposer.org/doc/faqs/how-to-install-composer-programmatically.md
#!/bin/sh
EXPECTED_CHECKSUM="$(wget -q -O - https://composer.github.io/installer.sig)"
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
ACTUAL_CHECKSUM="$(php -r "echo hash_file('sha384', 'composer-setup.php');")"
if [ "$EXPECTED_CHECKSUM" != "$ACTUAL_CHECKSUM" ]
then
    >&2 echo 'ERROR: Invalid installer checksum'
    rm composer-setup.php
    exit 1
fi
php composer-setup.php --quiet
RESULT=$?
rm composer-setup.php
exit $RESULT
```
Run `sh installComposer.sh` to download and install composer.

To have the command `composer` available globally, we can use this command:

```bash
mv composer.phar /usr/local/bin/composer
```


# Web server php with apache
## php & apache
An easy way to install a web-server with php & apache is using this install script:
``` bash
git clone https://github.com/timtim8461/ish-webserver-setup.git
cd ish-webserver-setup/
chmod 777 *.sh
chmod +x setup.sh
chmod +x start.sh 
./setup.sh
```
## Find php.ini and install SQLite3 extension
Enable **SQLite3** extension. SQLite3 is shipped with php, so we mostly  just only need to enable it in the php.ini. However, on iSH we may need to install it manually:

```bash
p:~# apk search sqlite3
p:~# apk add php7-sqlite3
```
We can find **php.ini** location with these commands:
+ The better way (faster) is:
```bash
php -i | grep php.ini
```
+ Or slower one:
```bash
p:~# cd /
p:/# find . -name 'php.ini'
./etc/php7/php.ini
```
And now we can edit it with nano:
```bash
p:/# nano /etc/php7/php.ini
```
In nano, press Ctrl + w and find **sqlite3** and remove `;` just before it to enable it. We will get something:
```ini
;extension=sockets
extension=sqlite3
;extension=tidy
```
You may need to restart the server to get SQLite3 extension working.
Check again, you will see it was already loaded.

```bash
p:~# php -i | grep php.ini
PHP Warning:  Module 'sqlite3' already loaded in Unknown on line 0
Configuration File (php.ini) Path => /etc/php7
Loaded Configuration File => /etc/php7/php.ini
```
# Install  Java openjdk

Install
```java
apk search openjdk
apk add openjdk8
```
If running `java -version` yields error, try this command:
```bash
java -mx512m -version
```
You can now try running a jar file:
```bash
java -mx512m -jar test.jar
```
If running ` javac -version` yields error
```
javac -version
ash: javac: not found
```
Try to set these paths:
```
##### 
export JAVA_HOME=/usr/lib/jvm/java-1.8-openjdk
export PATH="$JAVA_HOME/bin:${PATH}"
```
You can put these commands to:
`/etc/profile.d/profile.sh`
to set these vars automatically every time you start up the terminal.
+ If you have a newer version, try updating the version in the above command, like `/usr/lib/jvm/current??version`
If things go smooth, you will get response something like:
```bash
javac -version
javac 1.8.0_252
```
To remove these above path
List env with `export` or `env`
```
env
export
```
```
export
unset JAVA_HOME
unset PATH
```
# pynvim
On iSH (iPad), if `pip3 install pynvim` yields errors (**greenlet**)

```
include/python3.8/Python.h:11:10: fatal  error: limits.h: No such file or directory
#include <limits.h> 
                  ^
      compilation terminated.
```

We can try fixing it with this command:

``` bash
apk add --no-cache gcc linux-headers musl-dev
```

That's it for now.