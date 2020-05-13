# Sample Dovecot configuration

## Overview

These are example configuration files for a Dovecot 2.0.9 IMAP server. Newer versions of Dovecot, especially the
2.2.6 and 2.3 branches, introduce more modern security options. Some of the directives in the `10-ssl.conf` file 
are unlikely to work on Dovecot 2.3. Make sure to back up your existing configuration before making any changes.

For all of these example files, assume:

- This mail server is running CentOS; filesystem paths may vary on other operating systems.

- The example mail server uses the hostname `mail.domain.example`.

- A valid TLS certificate for `mail.domain.example` has been obtained from Let's Encrypt

## File descriptions

### 10-ssl.conf

This file is from `/etc/dovecot/conf.d` and is the only file in that directory I've modified from the default. 
It alters the configuration to point at the Let's Encrypt certificate instead of a self-signed one, and 
disables some older/weaker TLS protocols and ciphers.

### dovecot.conf

This is the main Dovecot configuration file.