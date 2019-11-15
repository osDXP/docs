# Update checking
If desired, you can hook into our update checker system by following the below documentation.

As an outline, you will need to use the following filters:

 * **`osdxp_dashboard_plugin_update_checker_list`** filter to register your plugin slug;

 * **`osdxp_dashboard_plugin_file_{MODULE_SLUG}`** filter to return the absolute path to your main plugin file;

 * **`osdxp_dashboard_plugin_update_metadata_url_{MODULE_SLUG}`** filter to register the URL to the plugin update meta.

The URL endpoint should provide the module update metadata as a JSON adhering to the format outlined in this document: [Plugin Update Checker JSON API reference](https://docs.google.com/spreadsheets/d/1eOBbW7Go2qEQXReOOCdidMTf_tDYRq4JfegcO1CBPIs/edit#gid=0).

## Registering the Updater
The `osdxp_dashboard_plugin_update_checker_list` filter is used to register plugins to the osDXP update checker system.

It is applied in `wp-content/plugins/osdxp-dashboard/includes/licensing.php`, *inside the **`init_update_checker`** method*.

###Filter Parameters

1. An array containing plugin slugs (**`array`**)

###Filter Return Value

The filter callbacks need to return an **`array`** of plugin slugs.

###Example Code
```php
add_filter(
	'osdxp_dashboard_plugin_update_checker_list',
	function ()
	{
		if (!is_array($plugins)) {
			$plugins = [];
    	}
    	if (!in_array('example-module', $plugins, true)) {
     		$plugins[] = 'example-module';
    	}
    	return $plugins;
	}
);
```

## Registering the Module File
The `osdxp_dashboard_plugin_file_{MODULE_SLUG}` filter is used to register the full path to your main plugin file. This is necessary for the plugin update checker to work.

The filter is applied in `wp-content/plugins/osdxp-dashboard/includes/class-licenseapi.php`, *inside the **`initUpdateChecker`** method*.

**`{MODULE_SLUG}`** is your module folder name or file name if your plugin is only 1 file and stays in the root of `/wp-content/plugins/`.

###Filter Parameters

1. The full path to your modules main plugin file (**`string`**)

###Filter Return Value

The filter callback needs to return the full path to your main plugin file as a **string**.

###Example Code
```php
add_filter(
	'osdxp_dashboard_plugin_file_example-module',
	function ()
	{
		return '/full/path/to/wp-content/plugins/example-module/example-module.php';
	}
);
```

##Registering the Update Endpoint
The `osdxp_dashboard_plugin_update_metadata_url_{MODULE_SLUG}` filter is used to register the URL to the plugin update meta.

The URL endpoint should provide the module update metadata as a JSON adhering to the format outlined in this document: [Plugin Update Checker JSON API reference](https://docs.google.com/spreadsheets/d/1eOBbW7Go2qEQXReOOCdidMTf_tDYRq4JfegcO1CBPIs/edit#gid=0).

**The method used to query the metadata URL is POST. Please make sure your endpoint is set up this way if you would like to use our updater.**

The filter is applied in `wp-content/plugins/osdxp-dashboard/includes/class-licenseapi.php`, *inside the **`initUpdateChecker`** method*.

**`{MODULE_SLUG}`** is your module folder name or file name if your plugin is only 1 file and stays in the root of `/wp-content/plugins/`.

###Filter Parameters

1. URL to the plugin update meta (**`string`**)

###Filter Return Value

The filter callback needs to return the URL to the plugin update meta as a **string**.

###Example Code
```php
add_filter(
	'osdxp_dashboard_plugin_update_metadata_url_example-module',
	function ()
	{
		return 'https://web.site/url-to-plugin-update-meta.json';
	}
);
```

## Update Checker Class
If desired, your module can use the Update Checker class on its own.

The class is located in `wp-content/plugins/osdxp-dashboard/includes/dependencies/plugin-update-checker/class-osdxp-module-update-checker.php`.

Below is an example use case.
### Example Code
```php
add_action('plugins_loaded', function () {
	new OSDXP_Dashboard\OsdxpModuleUpdateChecker(
		‘https://myupdateurl.com/update-info.json’,
		‘my-plugin/my-plugin.php’,
		‘my-plugin’
	);
});
```
