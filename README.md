# Using-Google-Cloud

Create and configure a web server on Google Cloud Platform

## Instructions
When creating a Virtual Machine for the very first time within Google Cloud Compute Engine API ensure to enable the API

Once enabled we can create your first VM instance. 

Here you can choose a name, select the region and zone as well as configure your machine depending on the requirements.

Depending on preference you can select which Boot Disk you wish your VM to load for this example CentOS 8

You can allow the Firewall to be turned on for HTTP & HTTPS traffic, once you are satisfied create the Virtual Machine





## Part 1



```bash
#Once connected to the VM its good practice to update the repository: 
sudo dnf update -y

```

```bash
#Use the following command to download apache on centOS 8: 
sudo dnf install httpd
```

```bash
#Once installed we need to enable and start httpd using the following command:
sudo systemctl enable httpd
sudo systemctl start httpd

```

```bash
#Check httpd is running: 

systemctl status httpd 

#This command will also flag any errors within httpd (if any)
```

```bash
#Navigate to: 
cd /var/www/html 

```

```bash
#As there isn't an index.html file inside the HTML directory, we can create one and input our header for our web page.
```

```bash
#To create the index file use the following command: 
sudo vi index.html 
```

```bash
#Once in the file, we can press i to insert and add our header:
<h2>Welcome to Appsbroker, Team Purple for the win!!!<h2>
```

```bash
#To save the file press Esc and :wq (write & quit)
```
## Part 2
```bash
#Head over to https://www.duckdns.org/domains and create a free domain
```

```bash
#Update your current ip address with the one from your Virtual Machine 
#Now traffic will be directed to your domain name
```
## Part 3
```bash
#If you have a firewalld, firewall set up, open up the http and https ports:
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload

```

```bash
#We first need to install mod_ssl, an Apache module that provides support for SSL encryption.
#Install mod_ssl with the dnf command:

sudo dnf install mod_ssl
```

```bash
#IMPORTANT Because of a packaging bug, we need to restart Apache once to properly generate the default SSL certificate and key, 
#otherwise we’ll get an error reading '/etc/pki/tls/certs/localhost.crt' does not exist or is empty.

sudo systemctl restart httpd
```

```bash
#Now that we have our self-signed certificate and key available, we need to update our Apache configuration to use them. On CentOS, 
#you can place new Apache configuration files (they must end in .conf) into /etc/httpd/conf.d and they will be loaded the next time 
#the Apache process is reloaded or restarted.

#For this tutorial we will create a new minimal configuration file. If you already have an Apache <Virtualhost> set up and just 
#need to add SSL to it, you will likely need to copy over the configuration lines that start with SSL, and switch the VirtualHost 
#port from 80 to 443. We will take care of port 80 in the next step.

#Open a new file in the /etc/httpd/conf.d directory:

sudo vi /etc/httpd/conf.d/your_domain_or_ip.conf (appsbroker.duckdns.org)

```

```bash

#Paste in the following minimal VirtualHost configuration:

<VirtualHost *:443>
    ServerName your_domain_or_ip (appsbroker.duckdns.org)
    DocumentRoot /var/www/html
    SSLEngine on
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>

<VirtualHost *:80>
    ServerName your_domain_or_ip (appsbroker.duckdns.org)
    Redirect / https://your_domain_or_ip/ (appsbroker.duckdns.org)
</VirtualHost>

```

```bash
#As changes have been made we need to restart Apache:

sudo systemctl restart httpd
```
## Part 4
```bash
#Installing the Certbot Let’s Encrypt Client
#To use Let’s Encrypt to obtain an SSL certificate, you first need to install Certbot and mod_ssl, an Apache module that provides 
#support for SSLv3 encryption.

#The certbot package is not available through the package manager by default. You will need to enable the EPEL repository to install Certbot.

#To add the CentOS 8 EPEL repository, run the following command:

sudo dnf install epel-release
```

```bash
#Now that you have access to the repository, install all of the required packages:

sudo dnf install certbot python3-certbot-apache
```

```bash
#Now that Certbot is installed, you can use it to request an SSL certificate for your domain.

#Using the certbot Let’s Encrypt client to generate the SSL Certificate for Apache automates many of the steps in the process. 
#The client will automatically obtain and install a new SSL certificate that is valid for the domains you provide as parameters.

#To execute the interactive installation and obtain a certificate that covers only a single domain, run the certbot command with:

sudo certbot --apache -d example.com (appsbroker.duckdns.org)
```

```bash
#Testing the Certificate and SSL Configuration
#At this point, you can ensure that Certbot created your SSL certificate correctly by using the SSL Server Test from the cloud security company Qualys.

#Open the following link in your preferred web browser, replacing example.com with your domain:

https://www.ssllabs.com/ssltest/analyze.html?d=example.com
```

https://appsbroker.duckdns.org

## Acknowledgements

 - [How To Create a Self-Signed SSL Certificate for Apache on CentOS 8](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-on-centos-8)
 - [How To Secure Apache with Let's Encrypt on CentOS 8](https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-centos-8)


