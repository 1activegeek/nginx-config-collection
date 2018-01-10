# Contribution Guidelines
1. Search for the configuration first and validate it does not exist - if it does, modify the current config with appropriate notes as to why this needs to be altered/updated.
2. Ensure ALL documentation has been created per the formatting rules.
3. All Pull Requests must have valuable titles and notes. Leaving defaults and not properly updating will be denied.
4. Provide proof where available - if you have a working config for a publicly facing site - share that URL in the pull request to validate it's functioning.
5. Please do not leave ANY FQDN/HOSTNAME/IP in your file. Follow the directions about replacing these entries.
6. Entries for the main page with the list of configs should be in `[Name of App](/appname/appname.md)` format to link to your addition once the pull request is approved.
7. Where possible, add notes using `## Your note here` in the configuration files explaining what things are doing. This will help educate everyone in why certain lines may be needed, what they're doing, or how something works.

## Format
Each configuration will be placed into a folder under the apps directory of this repo, with the folder name matching the application. This will help to make it easier for everyone to find the specific configuration they are looking for. The short term goal is to add markdown based docs with the details for quick copy/paste and visual browsing. The long term plan is to adjust this repo to also include downloadable config files ready to drop into your configuration.

Each configuration MUST have the following:
- **appname.md** - This will be your landing page details for the base proxy details. You can view the [template](/-template/appname.md) and see all the below details and the formatting required. Please do not deviate from the format.
  - **Name of Application** - name of the app with a `[Name of App](https://url.of.app)` format to allow easy clickthrough for discovery.
  - **Official Proxy Documentation** - if the app has any details about using a proxy, provide a link to this documentation (wikis, articles, references, etc) in the first line of application notes below.
    - *PLEASE NOTE: This should not be some random Reddit post or forum post, but actual solid documentation by the app provider.*
  - **Application Notes** - anything of importance that should be noted: partially working, certain resources not loading, cautions about security, implications of use, etc. Also ensure that there is information regarding the version of an app that this has worked with. Variations may happen between versions and can help illustrate reasons why a config may not work.
  - **Authentication Support** - if this configuration supports authentication and whether there are any gotchas when trying to use authentication (i.e. App has authentication by default, must disable, double auth with app and nginx, etc)
  - **Sub-domain Compatible** - whether the app supports being placed in sub-domain (yes / no / untested) - if untested, be sure to mark the code section of the doc with an N/A
    - *NOTE: only use NO when you know for sure it will break or the app has acknowledged as such, not just because you couldn't get it to work*
  - **Sub-directory Compatible** - whether the app supports being placed in sub-directory blocks (yes / no / untested) - if untested, be sure to mark the code section of the doc with an N/A
    - *NOTE: only use NO when you know for sure it will break or the app has acknowledged as such, not just because you couldn't get it to work*

- **Location Directive** - The most frequent use of nginx is to add multiple sites to a single host. This allows easy access to multiple services/systems on a single host. For this reason, the Location Directive will be outlined at the top of each configuration set. This is because most users will be looking for this information. To keep this uniform, please be sure of the following:
- Do not use INCLUDE files - if there are, be sure to include all relevant details from them (i.e. proxy.conf)
- All information that would be substituted (as seen below in appname.conf explanation - Rule 5 above) is substituted
- If NOT providing detail for a sub-directory entry, simply input `NO LOCATION DIRECTIVE` into the code section
- If there are relevant If blocks, rewrites, etc outside the location - they should be included here as they would be in the server config file

The following sections should be contained within the `<details>` blocks below the sub-domain / sub-directory sections. This is intended to allow users an easy way to quickly view the code with proper syntax highlighting. If adding new sections, please be sure to use ` ```nginx` at the top of the section for proper highlighting. In the future, the goal will be to mirror these files into the app folder. This will allow a user to also download the configs directly if needed for quick drag and drop into their nginx config folders.

- **appname.conf** - This file will have the appropriate configuration details
  - All places where an FQDN should be placed, should be replaced with `<fqdn>`
  - All places where a hostname/IP should be placed, should be replaced with `<hostname>`
  - All places where a baseURL should be placed, should be replaced with `<baseurl>`
  - Please use discretion in the use of Ports - only specify ports that are necessary due to the config or known port for the app to operate on. If any ports are specified, please note this in the **Application Notes** section as to why this port was used.
- **proxy.conf / nginx.conf / other.conf** - If using references like `include`, be sure to link the appropriate files as well. Where possible, it would be best to remove all include lines and just add in your relevant content to the core `appname.conf` file. A user should be able to view your addition and see ALL information needed for this to work.
