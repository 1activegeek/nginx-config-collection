<!-- This should be the app name and the URL to the app site -->
<!-- If major revisions exist of an app that change the RP support follow the app name with V# -->
# [Organizr](https://organizr.us)

## Application notes
<!-- This should point to any provided documentation by the app developer, if none, remove line below -->
[Official Proxy Documentation](https://github.com/causefx/Organizr/wiki/Authentication-%7C-Server-Based#nginx)

<!-- This should be used to highlight/outline any special notes, or important points about the configs -->
Enables you to lock down services by using [auth_request](http://nginx.org/en/docs/http/ngx_http_auth_request_module.html) to allow users that are logged in to organizr to use the location.

Does not work great with APIs connecting to your service. Follow this [guide](https://technicalramblings.com/blog/allowing-mobile-apps-work-with-services-using-organizr-server-auth/) for a write up on how to allow `/api`.

<!-- This will be used to outline all the pertinent block details -->
Block Details | Supported | Notes
------ | ------ | ------
authentication | N/A |
sub-directory | Yes |
sub-domain | Yes |
base URL | N/A |
iFrame | N/A |


<!-- This is to be used to show code for a sub-directory config -->
## Sub-Directory Configuration

<details>

<summary> Expand for Code </summary>

### organizr.conf
```nginx
    location /auth-admin { rewrite ^ /auth.php?admin; } #You can ban users and IP's, check Documentation
    location /auth-user { rewrite ^ /auth.php?user; }   #You can ban users and IP's, check Documentation
```

</details>

<!-- This is to be used to show code for a sub-domain config -->
## Sub-Domain Configuration

<details>
It s easiest to include this file in all your subdomains.
<summary> Expand for Code </summary>

### sub_auth.conf
```nginx
location = /auth-admin {
    proxy_pass <fqdn>/auth-admin;
    proxy_pass_request_body off;
    proxy_set_header Content-Length "";
}
location = /auth-user {
    proxy_pass <fqdn>/auth-user;
    proxy_pass_request_body off;
    proxy_set_header Content-Length "";
}
```

</details>