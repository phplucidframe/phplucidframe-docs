Working With Data
=================

To insert, update, delete data, PHPLucidFrame provides the helper functions - ``db_insert()``, ``db_update()``, ``db_delete()`` and ``db_delete_multi()``.

Inserting Your Data
-------------------

``db_insert()`` will save you when you are trying to insert your data into the database without writing ``INSERT`` statement. The syntax is ::

    db_insert('table_name', $data = array(), $useSlug = true)

For example, ::

    $success = db_insert('post', array(
        'title' => 'New Title', // this will be used for the slug field while third argument is true
        'body' => 'Post complete description here',
    ));

    if ($success) {
        // do something with db_insertId() or db_insertSlug()
    }

You can also provide a custom slug in the ``$data`` array. ::

    $slug = 'your-custom-slug-string';
    $success = db_insert('post', array(
        'slug' => $slug,
        'title' => 'Updated Title',
        'body' => 'Updated post complete description here'
    ));

- `db_insertId() <http://www.phplucidframe.com/api-doc/latest/function-db_insertId.html>`_ which returns the auto generated id used in the last query.
- `db_insertSlug() <http://www.phplucidframe.com/api-doc/latest/function-db_insertSlug.html>`_ returns the generated slug used in the last query.

.. note::
    - The first field in data array will be used to insert into the slug field.
    - Table prefix to the table name of the first parameter is optional.


Updating Your Data
------------------

``db_update()`` is a convenience method for your SQL ``UPDATE`` operation. The syntax is ::

    db_update('table_name', $data = array(), $useSlug = true, array $condition = array())

For example, ::

    $success = db_update('post', array(
        'id'    => 1, // The first field/value pair will be used as condition when you do not provide the fourth argument
        'title' => 'Updated Title', // this will be used for the slug field while third parameter is true
        'body'  => 'Updated post complete description here'
    ));

    // # Generated query
    // UPDATE post SET
    //   slug = "updated-title",
    //   title = "Updated Title",
    //   body = "Updated post complete description here
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE id = 1

    if ($success) {
        // do something
    }

You can also provide a custom slug in the ``$data`` array. ::

    $success = db_update('post', array(
        'id'    => 1, // The first field/value pair will be used as condition when you do not provide the fourth argument
        'slug'  => 'custom-updated-title', // providing custom slug string
        'title' => 'Updated Title',
        'body'  => 'Updated post complete description here'
    ));

    // # Generated query
    // UPDATE post SET
    //   slug = "custom-updated-title",
    //   title = "Updated Title",
    //   body = "Updated post complete description here
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE id = 1

You can provide the third or fourth parameter ``$condition``. See `Query Conditions <#id2>`_. As third parameter, ::

    db_update('post',
        array(
            'title' => 'Updated Title', // this will be used for slug as well
            'body'  => 'Updated post complete description here'
        ),
        array('id' => 1) // condition for update as third parameter
    );

    // # Generated query
    // UPDATE post SET
    //   slug = "updated-title",
    //   title = "Updated Title",
    //   body = "Updated post complete description here
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE id = 1

As fouth parameter, ::

    db_update('post',
        array(
            'title' => 'Updated Title',
            'body'  => 'Updated post complete description here'
        ),
        false, // To not update the slug field
        array('id' => 1) // condition for update as fouth parameter
    );

    // # Generated query
    // UPDATE post SET
    //   title = "Updated Title",
    //   body = "Updated post complete description here
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE id = 1

If you want to update the records by OR condition, use ``$or`` operator as key in the condition array. ::

    db_update('post',
        array(
            'status' => 'active'
        ),
        array(
            '$or' => array(
                'created <=' => date('Y-m-d H:i:s'),
                'created >=' => date('Y-m-d H:i:s'),
            )
        )
    );

    // # Generated query
    // UPDATE post SET
    //   status = 'active'
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE created <= 'xxxx-xx-xx xx:xx:xx' OR created >= 'xxxx-xx-xx xx:xx:xx'

Deleting Your Data
------------------

``db_delete()`` is a handy method for your SQL ``DELETE`` operation. This is only applicable for single record deletion. The syntax is ::

    db_delete('table_name', array $condition = array(), $softDelete = false)

