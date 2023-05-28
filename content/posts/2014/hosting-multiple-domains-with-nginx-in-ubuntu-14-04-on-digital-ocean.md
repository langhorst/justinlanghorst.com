+++
title = "Hosting multiple domains with Nginx in Ubuntu 14.04 on Digital Ocean"
description = "How to set-up a VPS on Digital Ocean to host multiple sites with the Nginx web-server on top of the Ubuntu 14.04 operating system."
date = 2014-07-12T10:04:15Z
tags = ["development","ubuntu","nginx","vps","fail2ban","ssh","web server"]
+++

So I was having problem with my previous server. Now that I've rebuilt everything I've realized that the problem I was having could have been easily fixed with my previous install. But, it doesn't matter; it was time to upgrade from Ubuntu 12.04 to 14.04 anyway.

The goal of the server is simple: host two domains using Nginx. The problem I was having with the 12.04 install was that I could never get the second domain to serve up the right files; it would always serve up the first domain. This turned out to be a problem with the Nginx configuration. I thought this was the case at the time, but since the install wasn't doing anything else anyway, I thought it was a good time to rebuild and start from scratch.

<!--more-->

There are four articles of importance here:

1. [Initial server setup with Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04)
2. [How to install Nginx on Ubuntu 14.04 LTS](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-14-04-lts)
3. [How to set up Nginx server blocks (virtual hosts) on Ubuntu 14.04 LTS](https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-server-blocks-virtual-hosts-on-ubuntu-14-04-lts)
4. [How to install and use Fail2Ban on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-fail2ban-on-ubuntu-14-04)

There is also a fifth article that may be useful to you: [How to set up a host name with DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-host-name-with-digitalocean).

The following is a quick summary of commands and configuration for the four articles listed above.

#### Initial server setup with Ubuntu 14.04

1. Login: `ssh root@[server_ip_address]`
2. Change the root password: `passwd`
3. Add a new user: `adduser [username]`
4. Give root priveleges to new user: `visudo`
5. BACK OUT IMMEDIATELY with CTRL-X and `export EDITOR=vim` ;-)
6. Give root privelges to new user: `visudo`

    `[username]  ALL=(ALL:ALL) ALL`

7. Alter SSH configuration: `vim /etc/ssh/sshd_config`
	1. Update port: `port [port_number]`
	2. Refuse Root Login: `PermitRootLogin no`
	3. Allow only certain users (careful!): `AllowUsers [username]`
8. Restart SSH: `service ssh restart`
9. Test SSH config from new terminal
	1. `ssh -p [port_number] [username]@[server_ip_address]`
	2. `sudo vim`
10. Exit the root terminal: `exit`

#### How to install Nginx on Ubuntu 14.04 LTS

1. Install Nginx:
	1. `sudo apt-get update`
	2. `sudo apt-get install nginx`
2. Check the installation: `curl http://[server_ip_address]` or `curl http://[domain_uri]`

#### How to set up Nginx server blocks (virtual hosts) on Ubuntu 14.04 LTS

1. Create a directory for each domain's files:
	1. `sudo mkdir -p /var/www/[domain1]/html`
	2. `sudo mkdir -p /var/www/[domain2]/html`
2. Change ownership to each directory:
	1. `sudo chown -R $USER:$USER /var/www/[domain1]/html`
	2. `sudo chown -R $USER:$USER /var/www/[domain2]/html`
3. Make sure the permissions of the web roots are correct: `sudo chmod -R 755 /var/www`
4. Create sample pages for each site:
	1. `vim /var/www/[domain1]/html/index.html` -> HTML
	2. `cp /var/www/[domain1]/html/index.html /var/www/[domain2]/html/index.html`
	3. `vim /var/www/[domain2]/html/index.html` -> HTML
5. Create server blocks for each domain:
	1. `sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/[domain]`
	2. `sudo vim /etc/nginx/sites-available/[domain1]`

	```
		server {
			listen 80;
			listen [::]:80;

			server_name [domain1] www.[domain1];

			root /var/www/[domain1]/html;
			index index.html index.htm;

			location / {
				try_files $uri $uri/ =404;
			}
		}
	```

	3. `sudo vim /etc/nginx/sites-available/[domain1] /etc/nginx/sites-available/[domain2]`
	4. Adjust the second domain's configuration to match the domain name and appropriate directory locations.
6. Enable the server blocks and restart Nginx
	1. `sudo ln -s /etc/nginx/sites-available/[domain1] /etc/nginx/sites-enabled/`
	2. `sudo ln -s /etc/nginx/sites-available/[domain2] /etc/nginx/sites-enabled/`
	3. `sudo vim /etc/nginx/nginx.conf` and make the following change: `server_names_hash_bucket_size: 64;`
	4. `sudo service nginx restart`

#### How to install and use Fail2Ban on Ubuntu 14.04

1. `sudo apt-get update`
2. `sudo apt-get install fail2ban`
