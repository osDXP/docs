#Conditionally load osDXP logic

_If conditionally loading osDXP in your **premium** plugin, consider having a separate type of serial number for osDXP-only content, thus having osDXP functionality be conditionally loaded only for a specific type of serial numbers._

##Constants availability
There are a number of constants defined in **_the main osDXP Dashboard Plugin file_** that you can check for in order to conditionally load your osDXP-speciffic logic. Below are two common examples and their usage.

 * `OSDXP_DASHBOARD_FILE` - the main osDXP Dashboard Plugin file
 * `OSDXP_DASHBOARD_VER` - osDXP Dashboard Plugin Version. This can be used to enforce minimum version compatibility

##Code Example
Below you can find an example of conditionally loading osDXP logic based on constant availability and minimum version compatibility.
```php
//Check for osDXP availability
if (defined('OSDXP_DASHBOARD_VER')) {
    //Check for minimum required version
    if ('1.0.3' >= OSDXP_DASHBOARD_VER) {
    	//Loads osDXP-speciffic file.
	    require_once 'dxp.php';
    } else {
    	//Gracefully fail with a notice.
    }
} else {
	//Gracefully fail with a notice.
}
```
