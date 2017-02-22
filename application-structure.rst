Application Structure
=====================

+---------------+-------------------------------------------------------------------------------------------+
| Directory     | Description                                                                               |
+===============+===========================================================================================+
| app           | This directory structure contains the application files and folders of your site.         |
|               | The directory is auto-bootstrapped with PHPLucidFrame environment.                        |
+---------------+-------------------------------------------------------------------------------------------+
| app/helpers   | The helpers mapping to the system core helpers should be placed in this directory         |
|               | directory. They are auto-loaded. For example, the custom validation helper                |
|               | (``validation_helper.php``) should be placed in this directory and it is auto-loaded      |
|               | across the site. The following helper files are allowed:                                  |
|               |                                                                                           |
|               | * ``auth_helper.php``                                                                     |
|               | * ``db_helper.php``                                                                       |
|               | * ``pager_helper.php``                                                                    |
|               | * ``session_helper.php``                                                                  |
|               | * ``utility_helper.php``                                                                  |
|               | * ``validation_helper.php``                                                               |
+---------------+-------------------------------------------------------------------------------------------+
| app/cmd       | The console command implementation should be placed in this directory. They are           |
|               | auto-loaded. For example, if you implement a custom command file ``GreetCommand.php``     |
|               | it should be placed in this directory and it is auto-loaded across the site.              |
+---------------+-------------------------------------------------------------------------------------------+
| app/entity    | This directory should be used to place the files which contains the business log          |
|               | functions or classes. They usually do the direct operations to the database layer.        |
+---------------+-------------------------------------------------------------------------------------------+
| app/inc       | The directory can include the site template files and site configuration file.            |
|               |                                                                                           |
|               | * ``/site.config.php`` (inherited by ``/inc/site.config.php``)                            |
|               | * ``/tpl/head.php`` (overridable by ``/inc/tpl/head.php``)                                |
|               | * ``/tpl/401.php`` (overridable by ``/inc/tpl/401.php``)                                  |
|               | * ``/tpl/403.php`` (overridable by ``/inc/tpl/403.php``)                                  |
|               | * ``/tpl/404.php`` (overridable by ``/inc/tpl/404.php``)                                  |
|               | * ``/tpl/header.php`` (overridable by ``/inc/tpl/header.php`` if you have)                |
|               | * ``/tpl/footer.php`` (overridable by ``/inc/tpl/footer.php`` if you have)                |
+---------------+-------------------------------------------------------------------------------------------+
| app/js        | The application javascript files should be placed in this directory.                      |
+---------------+-------------------------------------------------------------------------------------------+
| assets        | This directory contains all client resources such css, images, and js.                    |
+---------------+-------------------------------------------------------------------------------------------+
| assets/css    | This directory contains the application CSS files.                                        |
+---------------+-------------------------------------------------------------------------------------------+
| assets/images | This directory contains the images of the application.                                    |
+---------------+-------------------------------------------------------------------------------------------+
| assets/js     | This directory contains the system core javascript files which should not be hacked.      |
|               | Your application javascript files should be placed in ``/app/js``.                        |
+---------------+-------------------------------------------------------------------------------------------+
| business      | **[Deprecated]** This directory is deprecated and it is replaced by the directory         |
|               | ``/app/entity``                                                                           |
+---------------+-------------------------------------------------------------------------------------------+
| db            | This directory contains the database-related stuffs such as schema files,                 |
|               | seeding files, etc.                                                                       |
+---------------+-------------------------------------------------------------------------------------------+
| db/build      | This directory has the built schema definitions and it is ignored from version control.   |
+---------------+-------------------------------------------------------------------------------------------+
| db/generated  | This directory has the generated sql dump files and it is ignored from version control.   |
+---------------+-------------------------------------------------------------------------------------------+
| files         | This directory contains the files and folders of your site uploaded data. For example,    |
|               | sessions, photos, cache, etc.                                                             |
+---------------+-------------------------------------------------------------------------------------------+
| i18n          | This directory should be used to place ``.po`` language files, for example,               |
|               |                                                                                           |
|               | * ``en.po``                                                                               |
|               | * ``zh-CN.po``                                                                            |
+---------------+-------------------------------------------------------------------------------------------+
| i18n/ctn      | This directory should contain sub-directories to place custom multi-lingual content files.|
|               | For example,                                                                              |
|               |                                                                                           |
|               | * ``en/``                                                                                 |
|               |       * ``about-us.en``                                                                   |
|               |       * ``privacy-policy.en``                                                             |
|               | * ``zh-CN/``                                                                              |
|               |       * ``about-us.zh-CN``                                                                |
|               |       * ``privacy-policy.zh-CN``                                                          |
+---------------+-------------------------------------------------------------------------------------------+
| inc           | This directory structure contains the settings and configuration files of the application.|
|               | The following files are overwritable or inherited by the ``app/inc`` or                   |
|               | ``app/subsite/inc`` directory.                                                            |
|               |                                                                                           |
|               | * ``/site.config.php`` (inherited by ``/app/inc/site.config.php``)                        |
|               | * ``/tpl/head.php`` (overridable by ``/app/inc/tpl/head.php``)                            |
|               | * ``/tpl/401.php`` (overridable by ``/app/inc/tpl/401.php``)                              |
|               | * ``/tpl/403.php`` (overridable by ``/app/inc/tpl/403.php``)                              |
|               | * ``/tpl/404.php`` (overridable by ``/app/inc/tpl/404.php``)                              |
|               | * ``/tpl/header.php`` (overridable by ``/app/inc/tpl/header.php`` if you have)            |
|               | * ``/tpl/footer.php`` (overridable by ``/app/inc/tpl/footer.php`` if you have)            |
+---------------+-------------------------------------------------------------------------------------------+
| lib           | This directory is reserved for core library files. Custom and overwritten helpers should  |
|               | be placed in their own subdirectory of the ``app/helpers`` or ``app/{subsite}/helpers``   |
|               | directory sessions, photos, cache, etc.                                                   |
+---------------+-------------------------------------------------------------------------------------------+
| tests         | This directory should contain all test files. The directory is auto-bootstrapped with     |
|               | PHPLucidFrame environment.                                                                |
+---------------+-------------------------------------------------------------------------------------------+
| vendors       | This directory should be used to place downloaded and custom modules and third party      |
|               | libraries which are common to all sites.                                                  |
+---------------+-------------------------------------------------------------------------------------------+
