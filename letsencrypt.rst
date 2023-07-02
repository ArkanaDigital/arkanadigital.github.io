##################################
|TITLE| (|DATE|)
##################################

.. |TITLE| replace:: Securing NGINX With Letsencrypt
.. |DATE| replace:: 2023/03/07

.. contents:: Table of contents
    :depth: 4

.. sectnum::



************
Introduction
************

Let’s Encrypt is a free, automated, and open certificate authority developed by the
Internet Security Research Group (ISRG) that provides free SSL certificates.
Certificates issued by Let’s Encrypt are trusted by all major browsers and valid
for 90 days from the issue date.

This tutorial explains how to install a free Let’s Encrypt SSL certificate on Ubuntu 20.04,
running Nginx as a web server. We’ll also show how to configure Nginx to use the SSL certificate
and enable HTTP/2.

Prerequisites
=============

Before you proceed, make sure that you have met the following prerequisites:

* You have a domain name pointing to your public IP. We’ll use example.com.
* You have Nginx installed on your Ubuntu server.
* Your firewall is configured to accept connections on ports 80 and 443.

Installing Certbot
==================

We’ll use certbot to obtain and renew the certificates.

Certbot is a fully-featured and easy to use tool that automates the tasks for obtaining and renewing Let’s Encrypt SSL certificates and configuring web servers to use the certificates.

The certbot package is included in the default Ubuntu repositories. To install it run the following commands:

.. code-block:: bash

    sudo apt update
    sudo apt install certbot

Generating Strong Dh (Diffie-Hellman) Group
===========================================

Diffie–Hellman key exchange (DH) is a method of securely exchanging cryptographic keys over an unsecured communication channel.

Generate a new set of 2048 bit DH parameters by typing the following command:

.. code-block:: bash

    sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048

You can also use a key length up to 4096 bits, but the generation may take more than 30 minutes, depending on the system entropy.

Obtaining a Let’s Encrypt SSL certificate
=========================================

To obtain an SSL certificate for the domain, we’re going to use the Webroot plugin that works by creating a temporary file for validating the requested domain in the ${webroot-path}/.well-known/acme-challenge directory. The Let’s Encrypt server makes HTTP requests to the temporary file to verify that the requested domain resolves to the server where certbot runs.

To make it more simple we’re going to map all HTTP requests for .well-known/acme-challenge to a single directory, /var/lib/letsencrypt.

The following commands will create the directory and make it writable for the Nginx server:

.. code-block:: bash

    sudo mkdir -p /var/lib/letsencrypt/.well-known
    sudo chgrp www-data /var/lib/letsencrypt
    sudo chmod g+s /var/lib/letsencrypt

To avoid duplicating code, we’ll create two snippets and include them in all Nginx server block files.

Open your text editor and create the first snippet, letsencrypt.conf:

.. code-block:: bash

    sudo nano /etc/nginx/snippets/letsencrypt.conf

/etc/nginx/snippets/letsencrypt.conf

.. code-block:: bash

    location ^~ /.well-known/acme-challenge/ {
      allow all;
      root /var/lib/letsencrypt/;
      default_type "text/plain";
      try_files $uri =404;
    }

Next, create the second snippet, ssl.conf, which includes the chippers recommended by Mozilla,
enables OCSP Stapling, HTTP Strict Transport Security (HSTS) and enforces few security‑focused 
HTTP headers.

.. code-block:: bash

    sudo nano /etc/nginx/snippets/ssl.conf

/etc/nginx/snippets/ssl.conf
  
.. code-block:: bash
  
    ssl_dhparam /etc/ssl/certs/dhparam.pem;
    
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:10m;
    ssl_session_tickets off;
    
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers on;
    
    ssl_stapling on;
    ssl_stapling_verify on;
    resolver 8.8.8.8 8.8.4.4 valid=300s;
    resolver_timeout 30s;
    
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Frame-Options SAMEORIGIN;
    add_header X-Content-Type-Options nosniff;

Once the snippets are created, open the domain server block file and include the letsencrypt.conf snippet as shown below:

.. code-block:: bash

    sudo nano /etc/nginx/sites-available/example.com.conf

/etc/nginx/sites-available/example.com.conf

.. code-block:: bash
  
    server {
      listen 80;
      server_name example.com www.example.com;
    
      include snippets/letsencrypt.conf;
    }

To enable the new server block, create a symbolic link from the file to the sites-enabled directory:

.. code-block:: bash

    sudo ln -s /etc/nginx/sites-available/example.com.conf /etc/nginx/sites-enabled/

Restart the Nginx service for the changes to take effect:

.. code-block:: bash

    sudo systemctl restart nginx

You can now run Certbot with the webroot plugin and obtain the SSL certificate files by issuing:

.. code-block:: bash

    sudo certbot certonly --agree-tos --email admin@example.com --webroot -w /var/lib/letsencrypt/ -d example.com -d www.example.com

If the SSL certificate is successfully obtained, certbot will print the following message:

.. code-block:: bash

    IMPORTANT NOTES:
     - Congratulations! Your certificate and chain have been saved at:
       /etc/letsencrypt/live/example.com/fullchain.pem
       Your key file has been saved at:
       /etc/letsencrypt/live/example.com/privkey.pem
       Your cert will expire on 2020-10-18. To obtain a new or tweaked
       version of this certificate in the future, simply run certbot
       again. To non-interactively renew *all* of your certificates, run
       "certbot renew"
     - Your account credentials have been saved in your Certbot
       configuration directory at /etc/letsencrypt. You should make a
       secure backup of this folder now. This configuration directory will
       also contain certificates and private keys obtained by Certbot so
       making regular backups of this folder is ideal.
     - If you like Certbot, please consider supporting our work by:
    
       Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
       Donating to EFF:                    https://eff.org/donate-le

Now that you have the certificate files, you can edit your domain server block as follows:

.. code-block:: bash

    sudo nano /etc/nginx/sites-available/example.com.conf

/etc/nginx/sites-available/example.com.conf

.. code-block:: bash

    server {
        listen 80;
        server_name www.example.com example.com;
    
        include snippets/letsencrypt.conf;
        return 301 https://$host$request_uri;
    }
    
    server {
        listen 443 ssl http2;
        server_name www.example.com;
    
        ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
        ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;
        include snippets/ssl.conf;
        include snippets/letsencrypt.conf;
    
        return 301 https://example.com$request_uri;
    }
    
    server {
        listen 443 ssl http2;
        server_name example.com;
    
        ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
        ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;
        include snippets/ssl.conf;
        include snippets/letsencrypt.conf;
    
        # . . . other code
    }

With the configuration above we are forcing HTTPS and redirecting from www to non www version.

Reload the Nginx service for changes to take effect:

.. code-block:: bash

    sudo systemctl reload nginx

To verify that the SSL certificate is successfully installed, open your website using https://, and you’ll notice a green lock icon.

Auto-renewing Let’s Encrypt SSL certificate
===========================================

Let’s Encrypt’s certificates are valid for 90 days. To automatically renew the certificates before they
expire, the certbot package creates a cronjob and a systemd timer. The timer will automatically
renew the certificates 30 days before its expiration.

When the certificate is renewed, the nginx service needs to be reloaded. Open the
/etc/letsencrypt/cli.ini and add the following line:

.. code-block:: bash

    sudo nano /etc/letsencrypt/cli.ini

/etc/letsencrypt/cli.ini

.. code-block:: bash

    deploy-hook = systemctl reload nginx

To test the renewal process, run the certbot --dry-run command:

.. code-block:: bash

    sudo certbot renew --dry-run

If there are no errors, it means that the renewal process was successful.
