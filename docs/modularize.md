#Module Registration
In order for your plugin to appear as a module in the installed modules page, it has to first be recognized as a module by osDXP.
This means it needs to be a part of **the available modules data**.
## Available Modules Endpoint

Available modules data is retrieved in the following order:

**transient > remote > local**

### Definition
The available modules endpoint is defined in **the main plugin file, at line 62**:

`define('OSDXP_DASHBOARD_AVAILABLE_MODULES_LIST_URL', OSDXP_DASHBOARD_API_URL . '/modules');`

### Customization
This definition can be changed with either **your own endpoint** or with false if you only want to parse the information available in the local json file, available at:

`/wp-content/plugins/osdxp-dashboard/default-available-modules.json`

This file also provides an example for how your endpoint should structure the returned data in order for it to be parsable by the plugin.

### Addition
Please [get in touch with us](https://osdxp.org/contact/) if you would like your module to appear in the default available modules endpoint.

## Available Modules Filter
osDXP Dashboard exposes two available filters that, when used in conjunction, allows plugins to declare themselves as modules.

_This method **only works** when the plugin is installed and activated, since your logic needs to be executed._

### Definition
These filters are:

 * `osdxp_get_modules`

 * `osdxp_get_available_modules`

### Addition
Please find below an example usage of this method.
```php
add_filter('osdxp_get_modules','my_module');
add_filter('osdxp_get_available_modules','my_module');

//setting up module information
function my_module($modules) {
	//plugin basename
	$slug = 'my-module/my-module.php';

	//plugin header info
	//second param is false to strip extra markup injected by WP
	$modules[$slug] = get_plugin_data(WPMM_PLUGIN_FILE, false);

	//optional - if not set will output a placeholder logo
	$modules[$slug]['logo'] = 'https://my-module.com/my-logo.svg';

	//the 'Get Module' URL present in the Available Modules Page
	$modules[$slug]['url'] = 'https://my-module.com';

	//pricing info. can be left unset if not premium.
	$modules[$slug]['price'] = '99.99';
	$modules[$slug]['before-price-text'] = 'From';
	$modules[$slug]['after-price-text'] = 'Per Year';

	return $modules;
}
```
