# osx-tor

(UPDATE 12/15/16: These instructions are out of date. References to tor-0.2.6.7 below need to be updated with the current version of Tor served by Homebrew. The general procedure should still work.)

How to set Tor to run as background process on every OSX login

I wanted to install Tor in a Mac OSx VM and set it to launch on login, running in the background at all times. This makes it available to access hidden services or to pipe clients like Adium for Jabber chat through Tor.

Various instructions I found online came close but didn't work. With much help from https://github.com/garrettr I got it running. Figured I should post the blow by blow.

These instructions begin from scratch, without assuming anything is installed.

FIRST INSTALL HOMEBREW
======================

ENTER NEW COMMAND (~) > ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

    ==> This script will install:
    /usr/local/bin/brew
    /usr/local/Library/...
    /usr/local/share/man/man1/brew.1
    ==> The following directories will be made group writable:
    /usr/local/.
    /usr/local/bin
    /usr/local/include
    /usr/local/lib
    /usr/local/lib/pkgconfig
    ==> The following directories will have their group set to admin:
    /usr/local/.
    /usr/local/bin
    /usr/local/include
    /usr/local/lib
    /usr/local/lib/pkgconfig

    Press RETURN to continue or any other key to abort
    ==> /usr/bin/sudo /bin/chmod g+rwx /usr/local/. /usr/local/bin /usr/local/include /usr/local/lib /usr/local/lib/pkgconfig
    Password:
    ==> /usr/bin/sudo /usr/bin/chgrp admin /usr/local/. /usr/local/bin /usr/local/include /usr/local/lib /usr/local/lib/pkgconfig
    ==> /usr/bin/sudo /bin/mkdir /Library/Caches/Homebrew
    ==> /usr/bin/sudo /bin/chmod g+rwx /Library/Caches/Homebrew
    ==> Downloading and installing Homebrew...
    remote: Counting objects: 3585, done.
    remote: Compressing objects: 100% (3430/3430), done.
    remote: Total 3585 (delta 35), reused 1451 (delta 19), pack-reused 0
    Receiving objects: 100% (3585/3585), 2.75 MiB | 5.16 MiB/s, done.
    Resolving deltas: 100% (35/35), done.
    From https://github.com/Homebrew/homebrew
     * [new branch]      master     -> origin/master
    HEAD is now at 2c822ce rabbitmq: remove bottle.
    ==> Installation successful!
    ==> Next steps
    Run `brew help` to get started


THEN INSTALL TOR
================

