## LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS
I created an Nginx WebServer which would be configured as loadbalancer

![nginx loadbalancer](https://github.com/Oolabanji/test_/assets/136812420/877bcb76-16c7-49dc-8801-30876d80acb9)

I updated /etc/hosts file for local DNS with Web Servers names (e.g. Web1 and Web2) and their local IP addresses just like it was done with the apache load balancer.


![etchosts](https://github.com/Oolabanji/test_/assets/136812420/3a55a219-7b6b-413a-a5be-c54de1a01978)

I configured Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

![loadbalancer1](https://github.com/Oolabanji/test_/assets/136812420/f96032d8-15da-4caa-ac4e-f73c663b3589)


sudo vi /etc/nginx/nginx.conf

#insert following configuration into http section

 upstream myproject {

    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;

    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

#comment out this line
#include /etc/nginx/sites-enabled/*;

I restarted Nginx and verify server status.

sudo systemctl restart nginx

sudo systemctl status nginx

## REGISTER A NEW DOMAIN NAME AND CONFIGURE SECURED CONNECTION USING SSL/TLS CERTIFICATES

I registered a new domain name(www.florintech.site) with www.namecheap.com

I assigned an Elastic IP(52.15.62.217) to Nginx LB server and associated domain name with the Elastic IP.

I opened the Amazon EC2 console at https://console.aws.amazon.com/ec2/.

In the navigation pane, choose Elastic IPs.

Select the Elastic IP address to associate and choose Actions, Associate Elastic IP address.

For Resource type, choose Instance.

For instance, choose the instance with which to associate the Elastic IP address. You can also enter text to search for a specific instance.

Choose Associate.

![elasticIP](https://github.com/Oolabanji/test_/assets/136812420/acdef508-a207-4f2c-96e4-c9cc578d086c)

I updated A record in my registrar to point to Nginx LB using Elastic IP address

![Arecord](https://github.com/Oolabanji/test_/assets/136812420/0a09d4e3-4901-4728-8e8b-7cfc9e47e8ae)

I configured Nginx to recognize the new domain name. This was done by Updating the /etc/nginx/nginx.conf file with

server_name www.florintech.site

I installed certbot and requested for an SSL/TLS certificate for the domain name. N.B: I made sure snapd is running on the server.


sudo systemctl status snapd


![snapd](https://github.com/Oolabanji/test_/assets/136812420/55041069-41ea-4b65-bf0f-e3ffab4a2fbe)


sudo snap install --classic certbot

I requested certificate for the domain name by following the instructions below.

sudo ln -s /snap/bin/certbot /usr/bin/certbot

sudo certbot --nginx


Lets Encrypt renews every 90 days and I renew your

 certificate manually by running the following command.

sudo certbot renew --dry-run

I  also created a cron job to do this same thing at a 

stipulated time.

Edited cron file


crontab -e

Added the following line to the crontab file

5 */12 * */2 *   root /usr/bin/certbot renew > /dev/null 2>&1

I saved the crontab file

![project10 completed](https://github.com/Oolabanji/test_/assets/136812420/39601b1f-35f8-4867-bf21-1300e8ad65ba)



![project10completed1](https://github.com/Oolabanji/test_/assets/136812420/0e9ac8c2-8811-44ad-b85c-0dded9ee72a2)




