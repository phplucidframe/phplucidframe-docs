Database Configuration
======================

You can configure your database settings in three files according to your deployment environments:

- ``/inc/parameter/development.php`` for development environment
- ``/inc/parameter/staging.php`` for staging environment
- ``/inc/parameter/production.php`` for production environment
- ``/inc/parameter/test.php`` for test environment

::

    return array(
        // ...
        # Database connection information
        'db' => array(
            'default' => array(
                'engine'    => 'mysql', // database engine
                'host'      => 'localhost', // database host
                'port'      => '', // database port
                'database'  => 'lucid_blog', // database name
                'username'  => 'root', // database username
                'password'  => '', // database password
                'prefix'    => '', // table name prefix
                'collation' => 'utf8_unicode_ci' // database collation
            )
        )
        // ...
    );

Make Your Credentials Secret
----------------------------

As of version 2.0, PHPLucidFrame includes a file ``/inc/parameter/parameter.env.example.inc``. You can copy and rename it to ``parameter.env.inc`` which is already ignored from version control. So, in the file, you can define your important information that needs to be secret and to not share with others. For example, you can define your production database credentials in ``/inc/parameter/parameter.env.inc`` like below:

::

    return array(
        'prod' => array( # either prod or production as you like
            'db' => array(
                'default' => array(
                    'database'  => 'your_prod_db',
                    'username'  => 'your_prod_username',
                    'password'  => 'your_prod_pwd',
                    'prefix'    => '',
                )
            )
        )
    );

then, you can call those parameters from ``/inc/parameter/production.php`` using ``_env()``.

::

    return array(
        // ...
        # Database connection information
        'db' => array(
            'default' => array(
                'engine'    => 'mysql', // database engine
                'host'      => 'localhost', // database host
                'port'      => '', // database port
                'database'  => _env('prod.db.default.database')
                'username'  => _env('prod.db.default.username')
                'password'  => _env('prod.db.default.password')
                'prefix'    => _env('prod.db.default.prefix')
                'collation' => 'utf8_unicode_ci' // database collation
            )
        )
        // ...
    );

Connecting to Multiple Databases
--------------------------------

Sometimes, we need to connect multiple databases in our app. . In ``/inc/config.php`` (copy of ``/inc/config.default.php``), ``$lc_databases`` is an array composed of multiple database connection strings. Here’s the default syntax, specifying a single connection: ::

    $lc_databases = array(
        'default' => array( // default database; you could also have other database settings here
                'engine'    => _p('db.default.engine'),
                'host'      => _p('db.default.host'),
                'port'      => _p('db.default.port'),
                'database'  => _p('db.default.database'),
                'username'  => _p('db.default.username'),
                'password'  => _p('db.default.password'),
                'prefix'    => _p('db.default.prefix'),
                'collation' => _p('db.default.collation')
        )
    );

As an example, you might have two databases, the default database and a legacy database and the syntax would be as below: ::

    $lc_databases = array(
        'default' => array( // default database; you could also have other database settings here
                'engine'    => _p('db.default.engine'),
                'host'      => _p('db.default.host'),
                'port'      => _p('db.default.port'),
                'database'  => _p('db.default.database'),
                'username'  => _p('db.default.username'),
                'password'  => _p('db.default.password'),
                'prefix'    => _p('db.default.prefix'),
                'collation' => _p('db.default.collation')
        )
        'legacy' => array(
                'engine'    => _p('db.legacy.engine'),
                'host'      => _p('db.legacy.host'),
                'port'      => _p('db.legacy.port'),
                'database'  => _p('db.legacy.database'),
                'username'  => _p('db.legacy.username'),
                'password'  => _p('db.legacy.password'),
                'prefix'    => _p('db.legacy.prefix'),
                'collation' => _p('db.legacy.collation')
        )
    );

The next step is to define the parameters in ``/inc/parameter/development.php`` or ``/inc/parameter/production.php`` for your two databases in the configuration db. Here is any example. ::

    return array(
        // ...
        # Database connection information
        'db' => array(
            'default' => array(
                'engine'    => 'mysql', // database engine
                'host'      => 'localhost', // database host
                'port'      => '', // database port
                'database'  => 'lucid_blog', // database name
                'username'  => 'yourusername', // database username
                'password'  => 'yourpassword', // database password
                'prefix'    => '', // table name prefix
                'collation' => 'utf8_general_ci' // database collation
            ),
            'legacy' => array(
                'engine'    => 'mysql',
                'host'      => 'localhost',
                'port'      => '',
                'database'  => 'legacy_db',
                'username'  => 'legacyusername',
                'password'  => 'legacypassword',
                'prefix'    => '', // table name prefix
                'collation' => 'utf8_general_ci'
            )
        ),
        // ...
    );

When you need to connect to one of the other databases, you activate it by its key name and switch back to the default connection when finished: ::

    # Get some information from the legacy database.
    db_switch('legacy');
    # Fetching data from the `user` table of the legacy database
    $result = db_select('user')
        ->where('uid', $uid)
        ->getSingleResult()

    # Switch back to the default connection when finished.
    db_switch(); // or db_switch('default');

Database Session
----------------

Since version 1.5, PHPLucidFrame supports database session management. It is useful when your site is set up with load balancer that distributes workloads across multiple resources. Here’s the minimum table schema requirement for database session. ::

    CREATE TABLE `lc_sessions` (
        `sid` varchar(64) NOT NULL DEFAULT '',
        `host` varchar(128) NOT NULL DEFAULT '',
        `timestamp` int(11) unsigned DEFAULT NULL,
        `session` longblob NOT NULL DEFAULT '',
        `useragent` varchar(255) NOT NULL DEFAULT '',
        PRIMARY KEY (`sid`)
    );

Once you have the table created, you just need to configure ``$lc_session['type'] = 'database'`` in ``/inc/config.php`` (copy of ``/inc/config.default.php``) such as ::

    $lc_session = array(
        'type' => 'database',
        'options' => array(
            /* you can configure more options here, see the comments in /inc/config.default.php */
        )
    );