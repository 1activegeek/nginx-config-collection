<!-- This should be the app name and the URL to the app site -->
<!-- If major revisions exist of an app that change the RP support follow the app name with V# -->
# [NZBGet](https://portainer.io)

## Application notes
<!-- This should point to any provided documentation by the app developer, if none, remove line below -->
[Official Proxy Documentation](https://portainer.readthedocs.io/en/stable/faq.html#how-can-i-configure-my-reverse-proxy-to-serve-portainer)

<!-- This should be used to highlight/outline any special notes, or important points about the configs -->
It's important to note that if you proxy this container, and attempt to restart either the nginx container or this container, you can run into issues. Specifically when restarting the nginx container, you will lose access to the interface until nginx is back online. If you attempt to restart the portainer container, you will effectively end up losing it because the connection to the web console is lost.
<!-- This will be used to outline all the pertinent block details -->
Block Details | Supported | Notes
------ | ------ | ------
authentication | Yes | It has it's own built-in authentication, it's best to setup location (IP) based auth to limit access to internal only
sub-directory | Yes |
sub-domain | Untested |
base URL | No |
iFrame | Yes |

<!-- This will be used to sample out the Location block for sub-directory config -->
## Location Directive
```nginx
location /<baseurl>/ {
  proxy_pass http://<hostname>:9000/; ## Default <port> is 9000, adjust if necessary

  # Basic Proxy Config
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
  proxy_http_version 1.1;
  proxy_no_cache $cookie_session;
}
location /<baseurl>/api/websocket/ {
  proxy_pass http://<hostname>:9000/api/websocket/; ## Default <port> is 9000, adjust if necessary

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

### portainer.conf
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

  location /<baseurl>/ {
    proxy_pass http://<hostname>:9000/; ## Default <port> is 9000, adjust if necessary
    include /config/nginx/proxy.conf; ## Using a single include file for commonly used settings
  }
  location /<baseurl>/api/websocket/ {
    proxy_pass http://<hostname>:9000/api/websocket/; ## Default <port> is 9000, adjust if necessary
    include /config/nginx/proxy.conf; ## Using a single include file for commonly used settings
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

```nginx
N/A
```

</details>