LucidFrame encourages MYSQL Foreign Key Constraints to use. If ``ON DELETE RESTRICT`` is found, it performs soft delete (logical delete) by updating the current date/time into the field ``deleted``, otherwise it performs hard delete (physical delete). ::

    if (db_delete('post', array('id' => $idToDelete))) {
        $success = true;
    }

``db_delete_multi()`` is useful for batch record deletion for the given condition, but it does not check foreign key constraints. ::

    db_delete_multi('table_name', $condition = array(
        'field_name1'    => $value1,
        'field_name2 >=' => $value2,
        'field_name3'    => null,
    ))

If you want to delete the records by OR condition, use ``$or`` operator as key in the condition array. ::

    db_delete_multi('table_name', $condition = array(
        '$or' => array(
            'field_name1'    => $value1,
            'field_name2 >=' => $value2,
            'field_name3'    => null,
        )
    ))

See next section for `query conditions <#id2>`_ with ``db_delete()`` and ``db_delete_multi()``.

Query Conditions
----------------

You can provide a condition array to third or fourth parameter to ``db_update()`` and second parameter to ``db_delete()`` or ``db_delete_multi()``. You can use ``$and`` and ``$or`` operators as key in the condition array. The following are some examples.

Updating with simple condition: ::

    db_update('post', array(
        'title' => 'Updated Title',
    ), array(
        'id' => 1
    ));

    // # Generated query
    // UPDATE post SET
    //   slug = "updated-title",
    //   title = "Updated Title",
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE id = 1

Updating using AND condition: ::

    db_update('post', array(
            'cat_id' => 1 // The field to be updated
        ),
        false, // To not update the slug field
        array(
            'id' => 1,
            'delete !=' => NULL
        )
    );

    // # Generated query
    // UPDATE post SET
    //   cat_id = 1,
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE id = 1 AND deleted IS NOT NULL

Updating using IN condition: ::

    db_update('post', array(
            'cat_id' => 1 // The field to be updated
        ),
        false, // To not update the slug field
        array(
            'id' => array(1, 2, 3)
        ))
    );

    // # Generated query
    // UPDATE post SET
    //   cat_id = 1,
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE id IN (1, 2, 3)

Updating using OR condition: ::

    db_update('post', array(
            'cat_id' => 1 // The field to be updated
        ),
        false, // To not update the slug field
        array(
            '$or' => array(
                array('id' => 1),
                array('id' => 2)
            )
        )
    );

    // # Generated query
    // UPDATE post SET
    //   cat_id = 1,
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE id = 1 OR id = 2

Updating using IN and OR condition: ::

    db_update('post', array(
            'cat_id' => 1 // The field to be updated
        ),
        false, // To not update the slug field
        array(
            '$or' => array(
                'id' => array(1, 2, 3),
                'id >' => 10,
            )
        )
    );

    // # Generated query
    // UPDATE post SET
    //   cat_id = 1,
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE id IN (1, 2, 3) OR id > 10

Updating with complex AND/OR condition: ::

    db_update('post', array(
            'cat_id' => 1 // The field to be updated
        ),
        false, // To not update the slug field
        array(
            'title' => 'a project',
            'cat_id' => 2,
            '$or' => array(
                'id' => array(1, 2, 3),
                'id >=' => 10,
            )
        )
    );

    // # Generated query
    // UPDATE post SET
    //   cat_id = 1,
    //   updated = "xxxx-xx-xx xx:xx:xx"
    // WHERE title = "a project"
    // AND cat_id= 2
    // AND ( id IN (1, 2, 3) OR id >= 10 )

Condition Operators
-------------------

