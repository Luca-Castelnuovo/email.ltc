# Postal Installation Tools

For full details on how to use it [read the documentation](https://docs.postalserver.io).

```bash
sudo apt install git curl jq -y

git clone https://github.com/Luca-Castelnuovo/mail.ltc /opt/postal/install
sudo ln -s /opt/postal/install/bin/postal /usr/bin/postal && sudo chmod +x /usr/bin/postal

docker run -d \
   --name postal-mariadb \
   -p 127.0.0.1:3306:3306 \
   --restart always \
   -e MARIADB_DATABASE=postal \
   -e MARIADB_ROOT_PASSWORD=postal \
   mariadb

docker run -d \
   --name postal-rabbitmq \
   -p 127.0.0.1:5672:5672 \
   --restart always \
   -e RABBITMQ_DEFAULT_USER=postal \
   -e RABBITMQ_DEFAULT_PASS=postal \
   -e RABBITMQ_DEFAULT_VHOST=postal \
   rabbitmq:3.8

postal bootstrap postal.yourdomain.com

postal initialize

postal make-user

postal start
```
