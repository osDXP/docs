#Modifying the Manage License Button
The **Installed Modules** page will output a `Manage License` link on the `Status` column. The URL linked to this will be pulled from the `Author URI` key available in your plugins header. Below you can find information on modifying the outputted buttons text.

##Filter Definition
The filter that should be used is in the form of `osdxp_manage_button_module_{$SLUG}`, where `{$SLUG}` is your modules folder or main plugin file, in sluggified format.

For example, a plugin called `My First Plugin` with its main plugin file located at `/wp-content/plugins/my-first-plugin/my-first-plugin.php` will have its slug as `my-first-plugin` and its filter will be `osdxp_manage_button_module_my-first-plugin`.

You can use this filter to change the text output on the installed modules page

##Filter Return Value
The function hooked in the `osdxp_manage_button_module_{$SLUG}` should return a `string`.

##Example Code
Below you can find an example implementation of the `osdxp_manage_button_module_{$SLUG}` filter, using a plugin called `my-plugin-example`.
```php
add_filter(
'osdxp_manage_button_module_my-plugin-example',
function ()
{
	//Will output "View Website" instead of the default "Manage License"
	return 'View Website';
}
);
```
