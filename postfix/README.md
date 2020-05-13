# Sample Postfix configuration

## Overview

These are example configuration files for a reasonably secure installation of Postfix 2.6.6. You may have a newer
version, but the configuration options are generally the same. Make sure to back up your existing configuration 
before making any changes.

These settings are sufficient to implement [MTA-STS](https://www.hardenize.com/blog/mta-sts) in "enforce" mode if 
desired, and will qualify for inclusion on the [STARTTLS Everywhere](https://starttls-everywhere.org/) registry.

For all of these example files, assume:

- This mail server is running CentOS; filesystem paths may vary on other operating systems.

- The example mail server uses the hostname `mail.domain.example`.

- The primary user has a real account on the system, username `parseword`, which corresponds to the email 
address `parseword@domain.example`. Most other aliases, catch-alls, etc. are ultimately redirected to that mailbox. 
This aligns with a typical single-user (or very few users) setup.

- The mail server only operates on IPv4

- A valid TLS certificate for `mail.domain.example` has been obtained from Let's Encrypt 

## File descriptions

### header_checks

This file lets you define actions to perform against incoming messages based on headers in those messages. The 
example file demonstrates how to reject mail you don't want. You can disable using this file by commenting it out
in `main.cf`.

### ip_cidr_checks

This file lets you define actions to perform against incoming messages based on the IP address of the remote server. 
The example file demonstrates how to blacklist and whitelist IPs and CIDRs. It includes CIDRs for several large 
mail providers and "legitimate" bulk senders/MSPs.

### main.cf

This file contains the main configuration directives for Postfix.

Some notes here:

- The example configuration only supports TLSv1.2 or newer. Older TLS versions have been intentionally disabled. Also, 
a number of weaker ciphers have been intentionally disabled. All modern mail clients, as well as legitimate remote 
mail servers, will be able to connect just fine.

- You need to figure out where your operating system keeps its root CA file, and point 
the `smtp_tls_CAfile` and `smtpd_tls_CAfile` options to it. The example file contains the path for a CentOS system.

- You need to obtain your own TLS certificate from Let's Encrypt and set the correct paths for 
the `smtpd_tls_cert_file` and `smtpd_tls_key_file` options.

- You need to generate a pair of Diffie-Hellman parameter files. The necessary `openssl` commands are in the comments
toward the bottom of the file.

- It's assumed that OpenDKIM and OpenDMARC are installed and running on the default ports. If not, adjust 
the `smtpd_milters` option.

### main.cf.non-default-settings-only.txt

This is a copy of `main.cf` (above), with all of the comments and default options stripped out. Basically, this is an 
alphabetical list of all the settings I changed from a default Postfix installation.

### master.cf

This file defines the services, sockets, milters, etc. that make up the local Postfix surface. In the example, 
SpamAssassin is enabled for incoming mail and disabled for outgoing mail. If you don't have SpamAssassin installed, 
comment those parts out. Also defined here is a custom "cleanup" service for outbound messages 
(see `outgoing_header_checks` below).

### outgoing_header_checks

This file contains directives to strip a few specific headers from all outgoing messages. Among other things, this 
removes the remote client's IP address for privacy reasons. Outgoing messages will look like they originated on the mail 
server itself, not from your remote location.

### recipient_whitelist

This file lets you define actions to perform against incoming messages based on the recipient email address. All I 
use this for is to whitelist role accounts (abuse@, postmaster@ ...) so mail to these addresses is never bounced.

### virtual

This file defines virtual aliases.