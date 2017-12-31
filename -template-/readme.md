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
