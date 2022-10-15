# nginx-lb
Load Balancer with Nginx and SSL/TLS

The existing instance used for Apache Load balancer will be used for this project. So we connect to the EC2 instance running Ubuntu already.
Then we uninstall the Apache2 running on it as shown below:
```bash
sudo apt purge apache2
```
The Apache was uninstalled successfully:\
<img width="741" alt="Apache-Uninstall" src="https://user-images.githubusercontent.com/61512079/189927444-15d999fa-cf1a-4ca1-a13a-edc7020ac568.PNG">

Also we ensure port 80 and 443 is opened for inbound connection to the EC2 instance.
Next we add the IP address of the Webservers in the LB host file, 172.31.33.120  & 172.31.45.154 as web1 & web3 respectively as shown below:\
<img width="254" alt="host file" src="https://user-images.githubusercontent.com/61512079/189929822-7ebff3e1-2f05-4e17-a606-96d942a3f22d.PNG">

Next the instance is updated and nginx is installed:
```bash
sudo apt update
sudo apt install nginx
```
After nginx is successfully installed, we edit the configuration file and set the load balancer to use web1 and web3 configured earlier in the host file:
```bash
sudo vi /etc/nginx/nginx.conf
```

```vim
#insert following configuration into http section

 upstream myproject {
    server web1 weight=5;
    server web3 weight=5;
  }

server {
    listen 80;
    server_name www.mydevopstest.co;
    location / {
      proxy_pass http://myproject;
    }
  }

#comment out this line
#       include /etc/nginx/sites-enabled/*;
```
Next nginx is restarted and confirmed up:
```bash
sudo systemctl restart nginx
sudo systemctl status nginx
```
nginx status is confirmed here:\
<img width="756" alt="nginx status" src="https://user-images.githubusercontent.com/61512079/189933624-3b639d5a-6258-4cc0-85e6-e381ff20022f.PNG">

For this project, a registered domain will be used and assigned an elastic IP.  The domain registered is mydevopstest.co and was associated with the Elastic IP on Godaddy DNS as shown below:\

<img width="842" alt="mydevopstest domain" src="https://user-images.githubusercontent.com/61512079/196005386-2a28f193-b5d2-46ac-87f1-5d6ba245d8e7.PNG">

The Elastic IP is allocated on the EC2 instance as shown below:\
<img width="628" alt="elastic IP" src="https://user-images.githubusercontent.com/61512079/189940251-c13d675c-5d1d-41a5-90d9-1404029fba87.PNG">\
<img width="774" alt="Elastic IP1" src="https://user-images.githubusercontent.com/61512079/189940514-72237ec7-a0e6-43e5-89d2-b0ad0694cd33.PNG">

Next is the installation of certbot and then we request the SSL/TLS certificate:

```bash
sudo systemctl status snapd
```
The above command confirmed snap service is running.

<img width="899" alt="snap status" src="https://user-images.githubusercontent.com/61512079/196005446-30b6c43d-4f2c-4eb8-8a54-e64cc9a1ef03.PNG">

 Then install certbot with command below:
 ```bash
 sudo snap install --classic certbot
 ```
   <img width="391" alt="certbot" src="https://user-images.githubusercontent.com/61512079/196005515-03df0f87-75e4-44e3-8c6d-8380e0b27d6f.PNG">

```bash
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```

The certificate is now installed on the site and tested as shown in browser output below:\
<img width="483" alt="ngsite-secure" src="https://user-images.githubusercontent.com/61512079/196007015-d5a6b507-b90b-420e-95d4-53a9115835b3.PNG">

Further step executed is to set up periodical renewal of your SSL/TLS certificate.  The renewal can first be tested in dry run mode using the command below:
```bash
sudo certbot renew --dry-run
```
The renewal is successfully tested as shown:\
<img width="495" alt="renewal-test" src="https://user-images.githubusercontent.com/61512079/196007177-51b6d4ab-443a-48b1-a227-c6446a23ecbb.PNG">

The best pracice is to have a scheduled job that to run renew command periodically. So configured a cronjob to run the command twice a day with command below:
```bash
crontab -e
```
The crontab is edited as follow to run the command twice a day:
```vim
* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1
```

That is all with the implementation of Nginx Load Balancing Web Solution using secured HTTPS connection with periodically updated SSL/TLS certificates.

