# Styling

## Dashboard Class or Constants
In order to maintain design uniformity and be able to style your module markup specifically for osDXP, you can make use of either the CSS class or PHP constant defined by osDXP Dashboard.

When the Dashboard is active, the `dxp-dashboard` class is added to the `body` element class list, allowing you to scope styles for osDXP-only use.

There is also the `OSDXP_DASHBOARD_IS_ACTIVE` constant that gets defined and set to `1` when the osDXP Dashboard is visible. In legacy WordPress, this is unset.

### Example Code

`.wp-admin.dxp-dashboard .my-class { color: red; }`

## Notification Threshold
In order to maintain clarity, osDXP Dashboard will concatenate all administrator notifications into a single, expandable, notification.

If desired, this functionality can be hooked into via the `osdxp_notifications_threshold` filter in order to define the threshold at which concatenation will happen.

### Filter Return Value
The filter callback needs to return the threshold number as an **`integer`**.

### Example Code
```php
add_filter(
  'osdxp_notifications_threshold',
  function () {
    //will collapse notifications if 20 or more present
    return 20;
  }
);
```

## Dashboard Widgets
Modules may output an osDXP Dashboard widget by using any of the following filters:

* `osdxp_dashboard_editor_create_functionality` - *Create widget* for **Editor**

* `osdxp_dashboard_editor_manage_functionality` - *Manage widget* for **Editor**

* `osdxp_dashboard_multisite_create_functionality` - *Create widget* for **Multisite Admin**

* `osdxp_dashboard_multisite_manage_functionality` - *Manage widget* for **Multisite Admin**

* `osdxp_dashboard_network_create_functionality` - *Create widget* for **Network Admin**

* `osdxp_dashboard_network_manage_functionality` - *Manage widget* for **Network Admin**

* `osdxp_dashboard_single_create_functionality` - *Create widget* for **Single-site Admin**

* `osdxp_dashboard_single_manage_functionality` - *Manage widget* for **Single-site Admin**

### Filter Parameters
You hooked function needs to accept an array `$widgets`.
Your function needs to append to said array an array consisting of the following keys:

 * `title` - Your widget title
 * `subtitle` - Your widget subtitle
 * `link` - Your widget link
 * `icon` - Your widget icon in the form of a dashicons class
 * `button_text` - Your widget button text - **Only for manage-type widgets**

### Filter Return Value
Your hooked function needs to return an array of arrays consisting of aforementioned keys.

### Example Code
```php
add_filter('osdxp_dashboard_multisite_manage_functionality', 'call_back_f2');
function call_back_f2($example) {
	$example[] = array(
		'title' => 'title',
		'subtitle' => 'subtitle',
		'link' => self_admin_url('options.php'),
		'icon' => 'dashicons-admin-settings',
		'button_text' => 'button text', // For create-type widgets this does not apply
	);
	return $example;
}
```