ENTER NEW COMMAND (~) > brew install tor

    ==> Installing dependencies for tor: openssl, libevent
    ==> Installing tor dependency: openssl
    ==> Downloading https://homebrew.bintray.com/bottles/openssl-1.0.2a-1.mavericks.
    ######################################################################## 100.0%
    ==> Pouring openssl-1.0.2a-1.mavericks.bottle.1.tar.gz
    ==> Caveats
    A CA file has been bootstrapped using certificates from the system
    keychain. To add additional certificates, place .pem files in
      /usr/local/etc/openssl/certs

    and run
      /usr/local/opt/openssl/bin/c_rehash

    This formula is keg-only, which means it was not symlinked into /usr/local.

    Mac OS X already provides this software and installing another version in
    parallel can cause all kinds of trouble.

    Apple has deprecated use of OpenSSL in favor of its own TLS and crypto libraries

    Generally there are no consequences of this for you. If you build your
    own software and it requires this formula, you'll need to add to your
    build variables:

        LDFLAGS:  -L/usr/local/opt/openssl/lib
        CPPFLAGS: -I/usr/local/opt/openssl/include

    ==> Summary
    🍺  /usr/local/Cellar/openssl/1.0.2a-1: 463 files, 18M
    ==> Installing tor dependency: libevent
    ==> Downloading https://homebrew.bintray.com/bottles/libevent-2.0.22.mavericks.b
    ######################################################################## 100.0%
    ==> Pouring libevent-2.0.22.mavericks.bottle.tar.gz
    🍺  /usr/local/Cellar/libevent/2.0.22: 48 files, 1.8M
    ==> Installing tor
    ==> Downloading https://homebrew.bintray.com/bottles/tor-0.2.6.7.mavericks.bottl
    ######################################################################## 100.0%
    ==> Pouring tor-0.2.6.7.mavericks.bottle.tar.gz
    ==> Caveats
    You will find a sample `torrc` file in /usr/local/etc/tor.
    It is advisable to edit the sample `torrc` to suit
    your own security needs:
      https://www.torproject.org/docs/faq#torrc
    After editing the `torrc` you need to restart tor.

    To have launchd start tor at login:
        ln -sfv /usr/local/opt/tor/*.plist ~/Library/LaunchAgents
    Then to load tor now:
        launchctl load ~/Library/LaunchAgents/homebrew.mxcl.tor.plist
    ==> Summary
    🍺  /usr/local/Cellar/tor/0.2.6.7: 19 files, 7.0M



THEN INSTALL TORSOCKS
=====================

ENTER NEW COMMAND (~) > brew install torsocks

    ==> Downloading https://homebrew.bintray.com/bottles/torsocks-2.0.0.mavericks.bo
    ######################################################################## 100.0%
    ==> Pouring torsocks-2.0.0.mavericks.bottle.tar.gz
    🍺  /usr/local/Cellar/torsocks/2.0.0: 16 files, 284K



THEN SET TOR TO START AT LOGIN USING LAUNCHD
============================================

ENTER NEW COMMAND (~) > ln -sfv /usr/local/opt/tor/*.plist ~/Library/LaunchAgents

    /Users/brass/Library/LaunchAgents/homebrew.mxcl.tor.plist -> /usr/local/opt/tor/homebrew.mxcl.tor.plist



LOAD TOR NOW
============

ENTER NEW COMMAND (~) > launchctl load ~/Library/LaunchAgents/homebrew.mxcl.tor.plist


TEST THAT TOR IS RUNNING
========================

ENTER NEW COMMAND (~) > curl --proxy socks5h://curl:curl@127.0.0.1:9050/ https://check.torproject.org | grep Congratulations

      Congratulations. This browser is configured to use Tor.



## You can stop here. I like to tweak the torrc file to add logging and pointers to a hidden service


CREATE LOG FILE
===============

## First create subdirectories for a log file (the directory '0.3.2.9' could change in later Tor versions)

________________________________________
ENTER NEW COMMAND (~) > mkdir /usr/local/Cellar/tor/0.3.2.9/var/
_________________________________________
ENTER NEW COMMAND (~) > mkdir /usr/local/Cellar/tor/0.3.2.9/var/log/
_________________________________________
ENTER NEW COMMAND (~) > mkdir /usr/local/Cellar/tor/0.3.2.9/var/log/tor/

## Then create log file:
_________________________________________
ENTER NEW COMMAND (~) > touch /usr/local/Cellar/tor/0.3.2.9/var/log/tor/notices.log


CREATE TORRC FILE
=================

## This creates torrc from the torrc.sample that Brew installs by default

ENTER NEW COMMAND (~) > mv /usr/local/etc/tor/torrc.sample /usr/local/etc/tor/torrc



EDIT TORRC
==========

## Add logging and a pointer to a hidden service

ENTER NEW COMMAND (~) > open /usr/local/etc/tor/torrc

## Then add these lines at the top, substituting your details for MY_ONION.onion, MY_KEY and MY_CLIENT

Log notice file /usr/local/Cellar/tor/0.3.2.9/var/log/tor/notices.log

HidServAuth MY_ONION.onion MY_KEY #client: MY_CLIENT


UNLOAD AND RELOAD TOR
======================

ENTER NEW COMMAND (~) > launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.tor.plist

ENTER NEW COMMAND (~) > launchctl load ~/Library/LaunchAgents/homebrew.mxcl.tor.plist


TEST HIDDEN SERVICE
====================

## If the hidden service is working, this will take you to it

ENTER NEW COMMAND (~) > torify ssh MY_ONION.onion



# DONE #
