Installation
============

You can get a fresh copy of PHPLucidFrame on `the official website <http://www.phplucidframe.com>`_ or from `the github repository <http://www.phplucidframe.com/download/release/latest>`_.

#. Extract the downloaded archive in your local webserver document root, and you will get a folder named **phplucidframe-x.y.z** where **x.y.z** would be your downloaded version.
#. Rename it as **acme** (the name is up to you).
#. Change ``baseURL`` to ``acme`` in ``/inc/parameter/development.php``.
#. Check http://localhost/acme in your browser.

Alternatively, you can install PHPLucidFrame using `Composer <http://getcomposer.org>`_. Open your terminal and CD to your webserver document root, and then run ::

    composer create-project --prefer-dist phplucidframe/phplucidframe acme

Development Environment
-----------------------

In **development**, your directory setup may look something like the following structure so that it can be accessible via ``http://localhost/acme``. ::

    /path_to_webserver_document_root
        /acme
            /app
            /assets
            /db
            /files
            /i18n
            /inc
            /lib
            /tests
            /vendor
            .htaccess
            index.php

In this case, the configuration variable ``baseURL`` in ``/inc/parameter/development.php`` should look like this: ::

    return array(
        # No trailing slash (only if it is located in a sub-directory of the document root)
        # Leave blank if it is located in the document root
        'baseURL' => 'acme',
        // ....
    );

.. note::
    ``acme`` would be your project or app name.

Production Environment
----------------------

In **production**, your directory setup may look something like the following structure so that it can be accessible via ``http://www.example.com`` ::

    /path_to_webserver_document_root
        /app
        /assets
        /db
        /files
        /i18n
        /inc
        /lib
        /tests
        /vendor
        index.php

In this case, the configuration variable ``baseURL`` in ``/inc/parameter/production.php`` should look like this: ::

    return array(
        # No trailing slash (only if it is located in a sub-directory of the document root)
        # Leave blank if it is located in the document root
        'baseURL' => '',
        // ....
    );

By default, development environment is active. You can make production environment active by running the following command in your terminal. ::

    php lucidframe env production

If you don't want to use or can't use command line, you can save the file ``.lcenv`` (in the project root) with the content ``production``.

.. note::
    You can check the currently active environment by running the command ``php lucidframe env --show``.

Secret key
----------

To generate a secret key for your application, open your terminal or command line and CD to your project root, and then run ``php lucidframe secret:generate``. For more about the PHPLucidFrame console, read `the documentation section "The LucidFrame Console" <http://www.phplucidframe.com/download/doc/latest>`_.

Alternatively, you can also get a secret key from http://phplucidframe.com/secret-generator and save it in ``/inc/.secret`` without using command line.