+---------------+-------------------------------------------+-----------------------------------------+
| Operator      | Usage Example                             | Equivalent SQL Condition                |
+===============+===========================================+=========================================+
| ``=``         | ``array('id' => 1)``                      | ``WHERE id = 1``                        |
|               | ``array('id' => array(1, 2, 3))``         | ``WHERE id IN (1, 2, 3)``               |
+---------------+-------------------------------------------+-----------------------------------------+
| ``!=``        | ``array('id !=' => 1)``                   | ``WHERE id != 1``                       |
|               | ``array('id !=' => array(1, 2, 3))``      | ``WHERE id NOT IN (1, 2, 3)``           |
+---------------+-------------------------------------------+-----------------------------------------+
| ``>``         | ``array('id >' => 1)``                    | ``WHERE id > 1``                        |
+---------------+-------------------------------------------+-----------------------------------------+
| ``>=``        | ``array('id >=' => 1)``                   | ``WHERE id >= 1``                       |
+---------------+-------------------------------------------+-----------------------------------------+
| ``<``         | ``array('id <' => 1)``                    | ``WHERE id < 1``                        |
+---------------+-------------------------------------------+-----------------------------------------+
| ``<=``        | ``array('id <=' => 1)``                   | ``WHERE id <= 1``                       |
+---------------+-------------------------------------------+-----------------------------------------+
| ``between``   | ``array('id between' => array(1, 10))``   | ``WHERE id BETWEEN 1 and 10``           |
+---------------+-------------------------------------------+-----------------------------------------+
| ``nbetween``  | ``array('id nbetween' => array(1, 10))``  | ``WHERE id NOT BETWEEN 1 and 10``       |
+---------------+-------------------------------------------+-----------------------------------------+
| ``like``      | ``array('title like' => 'a project')``    | ``WHERE title LIKE "%a project%"``      |
| ``like%%``    | ``array('title like%%' => 'a project')``  |                                         |
+---------------+-------------------------------------------+-----------------------------------------+
| ``like%~``    | ``array('title like%~' => 'a project')``  | ``WHERE title LIKE "%a project"``       |
+---------------+-------------------------------------------+-----------------------------------------+
| ``like~%``    | ``array('title like~%' => 'a project')``  | ``WHERE title LIKE "a project%"``       |
+---------------+-------------------------------------------+-----------------------------------------+
| ``nlike``     | ``array('title nlike' => 'a project')``   | ``WHERE title NOT LIKE "%a project%"``  |
| ``nlike%%``   | ``array('title nlike%%' => 'a project')`` |                                         |
+---------------+-------------------------------------------+-----------------------------------------+
| ``nlike%~``   | ``array('title nlike%~' => 'a project')`` | ``WHERE title NOT LIKE "%a project"``   |
+---------------+-------------------------------------------+-----------------------------------------+
| ``nlike~%``   | ``array('title nlike~%' => 'a project')`` | ``WHERE title NOT LIKE "a project%"``   |
+---------------+-------------------------------------------+-----------------------------------------+

Finding Data
------------

A couple of quick helpful functions are provided for fetching data from tables.

db_find()
^^^^^^^^^

Returns a single entity result where the primary key matches the value passed in as the second parameter for the table name in the first parameter.

**Parameters**:

+-----------+----------+-------------------------------------------------------------------------+
| Name      | Type     | Description                                                             |
+===========+==========+=========================================================================+
| ``table`` | string   | The table name to fetch data from                                       |
+-----------+----------+-------------------------------------------------------------------------+
| ``id``    | int      | The value of the primary key to match                                   |
+-----------+----------+-------------------------------------------------------------------------+

**Example**: ::

    $result = db_find('post', 1);

db_findOrFail()
^^^^^^^^^^^^^^^

Returns a single entity result where the primary key matches the value passed in as the second parameter for the table name in the first parameter OR throws 404 if any result is not found.

**Parameters**:

+-----------+----------+-------------------------------------------------------------------------+
| Name      | Type     | Description                                                             |
+===========+==========+=========================================================================+
| ``table`` | string   | The table name to fetch data from                                       |
+-----------+----------+-------------------------------------------------------------------------+
| ``id``    | int      | The value of the primary key to match                                   |
+-----------+----------+-------------------------------------------------------------------------+

**Example**: ::

    $result = db_findOrFail('post', 1);

db_findAll()
^^^^^^^^^^^^

Returns all rows from the given table.

**Parameters**:

