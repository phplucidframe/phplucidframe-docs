Database Configuration and Data Manipulation
============================================

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
                    'username'  => 'your_prod_user',
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

Retrieving Your Data
--------------------

PHPLucidFrame provides several db helper functions to retrieve data from your database. You can use the following functions to retrieve your data:

- `db_query() <http://www.phplucidframe.com/api-doc/latest/function-db_query.html>`_ which reflects to `mysqli_query() <http://php-.net/manual/en/mysqli.query.php>`_.
- `db_fetchAssoc() <http://www.phplucidframe.com/api-doc/latest/function-db_fetcAassoc.html>`_ which reflects to `mysqli_fetch_assoc() <http://php.net/manual/en/mysqli-result.fetch-assoc.php>`_.
- `db_fetchArray() <http://www.phplucidframe.com/api-doc/latest/function-db_fetchArray.html>`_ which reflects to `mysqli_fetch_array() <http://php.net/manual/en/mysqli-result.fetch-array.php>`_.
- `db_fetchObject() <http://www.phplucidframe.com/api-doc/latest/function-db_fetchObject.html>`_ which reflects to `mysqli_fetch_object() <http://php.net/manual/en/mysqli-result.fetch-object.php>`_.
- `db_fetch() <http://www.phplucidframe.com/api-doc/latest/function-db_fetch.html>`_ which retrieves the only first field data
- `db_fetchResult() <http://www.phplucidframe.com/api-doc/latest/function-db_fetchResult.html>`_ which retrieves the one result in object.
- `db_extract() <http://www.phplucidframe.com/api-doc/latest/function-db_extract.html>`_ which retrieves the result in array or array of objects.

.. note:: Instead of using native query to fetch data, QueryBuilder is recommended. See `Query Builder <query-builder>`_ usage.

