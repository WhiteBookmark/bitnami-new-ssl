# Adding New SSL & Domain in Bitnami Multisite WordPress

## 1- Use bncert Command

First run this command for any domain for which you need SSL:

`sudo /opt/bitnami/bncert-tool`

Only enter 1 domain name every time you run the command. This will generate a new SSL for that domain and store in appropriate directory.

## 2- Add new virtual host in httpd-vhosts.conf file

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

## 3- Restart apache

Restart apache by running command:

`sudo /opt/bitnami/ctlscript.sh restart`

Or 

`sudo /opt/bitnami/ctlscript.sh restart apache`

Apache would restart now in case there are no syntax errors

## 4- Create a crontab file and save on your PC

Every time we use bncert-tool, it will remove all crontab jobs and just add 1 for the latest SSL it generated.

Generate a crontab file on your PC as a backup and add new domain name whenver you generate a new SSL:

```
 0 0 * * * sudo /opt/bitnami/letsencrypt/lego --path /opt/bitnami/letsencrypt --email="pvelinov@gmail.com" --http --http-timeout 30 --http.webroot /opt/bitnami/apps/letsencrypt --domains=lionscredit.com renew && sudo /opt/bitnami/apache2/bin/httpd -f /opt/bitnami/apache2/conf/httpd.conf -k graceful # bncert-autorenew

 0 0 * * * sudo /opt/bitnami/letsencrypt/lego --path /opt/bitnami/letsencrypt --email="pvelinov@gmail.com" --http --http-timeout 30 --http.webroot /opt/bitnami/apps/letsencrypt --domains=metamorfeus.com renew && sudo /opt/bitnami/apache2/bin/httpd -f /opt/bitnami/apache2/conf/httpd.conf -k graceful # bncert-autorenew

 0 0 * * * sudo /opt/bitnami/letsencrypt/lego --path /opt/bitnami/letsencrypt --email="pvelinov@gmail.com" --http --http-timeout 30 --http.webroot /opt/bitnami/apps/letsencrypt --domains=myartgallery.ca renew && sudo /opt/bitnami/apache2/bin/httpd -f /opt/bitnami/apache2/conf/httpd.conf -k graceful # bncert-autorenew

 0 0 * * * sudo /opt/bitnami/letsencrypt/lego --path /opt/bitnami/letsencrypt --email="pvelinov@gmail.com" --http --http-timeout 30 --http.webroot /opt/bitnami/apps/letsencrypt --domains=test.smarttsys.com renew && sudo /opt/bitnami/apache2/bin/httpd -f /opt/bitnami/apache2/conf/httpd.conf -k graceful # bncert-autorenew

 0 0 * * * sudo /opt/bitnami/letsencrypt/lego --path /opt/bitnami/letsencrypt --email="pvelinov@gmail.com" --http --http-timeout 30 --http.webroot /opt/bitnami/apps/letsencrypt --domains=testaz.advancedharmony.org renew && sudo /opt/bitnami/apache2/bin/httpd -f /opt/bitnami/apache2/conf/httpd.conf -k graceful # bncert-autorenew

 0 0 * * * sudo /opt/bitnami/letsencrypt/lego --path /opt/bitnami/letsencrypt --email="pvelinov@gmail.com" --http --http-timeout 30 --http.webroot /opt/bitnami/apps/letsencrypt --domains=test.testaz.advancedharmony.org renew && sudo /opt/bitnami/apache2/bin/httpd -f /opt/bitnami/apache2/conf/httpd.conf -k graceful # bncert-autorenew

 0 0 * * * sudo /opt/bitnami/letsencrypt/lego --path /opt/bitnami/letsencrypt --email="pvelinov@gmail.com" --http --http-timeout 30 --http.webroot /opt/bitnami/apps/letsencrypt --domains=test.otinyhouse.com renew && sudo /opt/bitnami/apache2/bin/httpd -f /opt/bitnami/apache2/conf/httpd.conf -k graceful # bncert-autorenew

 0 0 * * * sudo /opt/bitnami/letsencrypt/lego --path /opt/bitnami/letsencrypt --email="pvelinov@gmail.com" --http --http-timeout 30 --http.webroot /opt/bitnami/apps/letsencrypt --domains=otinyhouse.com renew && sudo /opt/bitnami/apache2/bin/httpd -f /opt/bitnami/apache2/conf/httpd.conf -k graceful # bncert-autorenew
```

## 5- Replace the crontab file

First find current crontab directory and file name by running command:

`sudo crontab -e`

Open in nano editor or your preferred and see what is the file name and directory

Example: /tmp/crontab.uCOkax/crontab

Go to that directory and replace the crontab file or copy paste from your file to the server's crontab file.

## 6- Restart cron

If you copy paste using `crontab -e` then you don't need to restart crontab. If you replace or edit the file any other way then run command to restart crontab:

`sudo service cron reload`

or wherever cron is located:

`/etc/init.d/cron reload`
