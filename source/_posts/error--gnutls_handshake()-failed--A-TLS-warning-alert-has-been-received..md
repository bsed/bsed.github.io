---
title: "error: gnutls_handshake() failed: A TLS warning alert has been received."
categories: [ "Linux" ]
tags: [ "openssl","git","compile" ]
draft: false
slug: "gnutlshandshake-failed-a-tls-warning-alert-has-been-received-error"
date: "2015-11-11 20:50:00"
---

Symptoms

This issue can manifest in 2 ways:

## First way:

Performing around a hundred of subsequent [cloning](http://atlassian.com/git/tutorial/git-basics#!clone) or [pushing](https://www.atlassian.com/git/tutorial/remote-repositories#!push) operations directly to Stash results in the following error:

    error: gnutls_handshake() failed: A TLS warning alert has been received.

 

## Second way:

The error message bellow has been perceived while frequently running git fetch from clients when Stash using HTTPS. 


<!--more-->


error: The requested URL returned error: 400 while accessing

If you're running into the error below, [setting Git on your client](https://confluence.atlassian.com/display/STASH/Stash+debug+logging#Stashdebuglogging-DebugloggingforGitoperationsontheclient) to DEBUG should uncover the same message as seen in the First way:


    error: gnutls_handshake() failed: A TLS warning alert has been received.

## Cause

We've seen this issue being caused by either one of the causes below:

### 1) Stash Server:

This happens if your Stash server is running on a Java 7 that contains the a bug in the TLS/SSL stack.

### 2) Git Client 

The client performing the git fetch operation has run into a bug found libcurl3-gnutls introduced on the 7.21.6-3 release when using HTTPS.

 
## Resolution

You can either:

### 1) Stash Server

    As a workaround for this Java bug, you can attach the following parameter to your Tomcat HTTPS connector. [The solution has been described here](http://mail-archives.apache.org/mod_mbox/tomcat-users/201302.mbox/%3C7710CE26658AD84CB8E3CE40308847F62BF5CD41@SCINTEXC01.corpnet.intermec.com%3E):

      ciphers="TLS_RSA_WITH_AES_256_CBC_SHA256, TLS_ECDH_ECDSA_WITH_AES_256_CBC_SHA384, TLS_ECDH_RSA_WITH_AES_256_CBC_SHA384,
        TLS_RSA_WITH_AES_256_CBC_SHA, TLS_ECDH_ECDSA_WITH_AES_256_CBC_SHA, TLS_ECDH_RSA_WITH_AES_256_CBC_SHA,
        TLS_RSA_WITH_AES_128_CBC_SHA256, TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA256, TLS_ECDH_RSA_WITH_AES_128_CBC_SHA256,
        TLS_RSA_WITH_AES_128_CBC_SHA, TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA, TLS_ECDH_RSA_WITH_AES_128_CBC_SHA,
        SSL_RSA_WITH_RC4_128_SHA, TLS_ECDH_ECDSA_WITH_RC4_128_SHA, TLS_ECDH_RSA_WITH_RC4_128_SHA,
        SSL_RSA_WITH_3DES_EDE_CBC_SHA, TLS_ECDH_ECDSA_WITH_3DES_EDE_CBC_SHA, TLS_ECDH_RSA_WITH_3DES_EDE_CBC_SHA,
        SSL_RSA_WITH_RC4_128_MD5, TLS_EMPTY_RENEGOTIATION_INFO_SCSV2"

    Or upgrade the JVM on the Stash server to the version [7u60](http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=8021840) or above. 
        We have an assertive confirmation from one of our customers that upgrading to from to 7u45 to 8u45 fixed the issue.

### 2) Git Client

As reported by a user on [the previously linked bug](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=559371), downgrading libcurl3-gnutls from 7.21.6-3 to 7.21.0-1 is an easy way to solve this issue. See the user's comment:

    > I had the same problem, here.
    > Downgrading libcurl3-gnutls from 7.21.6-3 to 7.21.0-1 fixed it for me.

Alternatively, if you are trying these operations from a client running Ubuntu, you can find on their forum a workaround to compile your git on the client with openssl, moving off from libcurl3-gnutls.

Quoting the solution from the Ubuntu forum above:

    sudo apt-get install build-essential fakeroot dpkg-dev
    mkdir ~/git-openssl
    cd ~/git-openssl
    sudo apt-get source git
    sudo apt-get build-dep git
    sudo apt-get install libcurl4-openssl-dev
    sudo dpkg-source -x git_1.7.9.5-1.dsc
    cd git_1.7.9.5

Then, edit `debian/control` file (run the command: `gksu gedit debian/control`) and replace all instances of libcurl4-gnutls-dev with libcurl4-openssl-dev.

Then build the package (if it's failing on test, you can remove the line `TEST=test` from the file `debian/rules`):

    sudo dpkg-buildpackage -rfakeroot -b

Install new package:

    i386: sudo dpkg -i ../git_1.7.9.5-1_i386.deb
    
    x86_64: sudo dpkg -i ../git_1.7.9.5-1_amd64.deb

link:

 - [https://confluence.atlassian.com/pages/viewpage.action?pageId=419005548](https://confluence.atlassian.com/pages/viewpage.action?pageId=419005548)
 - [http://askubuntu.com/questions/186847/error-gnutls-handshake-failed-when-connecting-to-https-servers](http://askubuntu.com/questions/186847/error-gnutls-handshake-failed-when-connecting-to-https-servers)