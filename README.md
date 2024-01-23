# Home Server Instructions

```bash
sudo apt install shellinabox
sudo systemctl enable shellinabox --now
sudo ufw allow in 4200

cd ~/Docker/Swag/config/nginx/proxy_confs/

vim terminal.subdomain.conf
```

Add the following into the file.

```yaml
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    server_name terminal.*;

    include /config/nginx/ssl.conf;

    client_max_body_size 0;

    location / {
        # enable the next two lines for http auth
        auth_basic "Restricted";
        auth_basic_user_file /config/nginx/.htpasswd;

        include /config/nginx/proxy.conf;
        include /config/nginx/resolver.conf;
        set $upstream_app 127.0.0.1;
        set $upstream_port 4200;
        set $upstream_proto http;
        proxy_pass $upstream_proto://$upstream_app:$upstream_port;
    }
}
```

Maybe try `192.168.1.196` if `127.0.0.1` doesn't work.

```bash
vim docker-compose.yml
```

Edit the `SUBDOMAINS` environment variable and add `terminal` and `soulseek` at the end of it, seperated by commas.

Test this out by restarting `swag` with the instructions below. 
Then check [terminal.plug-world.com](https://terminal.plug-world.com). There should be an http authentication, once past that the terminal should load.

```bash
cd ~/Docker/Swag/
docker-compose restart Swag
```
Maybe try "swag" if uppercase doesn't work.