The following is an example to retrieve multiple result rows: ::

    $sql = 'SELECT * FROM '.db_table('post').' ORDER BY postName';
    if ($result = db_query($sql)){
        while($row = db_fetchAssoc($result)){
            // do somethings here...
        }
    }

    // Extract all data into an array of objects
    // db_extract() invokes db_fetchObject() by default internally
    $sql = 'SELECT * FROM '.db_table('post').' ORDER BY postName';
    $posts = db_extract($sql); // The second or third argument can be given one of these: LC_FETCH_OBJECT (default), LC_FETCH_ASSOC, LC_FETCH_ARRAY
    _pr($posts);

    // Extract all data into key/value pair of array
    $sql = 'SELECT postId key, postTitle value FROM '.db_table('post).' ORDER BY postName';
    $posts = db_extract($sql);
    _pr($posts);
    /*
    array(
      $postId => $postTitle
    )
    */

The following is an example to retrieve a single result: ::

    // Retrieving a single-row result
    $sql = 'SELECT * FROM '.db_table('post').' WHERE postId = :id';
    if ($post = db_fetchResult($sql, array(':id'=>$id))) {
        _pr($post);
        // $post->postId;
        // $post->postTitle;
    }

    // Retrieving the result count
    $sql = 'SELECT COUNT(*) FROM '.db_table('post');
    $count = db_count($sql);

    // Retrieving a field
    $sql = 'SELECT MAX(postId) FROM '.db_table('post');
    $max = db_fetch($sql);

Inserting Your Data
-------------------

``db_insert()`` will save you when you are trying to insert your data into the database without writing ``INSERT`` statement. The syntax is ``db_insert('table_name', $data=array(), $useSlug=true)``. For example, ::

    $success = db_insert('post', array(
        'postTitle' => 'New Title', // this will be used for the slug field while third argument is true
        'postBody' => 'Post complete description here',
    ));

    if ($success) {
        // do something with db_insertId() or db_insertSlug()
    }

You can also provide a custom slug in the ``$data`` array. ::

    $slug = 'your-custom-slug-string';
    $success = db_insert('post', array(
        'slug' => $slug,
        'postTitle' => 'Updated Title',
        'postBody' => 'Updated post complete description here'
    ));

- `db_insertId() <http://www.phplucidframe.com/api-doc/latest/function-db_insertId.html>`_ which reflects to `mysqli_insert_id() <http://php.net/manual/en/mysqli.insert-id.php>`_.
- `db_insertSlug() <http://www.phplucidframe.com/api-doc/latest/function-db_insertSlug.html>`_ returns the generated slug used in the last query.

.. note::
    - The first field in data array will be used to insert into the slug field.
    - Table prefix to the table name of the first parameter is optional.


Updating Your Data
------------------

``db_update()`` is a convenience method for your SQL ``UPDATE`` operation. The syntax is ``db_update('table_name', $data=array(), $useSlug=true, $condition=NULL)``. For example, ::

    $success = db_update('post', array(
        'postId'    => 1, // this first data value must be the record ID to be updated
        'postTitle' => 'Updated Title', // this will be used for the slug field while third parameter is true
        'postBody'  => 'Updated post complete description here'
    ));
    // UPDATE post SET
    //   slug = "updated-title",
    //   postTitle = "Updated Title",
    //   postBody = "Updated post complete description here
    //   updated = "....."
    // WHERE postId = 1

    if($success){
        // do something
    }

You can also provide a custom slug in the `$data`` array. ::

    $slug = 'your-custom-slug-string';
    $success = db_update('post', array(
        'postId'    => $updateID, // this first data value must be the record id to be updated
        'slug'      => $slug, // providing custom slug string
        'postTitle' => 'Updated Title',
        'postBody'  => 'Updated post complete description here'
    ));

You can provide the third or fourth parameter ``$condition``. See `Query Conditions <#id2>`_. ::

    $condition = array(
        'fieldName1'    => 'value1',
        'fieldName2 !=' => 'value2',
        'fieldName2 >'  => 'value3',
    );

Deleting Your Data
------------------

``db_delete()`` is a handy method for your SQL ``DELETE`` operation. This is only applicable for single record deletion. The syntax is ``db_delete('table_name', $condition=null)``. LucidFrame encourages MYSQL Foreign Key Constraints to use. If ``ON DELETE RESTRICT`` is found, it performs soft delete (logical delete) by updating the current date/time into the field ``deleted``, otherwise it performs hard delete (physical delete). ::

    if (db_delete('post', array('postId' => $idToDelete))) {
        $success = true;
    }

``db_delete_multi()`` is useful for batch record deletion for the given condition, but it does not check foreign key constraints. ::

    db_delete_multi('table_name', $condition=array(
        'fieldName1'    => $value1,
        'fieldName2 >=' => $value2,
        'fieldName3'    => null,
    ))

See next section for `query conditions <#id2>`_ with ``db_delete()`` and ``db_delete_multi()``.

Query Conditions
----------------

You can provide a condition array to third or fourth parameter to ``db_update()`` and second parameter to ``db_delete()`` or ``db_delete_multi()``. You can also use ``db_and()`` and ``db_or()``. The following are some examples.

Updating with simple condition: ::

    db_update('post', array(
        'postTitle' => 'Updated Title',
    ), array(
        'postId' => 1
    ));
    // UPDATE post SET
    //   slug = "updated-title",
    //   postTitle = "Updated Title",
    //   updated = "....."
    // WHERE postId = 1

Updating using AND condition: ::

    db_update('post', array(
            'catId' => 1,
        ),
        false, // slug field is not updated
        db_and(array(
            'id' => 1,
            'delete !=' => NULL
        ))
    );
    // UPDATE post SET
    //   catId = 1,
    //   updated = "....."
    // WHERE id = 1 AND deleted IS NOT NULL

Updating using IN condition: ::

    db_update('post', array(
            'catId' => 1,
        ),
        false, // slug field is not updated
        array(
            'postId' => array(1, 2, 3)
        ))
    );
    // UPDATE post SET
    //   catId = 1,
    //   updated = "....."
    // WHERE postId IN (1, 2, 3)