+-----------------+----------+-------------------------------------------------------------------------+
| Name            | Type     | Description                                                             |
+=================+==========+=========================================================================+
| ``table``       | string   | The table name to fetch data from                                       |
+-----------------+----------+-------------------------------------------------------------------------+
| ``fields``      | array    | The list of the field names to select (optional)                        |
+-----------------+----------+-------------------------------------------------------------------------+
| ``orderBy``     | array    | The order by clause for query (optional)                                |
+-----------------+----------+-------------------------------------------------------------------------+

**Example**: ::

    $result = db_findAll('post', array('id', 'cat_id', 'title'));

db_findBy()
^^^^^^^^^^^

Returns array of data row objects of a table by condition.

**Parameters**:

+-----------------+----------+-------------------------------------------------------------------------+
| Name            | Type     | Description                                                             |
+=================+==========+=========================================================================+
| ``table``       | string   | The table name to fetch data from                                       |
+-----------------+----------+-------------------------------------------------------------------------+
| ``condition``   | array    | The condition array for query (optional)                                |
+-----------------+----------+-------------------------------------------------------------------------+
| ``orderBy``     | array    | The order by clause for query (optional)                                |
+-----------------+----------+-------------------------------------------------------------------------+
| ``limit``       | int      | The number of records to return; No limit by default                    |
+-----------------+----------+-------------------------------------------------------------------------+

**Example**: ::

    $result = db_findBy('post', array('cat_id' => 1), array('created' => 'desc'), 3);

db_findOneBy()
^^^^^^^^^^^^^^

Returns a single entity result of a table by condition

**Parameters**:

+-----------------+----------+-------------------------------------------------------------------------+
| Name            | Type     | Description                                                             |
+=================+==========+=========================================================================+
| ``table``       | string   | The table name to fetch data from                                       |
+-----------------+----------+-------------------------------------------------------------------------+
| ``condition``   | array    | The condition array for query (optional)                                |
+-----------------+----------+-------------------------------------------------------------------------+
| ``orderBy``     | array    | The order by clause for query (optional)                                |
+-----------------+----------+-------------------------------------------------------------------------+

**Example**: ::

    $result = db_findOneBy('post', array('cat_id' => 1), array('created' => 'desc'));

db_findOneByOrFail()
^^^^^^^^^^^^^^^^^^^^

Returns a single entiry result of a table by condition or throw 404 if not found

**Parameters**:

+-----------------+----------+-------------------------------------------------------------------------+
| Name            | Type     | Description                                                             |
+=================+==========+=========================================================================+
| ``table``       | string   | The table name to fetch data from                                       |
+-----------------+----------+-------------------------------------------------------------------------+
| ``condition``   | array    | The condition array for query (optional)                                |
+-----------------+----------+-------------------------------------------------------------------------+
| ``orderBy``     | array    | The order by clause for query (optional)                                |
+-----------------+----------+-------------------------------------------------------------------------+

**Example**: ::

    $result = db_findOneBy('post', array('cat_id' => 1), array('created' => 'desc'));


db_findWithPager()
^^^^^^^^^^^^^^^^^^

Returns array of data row objects with pagination result.

**Parameters**:

+-----------------+----------+-------------------------------------------------------------------------+
| Name            | Type     | Description                                                             |
+=================+==========+=========================================================================+
| ``table``       | string   | The table name to fetch data from                                       |
+-----------------+----------+-------------------------------------------------------------------------+
| ``condition``   | array    | The condition array for query (optional)                                |
+-----------------+----------+-------------------------------------------------------------------------+
| ``orderBy``     | array    | The order by clause for query (optional)                                |
+-----------------+----------+-------------------------------------------------------------------------+
| ``pagerOption`` | array    | Array of key/value pairs to Pager options (optional)                    |
+-----------------+----------+-------------------------------------------------------------------------+

**Return**:

Array of three items:

#. QueryBuilder - An instance of ``LucidFrame\Core\QueryBuilder``
#. Pager - An instance of ``LucidFrame\Core\Pager``
#. int - The total number of records


**Example**: ::

    list($qb, $pager, $total) = db_findWithPager('post', array('cat_id' => 1), array('created' => 'desc'));
