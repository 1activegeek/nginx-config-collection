# NGINX Configuration Collection

The goal of this repo is to be a central location for a listing of known-good working NGINX configurations. There is a planned format for these configurations so that it can ease the use of ensuring ALL relevant details are noted. This will include mandatory README files for EACH contribution, outlining all relevant details. The format will be noted in a link below.

This repository will hopefully grow and flourish from the addition of content from others. Please share this, star it, and contribute! The more folks we have contributing to this, the better off this can become.

## Format
Each configuration will be placed into a folder at the root of this repo, with the folder name matching the application. This will help to make it easier for everyone to find the specific configuration they are looking for. In addition, each configuration must have the following:
- **appname.conf** - This file will have the appropriate configuration details
- **README.md** - This will be your landing page details to explain the following items
  - **Name of Application** - self explanatory
  - **Application Notes** - anything of importance that should be noted (partially working, certain resources not loading, cautions about security, implications of use, etc)
  - **Supports Authentication** - if this configuration supports authentication and whether there are any gotchas when trying to use authentication (i.e. App has authentication by default, must disable, etc)
  - **Sub-domain Compatible** - whether the app supports being placed in sub-domain (yes / no / untested)
    - *NOTE: only use NO when you know for sure it will break or the app has acknowledged as such, not just because you couldn't get it to work*
  - **Sub-directory Compatible** - whether the app supports being placed in sub-directory blocks (yes / no / untested)
     - *NOTE: only use NO when you know for sure it will break or the app has acknowledged as such, not just because you couldn't get it to work*
  - **Additional Notes** - any additional notes of importance related to the configuration

## Contribution Guidelines
1. Search for the configuration first and validate it does not exist - if it does, modify the current config with appropriate notes as to why this needs to be altered/updated.
2. Ensure ALL documentation has been created per the formatting rules.
3. All Pull Requests must have valuable titles and notes. Leaving defaults and not properly updating will be denied. 
4. Provide proof where available - if you have a working config for a publicly facing site - share that URL in the pull request to validate it's functioning. 
