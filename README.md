# Conf docker multisite for wordpress
this is a quick tutorial to install and configure a multisites with docker and wordpress
___________________________________________________________________________________________________________________________________________________________________

## 1- Install docker 
Install the additionnal package
```
sudo apt update
sudo apt install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

Add Docker’s official GPG key:
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

```
Install docker 
```
apt update
apt install docker-ce docker-ce-cli containerd.io
```

## 2- Install docker-compose
```
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```
```
sudo chmod +x /usr/local/bin/docker-compose
```

## 3- Install Mariadb
```
sudo apt install mariadb-server
```

## 4- Configure Portainer
```
sudo docker volume create portainer_data
```
```
sudo docker run -d -p 9000:9000 -p 8000:8000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v /srv/portainer:/data portainer/portainer-ce
```
now browse to `http://youripaddress:9000` and will have access to the GUI 

### 5- Start and automate
```
sudo systemctl start docker
sudo systemctl enable docker
```

##  6- Prepare directory
You need to create a directory to house the wordpress data
```
sudo mkdir -p /srv/wordpress
cd /srv/wordpress/
```

## 7- Create yaml file
```
nano docker-compose.yml
```
Then copy the docker-compose-example (make sure to change the db data)

##  8- Run Wordpress
Make sure that you stay in the `wordpress` directory
```
sudo docker-compose up -d
```

##  9- Navigate to your wordpress installation
Go to `http://localhost:8080` or `http://youripaddress:8080` and you will have access to the Wordpress's configuration pannel

## 10- Shutdown the wordpress
```
docker stop [my_container]
```

## 11- Create a docker network
```
docker network create dockerwp
```

## 12- Start everything 
Nginx
```
docker run --name nginx-proxy --net dockerwp -p 80:80 -p 443:443 -v ~/certs:/etc/nginx/certs -v /etc/nginx/vhost.d -v /usr/share/nginx/html -v /var/run/docker.sock:/tmp/docker.sock:ro --label com.github.jrcs.letsencrypt_nginx_proxy_companion.nginx_proxy -d --restart always jwilder/nginx-proxy
```
Let’s Encrypt Nginx Proxy Companion
```
docker run --name letsencrypt-nginx-proxy-companion --net dockerwp -v ~/certs:/etc/nginx/certs:rw -v /var/run/docker.sock:/var/run/docker.sock:ro --volumes-from nginx-proxy -d --restart always jrcs/letsencrypt-nginx-proxy-companion
```
Mariadb
```
docker run --name mariadb1 --net dockerwp -v mariadb1:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=supersecret -e MYSQL_DATABASE=db1 -e MYSQL_USER=db1user -e MYSQL_PASSWORD=secret -d --restart always mariadb
```
```
docker run --name mariadb2 --net dockerwp -v mariadb2:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=supersecret -e MYSQL_DATABASE=db2 -e MYSQL_USER=db2user -e MYSQL_PASSWORD=secret -d --restart always mariadb
```

Wordpress
```
docker run --name wordpress1 --net dockerwp -v wordpress1:/var/www/html -v ~/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini -e WORDPRESS_DB_HOST=mariadb1:3306 -e WORDPRESS_DB_NAME=db1 -e WORDPRESS_DB_USER=db1user -e WORDPRESS_DB_PASSWORD=secret -e VIRTUAL_HOST=sub.domain1.com -e LETSENCRYPT_HOST=sub.domain1.com -e LETSENCRYPT_EMAIL=admin@domain1.com -d --restart always wordpress
```
```
docker run --name wordpress2 --net dockerwp -v wordpress2:/var/www/html -v ~/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini -e WORDPRESS_DB_HOST=mariadb2:3306 -e WORDPRESS_DB_NAME=db2 -e WORDPRESS_DB_USER=db2user -e WORDPRESS_DB_PASSWORD=secret -e VIRTUAL_HOST=sub.domain2.com -e LETSENCRYPT_HOST=sub.domain2.com -e LETSENCRYPT_EMAIL=admin@domain2.com -d --restart always wordpress
```




___________________________________________________________________________________________________________________________________________________________________

Now that everything is good you will need to change all of your domain name...



