## Format
Each configuration will be placed into a folder at the root of this repo, with the folder name matching the application. This will help to make it easier for everyone to find the specific configuration they are looking for.

In addition, each configuration must have the following:
- **appname.conf** - This file will have the appropriate configuration details
  - All places where an FQDN should be placed, should be replaced with `<fqdn>`
  - All places where a hostname should be placed, should be replaced with `<hostname>`
  - All places where an IP should be placed, should be replaced with `<ip>`
  - Please use discretion in the use of Ports - only specify ports that are necessary due to the config or known port for the app to operate on. If any ports are specified, please note this in the **Application Notes** section as to why this port was used.
- **readme.md** - This will be your landing page details to explain the following items. You can view the [template](/-template-/readme.md) and see all the below details and the formatting required. Please do not deviate from the format.
  - **Name of Application** - name of the app with a `[Name of App](https://url.of.app)` format to allow easy clickthrough for discovery.
  - **App Documentation for Proxying** - if the app has any details about using a proxy, provide a link to this documentation (wikis, articles, references, etc)
    - *PLEASE NOTE: This should not be some random Reddit post or forum post, but actual solid documentation by the app provider.*
  - **Application Notes** - anything of importance that should be noted (partially working, certain resources not loading, cautions about security, implications of use, etc)
  - **Authentication Support** - if this configuration supports authentication and whether there are any gotchas when trying to use authentication (i.e. App has authentication by default, must disable, double auth with app and nginx, etc)
  - **Sub-domain Compatible** - whether the app supports being placed in sub-domain (yes / no / untested)
    - *NOTE: only use NO when you know for sure it will break or the app has acknowledged as such, not just because you couldn't get it to work*
  - **Sub-directory Compatible** - whether the app supports being placed in sub-directory blocks (yes / no / untested)
     - *NOTE: only use NO when you know for sure it will break or the app has acknowledged as such, not just because you couldn't get it to work*
- **proxy.conf / nginx.conf / other.conf** - If using references like `include`, be sure to link the appropriate files as well. Where possible, it would be best to remove all include lines and just add in your relevant content to the core `appname.conf` file. A user should be able to view your addition and see ALL information needed for this to work.


## Contribution Guidelines
1. Search for the configuration first and validate it does not exist - if it does, modify the current config with appropriate notes as to why this needs to be altered/updated.
2. Ensure ALL documentation has been created per the formatting rules.
3. All Pull Requests must have valuable titles and notes. Leaving defaults and not properly updating will be denied.
4. Provide proof where available - if you have a working config for a publicly facing site - share that URL in the pull request to validate it's functioning.
5. Please do not leave ANY FQDN/HOSTNAME/IP in your file. Follow the directions about replacing these entries.
6. Entries for the main page with the list of configs should be in `[Name of App](/appname/readme.md)` format to link to your addition once the pull request is approved.
