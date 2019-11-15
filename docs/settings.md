#Settings Page Registration
osDXP modules should only register one setting page, as a direct child to **Module Settings** page. These can then be categorized via navigation tabs, as documented below.

**Top-level pages** will need to **provide an actionable element** in order for them be accepted. As an example, a module can register a top-level page for a CTA-type element. That is to say, an outputable _custom post type_ that serves as a _call to action_ for your website.

##Filter Definition
The filter `osdxp_add_module_settings_page` should be used in order to register a settings page.

##Filter Parameters

These are based off the WordPress `add_menu_page()` & `add_submenu_page()` functions.

`function` (callable)

The function to be called to output your settings page.

`menu_slug` (string)

The slug name to refer to this menu. It should be unique and only include lowercase alphanumeric, dashes, and underscores characters.

`parent_slug` (string)

The parent slug name in case of a submenu or endpoint.

`page_title` (string)

The text that is displayed in the page’s title when the menu is selected.

`menu_title` (string)

This is the text that is seen in the menu.

`icon_url` (string)

Accepts dashicons class, base64-encoded svg or link to an image file. Check Wordpress documentation for more in-depth information.

`type` (string)

Page type. Accepts `menu`,`submenu` or `endpoint`.

**Endpoint is a hidden sub-page. Use this if you want to link multiple settings pages with navtabs.**

`network` (string)

Network is used if your module needs a Network Admin settings page. You have three options here.

1. Not using the parameter, leaving it out of your code.
You can leave ‘network’ out of your parameters. If you do this, your settings page will only show up in the Dashboard under an individual site or in a single-site install.

2. `both` - Setting `network` to `both` will output your page in both individual Dashboards and under Network Admin. The use case here is you have one settings page with conditionals for network vs individual admin settings.

3. `yes` - Setting `network` to `yes` will output your settings page **only** under Network Admin.

##Filter Return Value
Your function needs to accept a `$pages` parameter of type `array`, in which you can append your page declaration. This variable must be returned by your function.

##Navigation Tabs
If you would like to use navtabs for your settings page, consider using the following markup. The classes used are styled to be inline with osDXP brand identity.
```html
<ul class="osdxp-nav-tabs”>

	<li>page 1</li>

	<li class="active”>active page</li>

</ul>
```

##Example Code Snippet
Below you can find an example declaration of a settings page.
```php
add_filter('osdxp_add_module_settings_page', 'my_settings_page');

function my_settings_page($pages) {
	$pages[] = [
		'function'    => 'my_settings_page_output',
		'menu_slug'   => 'test_plugin',
		'parent_slug' => 'parent_slug', //Default Module Settings page
		'page_title'  => 'My Settings Page',
		'menu_title'  => 'My Settings',
		'type'	      => 'subpage',
		'icon_url'    => 'my-icon.png',
		'network'     => 'both' //Default No
   ];

   return $pages;
}

function my_settings_page_output() {
	echo 'my settings';
}
```
