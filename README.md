# Conf docker multisite for wordpress

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
## 3- Configure Portainer
```
sudo docker volume create portainer_data
```
```
sudo docker run -d -p 9000:9000 -p 8000:8000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v /srv/portainer:/data portainer/portainer-ce
```
now browse to `http://youripaddress:9000` and will have access to the GUI 

### 4- Start and automate
```
sudo systemctl start docker
sudo systemctl enable docker
```

##  5- Prepare directory
You need to create a directory to house the wordpress data
```
sudo mkdir -p /srv/wordpress
cd /srv/wordpress/
```

## 6- Create yaml file
```
nano docker-compose.yml
```
Then copy the docker-compose-example (make sure to change the db data)

##  7- Run Wordpress
Make sure that you stay in the `wordpress` directory
```
sudo docker-compose up -d
```

##  8- Navigate to your wordpress installation
Go to `http://localhost:8080` or `http://youripaddress:8080` and you will have access to the Wordpress's configuration pannel

## 9- Shutdown the wordpress
```
docker stop [my_container]
```

## 10- Configure reverse proxy 

```
cd ..
mkdir reverse_proxy
cd reverse_proxy
```
create a new docker-compose.yml and then copy the `docker-compose-nginx-example`.

## 11- Config file










