Installation
============

You can get a fresh copy of PHPLucidFrame on `the official website <http://www.phplucidframe.com>`_. You can also fork and clone `the GitHub repository <http://github.com/phplucidframe/phplucidframe>`_ using `git <https://git-scm.com>`_. Alternatively, you can install it using Composer with the command::

    composer create-project --prefer-dist phplucidframe/phplucidframe [your-project-name]

Regardless of how you downloaded it, place the code inside of your DocumentRoot.
In **production**, your directory setup may look something like the following so that it can be accessible via ``http://www.example.com`` ::

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

In **development**, you could have a directory name, for example, ``acme``. Then your directory setup may look something like the following so that it can be accessible via ``http://localhost/acme``. ::

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

**Note**: ``acme`` would be your project name.
