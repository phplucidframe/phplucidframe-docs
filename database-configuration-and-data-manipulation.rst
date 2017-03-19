Database Configuration and Data Manipulation
============================================

You can configure your database settings in three files according to your deployment environments:

- ``/inc/parameter/development.php`` for development environment
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

If you enable ``$lc_useDBAutoFields`` in ``/inc/config.php``, each of tables in your database should have the following four fields:

- ``slug varchar(xxx)``
- ``created datetime``
- ``updated datetime``
- ``deleted datetime``

Nonetheless, you don’t need to worry about them if you write your own custom queries. They are also flaggable when you use more handy functions like ``db_insert()`` or ``db_update()``.

.. note:: As of version 1.14, Schema Manager will manage those slug and timestamp fields for you. Just ignore ``$lc_useDBAutoFields``.

Retrieving Your Data
--------------------

If you know how to write SQL language, you can write your own queries for create, read, update, delete using db helper functions. You can use the following function to retrieve your data:

- `db_query() <http://www.phplucidframe.com/api-doc/latest/function-db_query.html>`_ which reflects to `mysqli_query() <http://php.net/manual/en/mysqli.query.php>`_.
- `db_fetchAssoc() <http://www.phplucidframe.com/api-doc/latest/function-db_fetchassoc.html>`_ which reflects to `mysqli_fetch_assoc() <http://php.net/manual/en/mysqli.fetch-assoc.php>`_.
- `db_fetchArray() <http://www.phplucidframe.com/api-doc/latest/function-db_fetcharray.html>`_ which reflects to `mysqli_fetch_array() <http://php.net/manual/en/mysqli.fetch-array.php>`_.
- `db_fetchObject() <http://www.phplucidframe.com/api-doc/latest/function-db_fetchobject.html>`_ which reflects to `mysqli_fetch_object() <http://php.net/manual/en/mysqli.fetch-object.php>`_.
- `db_fetch() <http://www.phplucidframe.com/api-doc/latest/function-db_fetch.html>`_ which retrieves the only first field data
- `db_fetchResult() <http://www.phplucidframe.com/api-doc/latest/function-db_fetchresult.html>`_ which retrieves the one result in object.
- `db_extract() <http://www.phplucidframe.com/api-doc/latest/function-db_extract.html>`_ which retrieves the result in array or array of objects.

.. note:: Instead of using native query to fetch data, QueryBuilder is recommended. See `Query Builder <#query-builder>`_ usage.

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

- `db_insertId() <http://www.phplucidframe.com/api-doc/latest/function-db_insertid.html>`_ which reflects to `mysqli_insert_id() <http://php.net/manual/en/mysqli.insert-id.php>`_.
- `db_insertSlug() <http://www.phplucidframe.com/api-doc/latest/function-db_insertslug.html>`_ returns the generated slug used in the last query.

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

You can provide the third or fourth parameter ``$condition``. See `Query Conditions <#query-conditions>`_. ::

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

``db_delete_multi()` is useful for batch record deletion for the given condition, but it does not check foreign key constraints. ::

    db_delete_multi('table_name', $condition=array(
        'fieldName1'    => $value1,
        'fieldName2 >=' => $value2,
        'fieldName3'    => null,
    ))

See next section for `query conditions <#query-conditions>`_ with ``db_delete()`` and ``db_delete_multi()``.
