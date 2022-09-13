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
    server_name www.domain.com;
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

For this project, a registered domain will be used and assigned an elastic IP.

The Elastic IP is allocated on the EC2 instance as shown below:\
<img width="628" alt="elastic IP" src="https://user-images.githubusercontent.com/61512079/189940251-c13d675c-5d1d-41a5-90d9-1404029fba87.PNG">\
<img width="774" alt="Elastic IP1" src="https://user-images.githubusercontent.com/61512079/189940514-72237ec7-a0e6-43e5-89d2-b0ad0694cd33.PNG">




