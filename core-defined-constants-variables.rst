Core Defined Constants & Variables
==================================

The following PHP constants are available across PHPLucidFrame.

+-------------------+------------------------------------------------------------------------------------------+
| Constant          | Description                                                                              |
+===================+==========================================================================================+
| ``_DS_``          | Convenience use of ``DIRECTORY_SEPARATOR``                                               |
+-------------------+------------------------------------------------------------------------------------------+
| ``APP_ROOT``      | File system path to the application’s directory                                          |
+-------------------+------------------------------------------------------------------------------------------+
| ``WEB_ROOT``      | URL to the application root, e.g., ``http://www.example.com`` or                         |
|                   | ``http://localhost/phplucidframe/``                                                      |
+-------------------+------------------------------------------------------------------------------------------+
| ``ROOT``          | File system path to the directory ``root``                                               |
+-------------------+------------------------------------------------------------------------------------------+
| ``INC``           | File system path to the directory ``inc`` in the root directory                          |
+-------------------+------------------------------------------------------------------------------------------+
| ``DB``            | File system path to the directory ``db`` in the root directory                           |
+-------------------+------------------------------------------------------------------------------------------+
| ``LIB``           | File system path to the directory ``lib`` in the root directory                          |
+-------------------+------------------------------------------------------------------------------------------+
| ``HELPER``        | File system path to the directory ``lib/helpers`` in the root directory                  |
+-------------------+------------------------------------------------------------------------------------------+
| ``CLASSES``       | File system path to the directory ``lib/classes`` in the root directory                  |
+-------------------+------------------------------------------------------------------------------------------+
| ``I18N``          | File system path to the directory ``i18n`` in the root directory                         |
+-------------------+------------------------------------------------------------------------------------------+
| ``THIRD_PARTY``   | File system path to the directory ``third-party`` in the root directory                  |
+-------------------+------------------------------------------------------------------------------------------+
| ``VENDOR``        | File system path to the directory ``vendors`` in the root directory                      |
+-------------------+------------------------------------------------------------------------------------------+
| ``FILE``          | File system path to the directory ``files`` in the root directory                        |
+-------------------+------------------------------------------------------------------------------------------+
| ``CACHE``         | File system path to the directory ``cache`` in the root directory                        |
+-------------------+------------------------------------------------------------------------------------------+
| ``ASSETS``        | File system path to the directory ``assets`` in the root directory                       |
+-------------------+------------------------------------------------------------------------------------------+
| ``CSS``           | File system path to the directory ``assets/css`` in the root directory                   |
+-------------------+------------------------------------------------------------------------------------------+
| ``IMAGE``         | File system path to the directory ``assets/images`` in the root directory                |
+-------------------+------------------------------------------------------------------------------------------+
| ``JS``            | File system path to the directory ``assets/js`` in the root directory                    |
+-------------------+------------------------------------------------------------------------------------------+
| ``TEST_DIR``      | File system path to the directory ``tests`` in the root directory                        |
+-------------------+------------------------------------------------------------------------------------------+
| ``LC_NAMESPACE``  | Namespace according to the site directories, for example,                                |
|                   | if you have ``www.example.com/admin``, you may have a namespace ``admin``.               |
+-------------------+------------------------------------------------------------------------------------------+
| ``WEB_VENDOR``    | Web-accessible path to the ``vendors`` directory.,                                       |
|                   | e.g., ``http://www.example.com/vendors/``                                                |
+-------------------+------------------------------------------------------------------------------------------+
| ``HOME``          | The home page URL                                                                        |
+-------------------+------------------------------------------------------------------------------------------+

PHPLucidFrame has a global object in Javascript – ``LC``. The following Javascript global variables of ``LC`` are available to use.

+-------------------+------------------------------------------------------------------------------------------+
| Variable          | Description                                                                              |
+===================+==========================================================================================+
| ``LC.root``       | URL to the application root. It could also be accessible as ``WEB_ROOT`` in PHP.         |
+-------------------+------------------------------------------------------------------------------------------+
| ``LC.self``       | The current URL                                                                          |
+-------------------+------------------------------------------------------------------------------------------+
| ``LC.lang``       | The current language code                                                                |
+-------------------+------------------------------------------------------------------------------------------+
| ``LC.baseURL``    | The sub-directory name if your application is wrapped in. It would be blank              |
|                   | if your application is located in the web server document root.                          |
+-------------------+------------------------------------------------------------------------------------------+
| ``LC.route``      | The current route path                                                                   |
+-------------------+------------------------------------------------------------------------------------------+
| ``LC.cleanRoute`` | The current route path without query string and file name                                |
+-------------------+------------------------------------------------------------------------------------------+
| ``LC.namespace``  | Namespace according to the site directories, for example,                                |
|                   | if you have ``www.example.com/admin``, you may have a namespace ``admin``.               |
+-------------------+------------------------------------------------------------------------------------------+
| ``LC.Page.root``  | The absolute path to the site root including the language code                           |
+-------------------+------------------------------------------------------------------------------------------+

You can also extend any global variable from LC by using a hook ``__script()`` in ``/app/helpers/utility_helper.php``. ::

    <?php
    /**
     * This function is a hook to the core utility function _script()
     */
    function __script(){
    ?>
        LC.cleanURL = <?php echo (int) _cfg('cleanURL'); ?>;
    <?php
    }
    ?>
