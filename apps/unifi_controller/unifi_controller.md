<!-- This should be the app name and the URL to the app site -->
<!-- If major revisions exist of an app that change the RP support follow the app name with V# -->
# [Unifi Controller](https://www.ubnt.com/software/)

## Application notes
<!-- This should point to any provided documentation by the app developer, if none, remove line below -->


<!-- This should be used to highlight/outline any special notes, or important points about the configs -->
The controller software is not known to work as a sub-directory. This could be incorrect if someone has a working configuration, but many forum conversations come to the same conclusion that it must reside on a sub-domain vs a sub-directory.

<!-- This will be used to outline all the pertinent block details -->
Block Details | Supported | Notes
------ | ------ | ------
authentication | Yes | The built in authentication should be used, if using other authentication you will have to login twice.
sub-directory | No |
sub-domain | Yes |
base URL | N/A | Being that his works on a sub-domain only, no Base URL is needed
iFrame | No | The app by default is no iFrame friendly. You must use a proxy_hide_header directive to remove this issue.

<!-- This will be used to sample out the Location block for sub-directory config -->
## Location Directive
```nginx
location / {
  proxy_pass  http://<hostname>:8443; ## Default <port> is 8443, adjust if necessary
  proxy_buffering off;
  proxy_set_header Host $host;
  proxy_hide_header X-Frame-Options;

  # Enables use of Websockets communication
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";

  # Basic Proxy Config
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
  proxy_http_version 1.1;
  proxy_no_cache $cookie_session;
}
location /ws/ { ## Websockets
  proxy_pass http://<hostname>:8443/ws/; ## Default <port> is 8443, adjust if necessary
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
}
```
<!-- This is to be used to show code for a sub-directory config -->
## Sub-Directory Configuration

**Not supported**

<!-- This is to be used to show code for a sub-domain config -->
## Sub-Domain Configuration

<details>

<summary> Expand for Code </summary>

### unifi_controller.conf
```nginx
## Main server block to redirect traffic from HTTP to HTTPS
server {
  listen 80;
  server_name <fqdn>;
  return 301 https://$host$request_uri;
}

## Main server block for HTTPS
server {
  listen 443 ssl;
  server_name <fqdn>;

  root /config/www;
  index index.html index.htm index.php;
  include /config/nginx/ssl.conf ## Using a single include for all SSL related items

  location / {
    proxy_pass  http://<hostname>:8443; ## Default <port> is 8443, adjust if necessary
    proxy_buffering off;
    proxy_set_header Host $host;
    proxy_hide_header X-Frame-Options;
    include /config/nginx/proxy.conf

    # Enables use of Websockets communication
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
  location /ws/ { ## Websockets
    proxy_pass http://<hostname>:8443/ws/; ## Default <port> is 8443, adjust if necessary
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
```
### proxy.conf
```nginx
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
proxy_set_header Host $host:$server_port;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_redirect  http://  $scheme://;
proxy_http_version 1.1;
proxy_set_header Connection "";
proxy_cache_bypass $cookie_session;
proxy_no_cache $cookie_session;
proxy_buffers 32 4k;
```
### ssl.conf
```nginx
## Certificates from LE container placement
ssl_certificate /config/keys/letsencrypt/fullchain.pem;
ssl_certificate_key /config/keys/letsencrypt/privkey.pem;

## Strong Security recommended settings per cipherli.st
ssl_dhparam /config/nginx/dhparams.pem;
ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384;
ssl_ecdh_curve secp384r1; # Requires nginx >= 1.1.0
ssl_session_timeout  10m;

## Settings to add strong security profile (A+ on securityheaders.io/ssllabs.com)
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";
add_header X-Robots-Tag none;
add_header Content-Security-Policy "frame-ancestors https://*.<fqdn> https://<fqdn>"; ## Use *.domain.com, not *.sub.domain.com
add_header X-Frame-Options "ALLOW-FROM https://*.<fqdn>" always; ## Use *.domain.com, not *.sub.domain.com
add_header Referrer-Policy "strict-origin";
proxy_cookie_path / "/; HTTPOnly; Secure";
more_set_headers "Server: Classified";
more_clear_headers 'X-Powered-By';
```

</details>
