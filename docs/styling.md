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
