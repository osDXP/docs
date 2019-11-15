#Licensing inside osDXP
osDXP strives to provide a central location for users to handle module licensing. This is available in the **Installed Modules** page.

Below you can find the documentation needed to hook into osDXP licensing functionality.
## License Key Input

Displaying a license key input can be achieved by using the `osdxp_license_key_{MODULE_SLUG}` filter, where **`{MODULE_SLUG}`** is your module folder name or file name if your plugin is only one file and stays in the root of `/wp-content/plugins/`.

Both filters are applied in `wp-content/plugins/osdxp-dashboard/includes/dependencies/wordpress/class-osdxp-modules-list-table.php`, *inside the **_single_row_** method*.

### Filter Parameters
1. Initial license key

    * Defaults to **`false`**

    * Can either be **`false`**, **`null`** or a **`string`** (the license key)

        * If the license key is **`false`**, it means that the plugin doesn’t need any licensing, so the license key or input will not be displayed.

        * If the license key is **`null`**, then the license key input will be displayed.

        * If the license key is a **`string`**, then the license key will be displayed.


2. Plugin slug (**string**)

### Filter Return Value

The filter callbacks need to return either **`false`**, **`null`** or a **`string`**, as described above.

### Example Code
```php
add_filter('osdxp_license_key_my-example-plugin', 'my_example_license_key');
function my_example_license_key($license_key) {
	$valid 		  = MyPluginClass::is_valid_key($license_key);
	if ($valid) {
		return esc_html__( $license_key, 'my-example-plugin' );
	} elseif (false !== $license_key) {
		return esc_html__( 'Invalid or Expired.', 'my-example-plugin' );
	}
	return $license_key;
}
```


## License Field Markup

You can customize the markup for the license key input by using the **`osdxp_dashboard_license_field_markup`** & **`osdxp_dashboard_license_key_markup`** filters.

The filter is applied in `wp-content/plugins/osdxp-dashboard/includes/dependencies/wordpress/class-osdxp-modules-list-table.php`, *inside the **_single_row_** method*.

### Filter Parameters

1. License key (input field) markup (**string**)

2. Plugin slug (**string**)

### Filter Return Value

The filter callbacks need to return a **`string`**, which will be the license key (input field) markup.

### Example Code
Below is an example of modifying the license key (input field) markup, using an example plugin called `my-example-plugin`.
```php
add_filter('osdxp_dashboard_license_field_markup', 'my_license_field_markup');
add_filter('osdxp_dashboard_license_key_markup', 'my_license_key_markup');

function my_license_field_markup($field_markup, $plugin_slug) {
	//Only apply to own module
	if ('my-example-plugin' !== $plugin_slug) {
		return $field_markup;
	}

	$markup = '
		<div class="license-input-wrapper">
		<label for="my-example-plugin-license-field"><strong>Enter License Key:</strong></label>
		<input id="my-example-plugin-license-field"
			class="js-osdxp-module-license" type="text" size="40"
			data-module="my-example-plugin"
		>
		<button class="js-osdxp-submit-module-license button-primary">Submit</button>
		</div>
		';
	return $markup;
}

function my_license_key_markup($key_markup, $plugin_slug) {
	//Only apply to own module
	if ('my-example-plugin' !== $plugin_slug) {
		return $key_markup;
	}

	return '<p><strong>Valid Key!</strong></p>';
}
```

## Processing Submissions

Once a license key is submitted, it is sent to a REST endpoint on the WP site and passed through to the **`osdxp_dashboard_license_submit_response`** filter, to process the license key and return a response array.

This filter gives you the flexibility required to write whatever logic you need to process the submitted license key.

The filter is applied in `wp-content/plugins/osdxp-dashboard/includes/licensing.php`, *inside the **_handle_license_submit_** method*.

The REST endpoint is registered by the **`register_rest_endpoints`** function and the requests to it are handled by the **`handle_license_submit`** function, both being located in `wp-content/plugins/osdxp-dashboard/includes/licensing.php`.

*The endpoint accepts `POST` requests to the following URL: **_`/wp-json/osdxp-dashboard/v1/license/{MODULE_SLUG}/{LICENSE_KEY}`_**.*

In this convention, **`{MODULE_SLUG}`** is your module folder name or file name if your plugin is only one file and stays in the root of `/wp-content/plugins/`, and **`{LICENSE_KEY}`** is the submitted license key.

### Filter Parameters

1. Response array which initially is just an empty array  (**`array`**)

2. Plugin slug (**`string`**)

3. License key (**`string`**)

### Filter Return value

The filter callbacks need to return an **`array`** with the following structure:

* **license_key_markup (`string`)** : if present, the license key field will be replaced with this markup string. You can apply the **`osdxp_dashboard_license_key_markup_{MODULE_SLUG}`** filter and pass the license key, to get the license key markup.

* **error_messages** (**array**): an array of error messages;

* **success_message** (**array**): an array of success messages.

