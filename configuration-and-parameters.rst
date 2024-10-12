Configuration & Parameters
==========================

There are a couple of configuration files in PHPLucidFrame. These configuration files allow you to configure things like your database connection information, your mail server information, as well as various other core configuration values.

+-----+----------------------------------+------------------------------------------------------------------------------+
|     | File                             | Description                                                                  |
+=====+==================================+==============================================================================+
| 1   | /inc/config.php                  | The core configuration settings (copy of ``config.default.php``)             |
+-----+----------------------------------+------------------------------------------------------------------------------+
| 2   | /inc/route.config.php            | Configuration for routes                                                     |
+-----+----------------------------------+------------------------------------------------------------------------------+
| 3   | /inc/constants.php               | The core global constants                                                    |
+-----+----------------------------------+------------------------------------------------------------------------------+
| 4   | /inc/parameter/development.php   | The configuration settings for development environment                       |
+-----+----------------------------------+------------------------------------------------------------------------------+
| 5   | /inc/parameter/production.php    | The configuration settings for production environment                        |
+-----+----------------------------------+------------------------------------------------------------------------------+
| 6   | /inc/parameter/staging.php       | The configuration settings for staging environment                           |
+-----+----------------------------------+------------------------------------------------------------------------------+
| 7   | /inc/parameter/test.php          | The configuration settings for testing environment                           |
+-----+----------------------------------+------------------------------------------------------------------------------+
| 8   | /inc/parameter/.env.inc          | The configuration settings what would be excluded from your version Control  |
|     |                                  | (copy of ``/inc/parameter/.env.example.inc``)                                |
+-----+----------------------------------+------------------------------------------------------------------------------+
| 9   | /app/inc/site.config.php         | The custom app-specific configuration settings and the configration settings |
|     |                                  | from ``/inc/config.php`` can also be overidden here                          |
+-----+----------------------------------+------------------------------------------------------------------------------+
| 10  | /app/inc/route.config.php        | The custom app-specific route configuration settings                         |
+-----+----------------------------------+------------------------------------------------------------------------------+
| 11  | /app/inc/constants.php           | The app-specific constants                                                   |
+-----+----------------------------------+------------------------------------------------------------------------------+

There may be additional configuration files if you have sub-sites or modules in your application. Let's say, if you have admin module in your application, there could be the following configuration files in the admin directory.

#. ``/app/admin/inc/site.config.php``
#. ``/app/admin/inc/route.config.php``
#. ``/app/admin/inc/constants.php``

+-----+---------------------------------+-------------------------------------------------------+
|     | File                            | Description                                           |
+=====+=================================+=======================================================+
| 1   | /app/inc/site.config.php        | The configuration settings for admin and the settings |
|     |                                 | from ``/inc/config.php`` can also be overidden here   |
+-----+---------------------------------+-------------------------------------------------------+
| 2   | /app/admin/inc/route.config.php | You can define admin routes here                      |
+-----+---------------------------------+-------------------------------------------------------+
| 3   | /app/admin/inc/constants.php    | You can define constants for admin here               |
+-----+---------------------------------+-------------------------------------------------------+

Note that it works only when you define "admin" in ``$lc_sites`` in ``/inc/config.php``. ::

    $lc_sites = array(
        /* 'virtual_folder_name (namespace)'  => 'path/to/physical_folder_name_directly_under_app_directory' */
        'admin' => 'admin'
    );

Let's say, you have http://example.com and http://example.com/admin

When you access http://example.com, these config files are included in the following priority:

#. /inc/config.php
#. /app/inc/site.config.php

When you access http://example.com/admin, these config files are included in the following priority:

#. /inc/config.php
#. /app/inc/site.config.php
#. /app/admin/inc/site.config.php

When you access http://example.com, these route config files are included in the following priority:

#. /inc/route.config.php
#. /app/inc/route.config.php

When you access http://example.com/admin, these config files are included in the following priority:

#. /inc/route.config.php
#. /app/inc/route.config.php
#. /app/admin/inc/route.config.php

When you access http://example.com, these constant files are included in the following priority:

#. /inc/constants.php
#. /app/inc/constants.php

When you access http://example.com/admin, these constant files are included in the following priority:

#. /inc/constants.php
#. /app/inc/constants.php
#. /app/admin/inc/constants.php
