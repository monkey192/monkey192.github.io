---
title: 'nginx: enable basic authentication'
date: 2022-09-07 14:01:50
tags:
    - nginx
    - basic auth
---

### What is Basic Authentication?
Basic authentication is a simple authentication schema build into the HTTP protocol.
The client sends HTTP request with the `Authorization` header that contains a base-64 encoded string from `username:password` values
  
For example: `Authorization: Basic YXBpdXNlcjpDSUNhcGkwMQ==`
  
I have assinged this task so want to take a note for enabling basic authen in nginx

### How to enable basic authen in `nginx`
Refer nginx site in [here](https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication/)

- first, install `nginx` http://nginx.org/en/download.html

- Generate a Password File
    you can use the `htpasswd` or apache2-utils (Debian, Ubuntu) or httpd-tools (RHEL/CentOS/Oracle Linux).
    ```sh
    htpasswd -c ./.htpasswd admin
    New password:
    Re-type new password:
    Adding password for user admin

    --- 
    cat .htpasswd
    admin:$apr1$MnWfNTQr$AiRKKJd.pfSgIN.fMcanJ/  <== Encrypted password
    ```
- Configuring nginx
    you can limit access to the whole website with basic authentication by specify the `auth_basic` directive at `server`

    test config: 
    ```sh
    nginx -t
    nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
    ```
    reload nginx:
    ```sh
    nginx -s quit
    nginx
    ```
- Test your setting
    ![Login request](login_request.png)
    ![Login Success](login_success.png)

### Refer
- [WWW-Authenticate](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/WWW-Authenticate)
- [Basic Auth](https://www.twilio.com/docs/glossary/what-is-basic-authentication)