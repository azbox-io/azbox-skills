# PHP websites: azbox/azbox-php

`azbox/azbox-php` translates whole HTML pages of a PHP website on the fly, using the
translations of an AZbox project. It is not an i18n helper for PHP code; for Laravel
translation files use `azbox-cli` or import/export `lang/*.php` files in the dashboard.

```bash
composer require azbox/azbox-php
```

It is configured with an `api_key` and a `projectId`. Follow the package README and
https://azbox.io/docs/quickstart/php/ for the setup; do not guess option names.
