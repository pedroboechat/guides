# How to configure a NGINX WebServer

## Installing:

```bash
sudo apt install nginx
```

After installing, go to `/etc/nginx/sites-available/`. There you'll find a file named `default`. That's the default configuration file for NGINX.

Remember to check, in case you have a firewall enabled, if the ports 80 and 443 are allowed.



## Disable/remove configuration files:

To disable a configuration, just delete it's symbolic link from `/etc/nginx/sites-enabled/`.

```bash
sudo rm /etc/nginx/sites-enabled/default
```

If you want to permanently remove it, also delete it from `/etc/nginx/sites-available/`.

```bash
sudo rm /etc/nginx/sites-available/default
```

To apply the changes, restart NGINX service:

```bash
sudo systemctl restart nginx
```



## Create configuration files:

To create a new configuration file, do it in `/etc/nginx/sites-available/` and to enable it by creating a symbolic link to `/etc/nginx/sites-enabled/example.com`.

```bash
sudo nano /etc/nginx/sites-available/example.com
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/example.com
```

In the configuration you may configure a basic route to a directory:

```bash
server {
	listen 80; # Port the server is listening
	server_name example.com; # The server name, that should be equal to the url that will access it

    location / { # It creates the rules of directory to the location / of the server
        root /var/www/html/; # Defines the root directory for the location
        index index.html index.htm index.php; # Defines the index files the server will search for
    }
}
```

Or a proxy to another port:

```bash
server {
	listen 80; # Port the server is listening
	server_name example.com; # The server name, that should be equal to the url that will access it

	location / { # It creates the rules of directory to the location / of the server
		proxy_pass http://127.0.0.1:8050; # Defines the proxy address
	}
}
```

To apply the changes, restart NGINX service:

```bash
sudo systemctl restart nginx
```



## Configure a route for the server ip:

If you have an public ip of 1.2.3.4 and want to see something if you access the server via it, you can create a new server as:

```bash
server {
	listen 80 default_server; # Port the server is listening and it's definition as default server
	server_name _; # Kind of "no server name"

	location / {
		root /var/www/html/;
		index index.html index.htm index.php;
    }
}
```



## Configure subdomains:

Let's say we have the `example.com` domain and want to configure the `another.example.com` subdomain. We can do it by creating another server as:

```bash
### example.com ###
server {
	listen 80;
	server_name example.com;

    location / {
        root /var/www/html/;
        index index.html index.htm index.php;
    }
}

### another.example.com ###
server {
	listen 80;
	server_name another.example.com;

    location / {
        root /var/www/html/another;
        index index.html index.htm index.php;
    }
}
```



## Configure a redirection:

If you want to create a server that redirects to another one, you can as:

```bash
### example.com ###
server {
	listen 80;
	server_name example.com;

    location / {
        root /var/www/html/;
        index index.html index.htm index.php;
    }
}

### www.example.com ###
server {
	listen 80;
	server_name www.example.com;

    return 301 http://example.com$request_uri;
}
```



## Configure SSL with Certbot and Let's Encrypt:

First install Certbot and the NGINX plugin:

```bash
sudo apt install certbot python3-certbot-nginx
```

Then run Certbot for your NGINX server:

```bash
sudo certbot --nginx
```

Just follow Certbot instructions and SSL will be enabled!



## Configure a stub server for getting a certificate:

You may need to get a certificate for a domain that is not exactly serving HTML page. In this case you may create a stub server just to answer to the challenge from Certbot:

```bash
server {
	listen 80;
	server_name example.com;

	client_max_body_size 10m;

	location /.well-known/acme-challenge/ {
		default_type "text/plain";
		allow all;
	}
}
```

