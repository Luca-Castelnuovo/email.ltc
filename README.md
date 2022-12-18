# Postal Installation Tools

For full details on how to use it [read the documentation](https://docs.postalserver.io).

```bash
sudo apt install git curl jq -y

git clone https://github.com/Luca-Castelnuovo/mail.ltc /opt/postal/install
sudo ln -s /opt/postal/install/bin/postal /usr/bin/postal && sudo chmod +x /usr/bin/postal && sudo mkdir -p /etc/letsencrypt

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

postal bootstrap postal.example.com
postal initialize
postal make-user

### TLS SMTP setup (optional)
sudo apt install certbot python3-certbot-dns-cloudflare -y

# I use Cloudflare for DNS challenge
sudo tee /etc/letsencrypt/cloudflare.ini > /dev/null <<EOT
dns_cloudflare_api_token = AN_API_TOKEN_HERE
EOT
sudo chmod 0600 /etc/letsencrypt/cloudflare.ini

# If you want to use another DNS provider, you can use the appropriate certbot dns package
# https://eff-certbot.readthedocs.io/en/stable/using.html#dns-plugins
sudo certbot certonly -d postal.example.com \
    --dns-cloudflare --dns-cloudflare-credentials /etc/letsencrypt/cloudflare.ini \
    --non-interactive --agree-tos \
    --email email@domain.com

# TODO: create sed command
nano /opt/postal/config/postal.yml # Update line 19
### end TLS SMTP setup

postal start
```
