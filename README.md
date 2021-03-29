# Adding New SSL & Domain in Bitnami Multisite WordPress

## Use bncert Command

First run this command for any domain for which you need SSL:

`sudo /opt/bitnami/bncert-tool`

Only enter 1 domain name every time you run the command. This will generate a new SSL for that domain and store in appropriate directory.

## Add new virtual host in httpd-vhosts.conf file

Add following block of code in your httpd-vhosts.conf file:

```apache
<VirtualHost *:80>
    ServerName smarttsys.com
    ServerAlias *.smarttsys.com
    DocumentRoot "/opt/bitnami/apps/wordpress/htdocs"
    Include "/opt/bitnami/apps/wordpress/conf/httpd-app.conf"
</VirtualHost>
<VirtualHost *:443>
    ServerName smarttsys.com
    ServerAlias *.smarttsys.com
    DocumentRoot "/opt/bitnami/apps/wordpress/htdocs"
    SSLEngine on
    SSLCertificateFile "/opt/bitnami/apache2/conf/smarttsys.com.crt"
    SSLCertificateKeyFile "/opt/bitnami/apache2/conf/smarttsys.com.key"
    Include "/opt/bitnami/apps/wordpress/conf/httpd-app.conf"
</VirtualHost>
```

Replace smarttsys.com with your domain name. If you want separate SSL for subdomains then generate separate SSL using the bncert tool and duplicate code block for that sub-domain but ensure no code block contains:

`*.smarttsys.com`

It would become:

`www.smarttsys.co`

Example:

Primary domain code block would become:


```apache
<VirtualHost *:80>
    ServerName smarttsys.com
    ServerAlias www.smarttsys.com
    DocumentRoot "/opt/bitnami/apps/wordpress/htdocs"
    Include "/opt/bitnami/apps/wordpress/conf/httpd-app.conf"
</VirtualHost>
<VirtualHost *:443>
    ServerName smarttsys.com
    ServerAlias www.smarttsys.com
    DocumentRoot "/opt/bitnami/apps/wordpress/htdocs"
    SSLEngine on
    SSLCertificateFile "/opt/bitnami/apache2/conf/smarttsys.com.crt"
    SSLCertificateKeyFile "/opt/bitnami/apache2/conf/smarttsys.com.key"
    Include "/opt/bitnami/apps/wordpress/conf/httpd-app.conf"
</VirtualHost>
```

Sub-domain would be:

```apache
<VirtualHost *:80>
    ServerName test.smarttsys.com
    ServerAlias www.test.smarttsys.com
    DocumentRoot "/opt/bitnami/apps/wordpress/htdocs"
    Include "/opt/bitnami/apps/wordpress/conf/httpd-app.conf"
</VirtualHost>
<VirtualHost *:443>
    ServerName test.smarttsys.com
    ServerAlias www.test.smarttsys.com
    DocumentRoot "/opt/bitnami/apps/wordpress/htdocs"
    SSLEngine on
    SSLCertificateFile "/opt/bitnami/apache2/conf/test.smarttsys.com.crt"
    SSLCertificateKeyFile "/opt/bitnami/apache2/conf/test.smarttsys.com.key"
    Include "/opt/bitnami/apps/wordpress/conf/httpd-app.conf"
</VirtualHost>
```

SSLCertificateFile & SSLCertificateKeyFile directory will contain new SSL with a different file name.

## Restart apache

Restart apache by running command:

`sudo /opt/bitnami/ctlscript.sh restart`

Or 

`sudo /opt/bitnami/ctlscript.sh restart apache`

Apache would restart now in case there are no syntax errors

## Replace the crontab file