### Example Code
Below is an example of processing licensing keys using an example plugin called `example-module`.
```php
add_filter('osdxp_dashboard_license_submit_response', 'process_license_key_submit', 10, 3);

function process_license_key_submit($response, $plugin_slug, $license_key) {
	// Process only the license submission for our plugin.
	if ('example-module' !== $plugin_slug) {
		return $response;
	}

	if (!is_array($response)) {
		$response = [];
	}

	// Sanitize license key.
	$license_key = sanitize_text_field($license_key);

	/*
	The following is an example licensing method.
	You would need to replace this and perhaps the following logic, with your own functinality.
	*/
	$license_api = LicenseAPI::getInstance($plugin_slug);
	$license_api->setAPIKey($license_key);
	$license_data = $license_api->getAccount();

	if (is_wp_error($license_data)) {
		// Encountered a specific error.
      	if (empty($response['error_messages']) || !is_array($response['error_messages'])) {
			$response['error_messages'] = [];
      	}
		// Keep previous error messages.
		$response['error_messages'] = array_merge(
			$response['error_messages'],
			array_map('esc_html', $license_data->get_error_messages())
		);
	} elseif ($license_data) {
		save_license_key_and_data($plugin_slug, $license_key, $license_data);

		// Get license key markup.
		$response['license_key_markup'] = apply_filters(
			'osdxp_dashboard_license_key_markup_' . $plugin_slug,
			$license_key
		);

		// Keep previous success messages.
		if (empty($response['success_messages']) || !is_array($response['success_messages'])) {
			$response['success_messages'] = [];
		}
		$response['success_messages'][] = esc_html__('License successfully added.', 'osdxp-dashboard');
	} else {
	// No error was encountered, but the license wasn't successfully added either.
	$response['error_messages'][] = esc_html__(
		'An error has occurred when trying to process your license key. Please try again later.',
		'example-module'
	);
}

return $response;
}
```

## Processing Removal Requests

After a license has been added into the system, a "Remove Key" button will be displayed next to the license key. Once clicked, a confirmation popup will appear.
If confirmed, an AJAX request will be sent to the license key removal REST endpoint, on the WP site, and passed through to the **`osdxp_dashboard_license_deletion_response`** filter to process the removal request and return a response.

The REST endpoint is registered by the **`register_rest_endpoints`** function and the requests to it are handled by the **`handle_license_deletion`** function, both being located in `wp-content/plugins/osdxp-dashboard/includes/licensing.php`.

*The endpoint accepts DELETE requests  to the following URL: **`/wp-json/osdxp-dashboard/v1/license/{MODULE_SLUG}`**.*

In this convention, **`{MODULE_SLUG}`** is your module folder name or file name if your plugin is only one file and stays in the root of `/wp-content/plugins/`.

### Filter Parameters

1. Response array which initially is just an empty array  (**`array`**)

2. Plugin slug (**`string`**)

### Filter Return Value

The filter callbacks need to return an **array** with the following structure:

* **success (`int`)**: it has a value of **1**, if the key removal was completed successfully - this will tell the JS from the Installed Modules to remove the license key markup and display the license key input field, so that a new key can be entered;

* **error_messages (`array`)**: an array of error messages;

* **success_message (`array`)**: an array of success messages.

### Example Code
```php
add_filter('osdxp_dashboard_license_deletion_response', 'process_license_key_deletion', 10, 2);

function process_license_key_deletion($response, $plugin_slug) {
	// Return response as-is if the request was not for your plugin.
	if ('test-module' !== plugin_slug) {
	return $response;
	}

	if (!is_array($response)) {
	$response = [];
	}

	// This is where you would place your code to delete the license key.
	ExampleClass::deleteLicense();

	// Build response array.
	$response['success'] = 1;
	$response['success_messages'][] = esc_html__('License successfully removed.', 'test-module');

	return $response;
}
```

## Setting Persistent Errors

If you want to set a persistent error, you can create a site transient called **`dxp_authorization_error_{MODULE_SLUG}`** to store the error and display it dynamically using the **`render_plugins_license_errors`**function, which is a hook for the **`admin_notices`** action.

See the **`get_license_error_transient_name`** function inside `wp-content/plugins/osdxp-dashboard/includes/licensing.php`.

You will also need to set a callback for the **`osdxp_dashboard_plugin_name_{MODULE_SLUG}`** filter, to return the human-readable plugin name (e.g.: Test Module).

In this convention, **`{MODULE_SLUG}`** is your module folder name or file name if your plugin is only one file and stays in the root of `/wp-content/plugins/`.

### Filter Parameters

1. Plugin name (**`string`**)

### Filter Return value

The filter callback needs to return the human-readable plugin name as a **string**.

### Example Code
```php
add_filter(
	'osdxp_dashboard_plugin_name_test-module',
	function ()
	{
		return 'Test Module';
	}
);
```

## Displaying license status

In order to display license status information for your module, you can use the **`osdxp_dashboard_license_data`** and **`osdxp_dashboard_license_data_{MODULE_SLUG}`** filters, where **`{MODULE_SLUG}`** is your module folder name or file name if your plugin is only 1 file and stays in the root of `/wp-content/plugins/`.

### Filter Parameters

1. License info, which is an array containing the following keys:

    1. **expiry** (**`int`**) - expiry time in UNIX timestamp format; e.g.: 1568723352

    2. **status** (**`string`**) - a short **string** to reflect license status; e.g.: "Premium - 25 sites", “Expired”, “Active”

2. Plugin slug (**`string`**)

### Filter Return Value

The filter callbacks need to return an **`array`** with a structure similar to the one specified in the Filter Parameters.

### Example Code

**`add_filter('osdxp_dashboard_license_data', 'my_license_information');`**
```php
function my_license_information($license_data, $plugin_slug) {
	if ('test-module' === $plugin_slug) {
		return [
			'expiry' => 1568723352,
			'status' => 'Active',
		];
	}

	return $license_data;
}
```

**`add_filter('osdxp_dashboard_license_data_test-module', 'my_license_information');`**
```php
function my_license_information() {
	return [
		'expiry' => 1568723352,
		'status' => 'Active',
	];
}
```
