# Conf docker multisite for wordpress

## 1- Install docker 
```
sudo apt update
sudo apt install docker.io
```

### Start and automate
```
sudo systemctl start docker
sudo systemctl enable docker
```

## 2- Prepare directory
You need to create a directory to house the wordpress data
```
sudo mkdir -p /srv/wordpress
cd /srv/wordpress/
```

## 3- Create yaml file
```
nano docker-compose.yaml
```
Then copy the docker-compose-example




