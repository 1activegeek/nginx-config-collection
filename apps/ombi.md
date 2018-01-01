# [Ombi](https://www.ombi.io) <!-- This should be the app name -->

## Reverse Proxy Documentation

[Ombi Proxy Documentation](https://github.com/tidusjar/Ombi/wiki/Nginx-Reverse-Proxy-examples-(Linux))

## Application notes

There are 2 different versions of the app. Please be aware that there are some differences between the versions.


Block Details | Supported | Notes
------| ------ | ------
authentication | Yes | N/A
sub-directory | Yes | This is the format used for the example. Using V3, so it does include a rewrite as well.
sub-domain | Untested | N/A


## Sub-Directory Configuration

<details>

<summary> Expand for Code </summary>

### Ombi.conf

```
## Main server block redirect HTTP to HTTPS
server {
  listen 80;
  server_name <fqdn>;
  return 301 https://$host$request_uri;
}

## Main server block serving HTTPS
server {
  listen 443 ssl;

  root /config/www;
  index index.html index.htm index.php;

  server_name <fqdn>;

## Location for SSL Certificates
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

## Ombi app block
  location /ombi/ {
    proxy_pass  http://<hostname>:<port>/;
    include /config/nginx/proxy.conf;
  }
     if ($http_referer ~* /ombi/) {
            rewrite ^/dist/([0-9\d*]).js /ombi/dist/$1.js last;
    }

```

### Proxy.conf

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

# Basic Proxy Config
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_redirect  http://  $scheme://;
proxy_http_version 1.1;
proxy_no_cache $cookie_session;
proxy_buffers 32 4k;

## Ombi specific

proxy_cache_bypass $http_upgrade;
proxy_set_header Host $host;
proxy_set_header Connection keep-alive;
proxy_set_header Upgrade $http_upgrade;
```

</details>
