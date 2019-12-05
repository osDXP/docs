#Getting Started
This documentation is structured around the assumption that you already have the basic knowledge required to create a Wordpress Plugin.
##Installation
###Via the release asset

 * Open the latest release, available from the releases page [here](https://github.com/osDXP/osdxp-dashboard/releases).

 * Download the `osdxp-dashboard.zip` file from the release assets.

 * Upload and install in your Wordpress instance as a regular plugin.

 * Enjoy!

###Via the repo

 * Clone the repository in `/wp-content/plugins/osdxp-dashboard`

 * Run `composer install`.

 * Enjoy!

###Via Composer

 * Run `composer require osdxp/osdxp-dashboard`.

 * Enjoy!

###Building assets

 * To compile source JS and SCSS files there are several options available.

 * First, run `npm install` to pull missing dependencies.

 * Run `npm run build` to compile development-focused, unminified files.

 * Run `npm run prod` to compile production-ready, minified files.

 * Run `npm run watch` or `npm run watch-prod` to compile with browsersync enabled(injecting modifications without page reload).

 * Enjoy!

##Plugin Conversion
If you already have a plugin developed and would like to convert it to be compatible with osDXP, following this developer reference will provide you with the required information to achieve osDXP compatibility.

###Resources
Below you can find examples of osDXP plugin conversions, available from the [osDXP GitHub organization](https://github.com/osDXP) repos.

 * [Example Module Integration, using WP Maintenance Mode plugin](https://github.com/osDXP/example-module-integration)
 * [Shared Counts for osDXP](https://github.com/osDXP/osdxp-shared-counts)
 * [Login Logo for osDXP](https://github.com/osDXP/osdxp-login-logo)

##Versioning
For transparency into our release cycle and in striving to maintain backward compatibility, **osDXP Dashboard** is maintained under [the Romantic Versioning guidelines](http://blog.legacyteam.info/2015/12/romver-romantic-versioning/). Sometimes we screw up, but we adhere to those rules whenever possible.