Updating using OR condition: ::

    db_update('post', array(
        'catId' => 1,
        ),
        false, // slug field is not updated,
        db_or(
            array('postId' => 1),
            array('postId' => 2)
        )
    );
    // UPDATE post SET
    //   catId = 1,
    //   updated = "....."
    // WHERE postId = 1 OR postId = 2

Updating using IN and OR condition: ::

    db_update('post', array(
            'catId' => 1,
        ),
        false, // slug field is not updated
        db_or(array(
            'id' => array(1, 2, 3),
            'id >' => 10,
        ))
    );
    // UPDATE post SET
    //   catId = 1,
    //   updated = "....."
    // WHERE id IN (1, 2, 3) OR id > 10

Updating with complex AND/OR condition: ::

    db_update('post', array(
            'catId' => 1,
        ),
        false, // slug field is not updated
        db_and(array(
            'postTitle' => 'a project',
            'catId' => 2,
            db_or(array(
                'id' => array(1, 2, 3),
                'id >=' => 10,
            ))
        ))
    );
    // UPDATE post SET
    //   catId = 1,
    //   updated = "....."
    // WHERE postTitle = "a project"
    // AND catId= 2
    // AND ( id IN (1, 2, 3) OR id >= 10 )

Condition Operators
-------------------

+---------------+-----------------------------------------------+---------------------------------------------+
| Operator      | Usage Example                                 | Equivalent SQL Condition                    |
+===============+===============================================+=============================================+
| ``=``         | ``array('postId' => 1)``                      | ``WHERE postId = 1``                        |
|               | ``array('postId' => array(1, 2, 3))``         | ``WHERE postId IN (1, 2, 3)``               |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``!=``        | ``array('postId !=' => 1)``                   | ``WHERE postId != 1``                       |
|               | ``array('postId !=' => array(1, 2, 3))``      | ``WHERE postId NOT IN (1, 2, 3)``           |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``>``         | ``array('postId >' => 1)``                    | ``WHERE postId > 1``                        |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``>=``        | ``array('postId >=' => 1)``                   | ``WHERE postId >= 1``                       |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``<``         | ``array('postId <' => 1)``                    | ``WHERE postId < 1``                        |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``<=``        | ``array('postId <=' => 1)``                   | ``WHERE postId <= 1``                       |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``between``   | ``array('postId between' => array(1, 10))``   | ``WHERE postId BETWEEN 1 and 10``           |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``nbetween``  | ``array('postId nbetween' => array(1, 10))``  | ``WHERE postId NOT BETWEEN 1 and 10``       |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``like``      | ``array('postTitle like' => 'a project')``    | ``WHERE postTitle LIKE "%a project%"``      |
| ``like%%``    | ``array('postTitle like%%' => 'a project')``  |                                             |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``like%~``    | ``array('postTitle like%~' => 'a project')``  | ``WHERE postTitle LIKE "%a project"``       |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``like~%``    | ``array('postTitle like~%' => 'a project')``  | ``WHERE postTitle LIKE "a project%"``       |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``nlike``     | ``array('postTitle nlike' => 'a project')``   | ``WHERE postTitle NOT LIKE "%a project%"``  |
| ``nlike%%``   | ``array('postTitle nlike%%' => 'a project')`` |                                             |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``nlike%~``   | ``array('postTitle nlike%~' => 'a project')`` | ``WHERE postTitle NOT LIKE "%a project"``   |
+---------------+-----------------------------------------------+---------------------------------------------+
| ``nlike~%``   | ``array('postTitle nlike~%' => 'a project')`` | ``WHERE postTitle NOT LIKE "a project%"``   |
+---------------+-----------------------------------------------+---------------------------------------------+

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
