# [Template](https://github.com/1activegeek/nginx-config-collection) <!-- This should be the app name -->

## Reverse Proxy Documentation

If there was documentation for using this app with a reverse proxy, I would link it here. If it's short enough, it can be copy/pasted here, but should still have a link to the original as well.

## Application notes
This is for version 1 of the app. I'd outline also that this app doesn't need anything special for the core proxypass, but it does need a rewwrite rule for a specific sub-directory. For this reason, while sub-directory and sub-domain work, it's probably best to use a sub-domain so as not to interfere with other possible proxy entries.


Block Details | Supported | Notes
------| ------ | ------
authentication | Yes | N/A
sub-directory | Yes | Due to rewrite requirement, it may be better suited for sub-domain usage. This is the format of the config file.
sub-domain | Yes | N/A

<details>

<summary> Config Code</summary>

## Sub-Direcotry Server Configuration Block

```
## Main server block to redirect traffic from HTTP to HTTPS
server {
  listen 80;
  server_name <fqdn>;
  return 301 https://$host$request_uri;
}

## Main server block for HTTPS
server {
  listen 443 ssl;

  root /config/www;
  index index.html index.htm index.php;

  server_name <fqdn>;

  ssl_certificate /config/keys/letsencrypt/fullchain.pem;
  ssl_certificate_key /config/keys/letsencrypt/privkey.pem;

## Strong Security recommended settings per cipherli.st
  ssl_protocols TLSv1.2;
  ssl_prefer_server_ciphers on;
  ssl_dhparam /config/nginx/dhparams.pem;
  ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384;
  ssl_ecdh_curve secp384r1; # Requires nginx >= 1.1.0
  ssl_session_timeout  10m;
  ssl_session_cache shared:SSL:10m;
  ssl_session_tickets off; # Requires nginx >= 1.5.9
  ssl_stapling on; # Requires nginx >= 1.3.7
  ssl_stapling_verify on; # Requires nginx => 1.3.7
  add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
  add_header X-Frame-Options SAMEORIGIN;
  add_header X-Content-Type-Options nosniff;
  add_header X-XSS-Protection "1; mode=block";
  add_header X-Robots-Tag none;

  client_max_body_size 0;

  location /template/ {
    proxy_pass  http://<hostname>/;
    include /config/nginx/proxy.conf;
  }
```

### Proxy.conf for Sub-Directory Config

```
client_max_body_size 10m;
client_body_buffer_size 128k;

#Timeout if the real server is dead
proxy_next_upstream error timeout invalid_header http_500 http_502 http_503;

# Advanced Proxy Config
send_timeout 5m;
proxy_read_timeout 240;
proxy_send_timeout 240;
proxy_connect_timeout 240;
proxy_hide_header X-Frame-Options;

# Basic Proxy Config
proxy_set_header Host $host:$server_port;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto https;
proxy_bind $server_addr;
proxy_redirect  http://  $scheme://;
proxy_http_version 1.1;
proxy_set_header Connection "";
proxy_set_header Connection "upgrade";
proxy_set_header Upgrade $http_upgrade;
proxy_no_cache $cookie_session;
proxy_buffers 32 4k;
```

</details>
