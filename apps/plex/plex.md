<!-- This should be the app name and the URL to the app site -->
<!-- If major revisions exist of an app that change the RP support follow the app name with V# -->
# [Plex](https://plex.tv)

## Application notes
<!-- This should point to any provided documentation by the app developer, if none, remove line below -->
<!-- This should be used to highlight/outline any special notes, or important points about the configs -->
It seems this requires use of a sub-directory. It actually has problems if placed on the root of a sub-domain. While it may work, it is not suggested to proxy ALL Plex traffic through this proxy. This would put a large load on the system unnecessarily.

If you desire to utilize the SSO feature with an app called Organizr, you will have to use this on a sub-directory. Sub domains should likely be supproted if desired, however it will not work with the SSO mechanism properly.

**EDIT: 2018-03-16 - The old mechanism seems to not be working as properly as it did previously, at least in my setup. I've switched to this which is working. It had something to do with the way the Plex system looks for deviceID or auth Tokens when it's not seen the device previously.**
<!-- This will be used to outline all the pertinent block details -->
Block Details | Supported | Notes
------ | ------ | ------
authentication | Untested | Has Plex auth built in, it actually may not function with another auth in front
sub-directory | Yes |
sub-domain | N/A |
base URL | Yes | You must use /plex, it can not be altered
iFrame | Yes |

<!-- This will be used to sample out the Location block for sub-directory config -->
## Location Directive
```nginx
location /plex {
  return 301 /web;
}
location ~ ^/(\?(?:.*)(X-Plex-Device=)|web|video|photo|library|web|status|system|updater|clients|:|playQueues)(.*) {
  proxy_pass http://<hostname>:32400/; ## Default <port> is 32400, adjust if necessary
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";

  # Basic Proxy Config
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
  proxy_http_version 1.1;
  proxy_no_cache $cookie_session;
}
```
<!-- This is to be used to show code for a sub-directory config -->
## Sub-Directory Configuration

<details>

<summary> Expand for Code </summary>

### plex.conf
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

  location /plex {
    return 301 /web;
  }
  location ~ ^/(\?(?:.*)(X-Plex-Device=)|web|video|photo|library|web|status|system|updater|clients|:|playQueues)(.*) {
    proxy_pass http://<hostname>:32400/; ## Default <port> is 32400, adjust if necessary
    include /config/nginx/proxy.conf; ## Using a single include file for commonly used settings
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

<!-- This is to be used to show code for a sub-domain config -->
## Sub-Domain Configuration

<details>

<summary> Expand for Code </summary>

```
N/A
```

</details>
