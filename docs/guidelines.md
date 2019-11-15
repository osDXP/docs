#Module Guidelines

**Developers working on osDXP modules should follow the guidelines listed below**

* Plugins should register only one settings page, available as a subpage of `Module Settings`

* GPL Compliant Codebase

* Php 7.2 minimum version compatibility

* Detailed docblocks need to be present throughout your codebase

* Plugins must validate against an established ruleset for PHP and Javascript, passing linting tests against these rules.

    * PSR12 is the desired PHPCS ruleset to validate against, though if you're already using the WordPress Coding Standards, you may continue to do so for each osDXP plugin release for the immediate future.

    * Javascript should validate against ES6, at a minimum.

* No SaaS-ification of Client Data(No Customer Data is saved on Vendors System)

* Clear data privacy standards

* Accessibility declaration

* Object Oriented/Class Based PHP.

* Follow osDXP UX Standards

* No "AdWare" (promotional popups, admin notices, or other text taking up screen space)
