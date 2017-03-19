File Inclusion
==============

PHPLucidFrame helps you to include files more easier. You can use ``_i()`` for PHP files, ``_js()`` for Javascript files and ``_css()`` for CSS files. The ``_i()`` is returning the system file path and it has to be used with the PHP built-in functions include and require. The ``_js()`` and ``_css()`` will look for the files in the directory ``/assets/css/`` and ``/assets/js/`` and include them automatically.

All of three functions will operate based on the configuration variable ``$lc_sites`` in ``/inc/config.php``. They will look for the files from the most specific directory to the least.  For example, if you use ``include(_i('inc/tpl/head.php'))``, it will look for the files as follow and it will stop where the file is found.

1. ``/app/inc/tpl/head.php``
2. ``/inc/tpl/head.php``

Another example is that if you have a directory ``/app/admin/`` configured in ``$lc_sites`` as follow: ::

    # $lc_sites: consider sub-directories as additional site roots and namespaces
    /**
     * ### Syntax
     *  array(
     *   'virtual_folder_name (namespace)'  => 'physical_folder_name_directly_under_app_directory'
     *  )
     *  For example, if you have the configuration `array('admin' => 'admin')` here, you let LucidFrame know to include the files
     * from those directories below without specifying the directory name explicitly in every include:
     *   /app/admin/assets/css
     *   /app/admin/assets/js
     *   /app/admin/inc
     *   /app/admin/helpers
     * you could also set 'lc-admin' => 'admin', then you can access http://localhost/phplucidframe/lc-admin
     * Leave this an empty array if you don't want this feature
     * @see https://github.com/phplucidframe/phplucidframe/wiki/Configuration-for-The-Sample-Administration
    -Module
     */
    $lc_sites = array(
        'admin' => 'admin',
    );

then, PHPLucidFrame will look for the file:

1. ``/app/admin/inc/tpl/head.php``
2. ``/app/inc/tpl/head.php``
3. ``/inc/tpl/head.php``

For ``js()`` and ``_css()``, you don’t need to include the directory path as it looks for the files in the ``/assets/js/`` and ``/assets/css/``  folders and prints out ``<script>`` and ``<link />`` respectively if they found the files. There are two system provided directories - ``/assets/js/`` and ``/assets/css/`` under the root. Let’s say you also have those two directories in other sub-directories as below: ::

    /path_to_webserver_document_root
       /app
        |-- /admin
        |    |-- /assets
        |    |    |-- /css
        |    |    |-- /js
        |-- /assets
        |    |-- /css
        |    |-- /js
       /assets
        |-- /css
        |-- /js

When you use ``_js('site.js')`` and if you are at admin, it will look for the file as the following priority and it will stop where the file is found.

1. ``/app/admin/assets/js/site.js``
2. ``/app/assets/js/site.js``
3. ``/assets/js/site.js``

It is same processing for the usage of ``_css('base.css')``:

1. ``/app/admin/assets/css/base.css``
2. ``/app/assets/css/base.css``
3. ``/assets/css/base.css``
