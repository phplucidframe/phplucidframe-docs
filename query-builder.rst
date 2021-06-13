Query Builder
=============

As of version 1.9, PHPLucidFrame added a new feature called query builder that allows data to be retrieved in your database without writing raw SQL statements.

Selecting Data for Multiple Results
------------------------------------

If you want to fetch an array of result set, you can use ``getResult()``. ::

    $result = db_select('post')->getResult();
    _pr($result); // array of results

This generates the following query: ::

    SELECT * FROM `post`

Selecting Data for Single Result
---------------------------------

To get a single result set, you can use ``getSingleResult()``. ::

    $result = db_select('post')->getSingleResult();
    _pr($result); // the result object

This generates the following query: ::

    SELECT * FROM `post` LIMIT 1


Selecting Data for Muliple Fields
---------------------------------

To fetch multiple fields, you can use ``fields('table`, array('field1', 'field2', ...))``. The first parameter is table name or alias. The second paramter is a list of field names to fetch from the table. ::

    $result = db_select('post', 'p')
        ->fields('p', array('id', 'title', 'created'))
        ->getResult();

    _pr($result); // array of results

This generates the following query: ::

    SELECT `p`.`id`, `p`.`title`, `p`.`created` FROM `post` `p`

If you want field alias, you can use nested array in ``fields()``, for example, ::

    $result = db_select('post', 'p')
        ->fields('p', array('id', array('title', 'title'), 'created'))
        ->getResult();

    _pr($result); // array of results

In this case, ``post_title`` is alias for ``title``. This generates the following query: ::

    SELECT `p`.`id`, `p`.`title` `title`, `p`.`created` FROM `post` `p`

Selecting Data for Single Field
-------------------------------

To fetch a single field, you can use ``field('field_name')`` and then ``fetch()``. ::

    $title = db_select('post', 'p')
        ->field('title')
        ->fetch();

    echo $title;

This generates the following query: ::

    SELECT `p`.`title` FROM `post`

Joining Tables
--------------

If you want to join multiple tables, you can use ``join($table, $alias, $condition, $type = 'INNER')``. Here is explanation of the list of arguments:

- ``$table`` is the table name to join.
- ``$alias`` is the alias for the table name and you can also set ``null`` for this.
- ``$condition`` is the joining condition e.g., ``table1.pk_id = table2.fk_id``.
- ``$type`` is the join type. Available options are ``INNER``, ``LEFT``, ``RIGHT``, ``OUTER``. Default is ``INNER``. ::

    $result = db_select('post', 'p')
        ->fields('p', array('id', 'title'))
        ->fields('u', array(array('name', 'author')))
        ->fields('c', array(array('name', 'categoryName')))
        ->join('user', 'u', 'p.uid = u.uid')
        ->leftJoin('category', 'c', 'p.cat_id = c.cat_id')
        ->getResult();
    _pr($result); // array of results

It generates the following query: ::

    SELECT `p`.`id`, `p`.`title`, `u`.`name` `author`, `c`.`name` `categoryName`
    FROM `post` `p`
    INNER JOIN `user` `u` ON `p`.`uid` = `u`.`uid`
    LEFT JOIN `category` `c` ON `p`.`id` = `c`.`id`

.. note::
    - Instead of fourth parameter to ``join()``, you could also use the individual methods - ``leftJoin()``, ``rightJoin()`` and ``outerJoin()``.

Fetching Specific Data (WHERE condition)
----------------------------------------

There are some methods available to create query conditions - ``where()``, ``andWhere()``, ``orWhere()`` and ``condition()``. ``where()`` is an alias of ``andWhere()``. You can use ``where()``, ``andWhere()`` and ``orWhere()`` with array parameter or without parameter.

Simple condition
^^^^^^^^^^^^^^^^

For array parameter, it accepts all `conditional operators described in the previous section <database-configuration-and-data-manipulation.html#condition-operators>`_, for example, ::

    $result = db_select('post', 'p')
        ->fields('p', array('id', 'title'))
        ->fields('u', array(array('name', 'author')))
        ->fields('c', array(array('name', 'categoryName')))
        ->join('user', 'u', 'p.user_id = u.id')
        ->leftJoin('category', 'c', 'p.cat_id = c.id')
        ->where(array(
            'c.id' => 1,
            'u.id' => 2
        ))
        ->getResult();

Without parameter, it initializes to create conditions by using ``condition()``: ::

    $result = db_select('post', 'p')
        ->fields('p', array('id', 'title'))
        ->fields('u', array(array('name', 'author')))
        ->fields('c', array(array('name', 'categoryName')))
        ->join('user', 'u', 'p.user_id = u.id')
        ->leftJoin('category', 'c', 'p.cat_id = c.id')
        ->where()
            ->condition('c.id', 1)
            ->condition('u.id', 2)
        ->getResult();

The above two queries would generate the following same query: ::

    SELECT `p`.`id`, `p`.`title`, `u`.`name` `author`, `c`.`name` `categoryName`
    FROM `post` `p`
    INNER JOIN `user` `u` ON `p`.`user_id` = `u`.`id`
    LEFT JOIN `category` `c` ON `p`.`cat_id` = `c`.`id`
    WHERE `c`.`id` = 1
    AND `u`.`id` = 2

Complex condition using AND/OR
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can use ``db_and()`` and ``db_or()`` for complex conditions. Here is an exmaple: ::

    $result = db_select('post', 'p')
        ->fields('p')
        ->fields('u', array('username', array('name', 'author')))
        ->join('user', 'u', 'p.user_id = u.id')
        ->leftJoin('category', 'c', 'p.cat_id = c.id')
        ->where(array(
            'title like' => 'Sample project',
            db_or(array(
                'p.id' => array(1, 2, 3),
                'u.id' => 1
            ))
        ))
        ->orderBy('p.created', 'desc')
        ->limit(0, 20)
        ->getResult();

It generates the following query: ::

    SELECT `p`.*, `u`.`username`, `u`.`name` `author`
    FROM `post` `p`
    INNER JOIN `user` `u` ON `p`.`user_id` = `u`.`id`
    LEFT JOIN `category` `c` ON `p`.`cat_id` = `c`.`id`
    WHERE `p`.`title` LIKE "%Sample project%"
    AND ( `p`.`id` IN (1, 2, 3) OR `u`.`id` = 1 )
    ORDER BY `p`.`created` DESC
    LIMIT 0, 20

Complex nested condition using OR/AND/OR
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following is an example for complex nested conditions using AND/OR: ::

    $result = db_select('post', 'p')
        ->fields('p')
        ->fields('u', array('username', array('name', 'author')))
        ->join('user', 'u', 'p.user_id = u.id')
        ->leftJoin('category', 'c', 'p.cat_id = c.id')
        ->orWhere(array(
            'p.title nlike' => 'Sample project',
            db_and(array(
                'p.id' => array(1, 2, 3),
                'p.status <=' => 10,
                db_or(array(
                    'p.created >' => '2017-12-31',
                    'p.deleted' => null
                ))
            ))
        ))
        ->orderBy('p.created', 'desc')
        ->limit(5)
        ->getResult()

It generates the following query: ::

    SELECT `p`.*, `u`.`username`, `u`.`name` `author`
    FROM `post` `p`
    INNER JOIN `user` `u` ON `p`.`user_id` = `u`.`id`
    LEFT JOIN `category` `c` ON `p`.`cat_id` = `c`.`id`
    WHERE `p`.`title` NOT LIKE "%Sample project%"
    OR (
        `p`.`id` IN (1, 2, 3)
        AND `p`.`status` <= 10
        AND ( `p`.`created` > "2017-12-31" OR `p`.`deleted` IS NULL )
    )
    ORDER BY `p`.`created` DESC
    LIMIT 5


Grouping Results
----------------

You can use ``groupBy()`` to write the GROUP BY portion of your query: ::

    $result = db_select('post', 'p')
        ->groupBy('p.cat_id')
        ->getResult();

You can use multiple ``groupBy()`` calls. This generates the following query: ::

    SELECT `p`.* FROM `post` `p`
    GROUP BY `p`.`cat_id`

HAVING Condition on Group Result
--------------------------------

There are some methods available to create having conditions - ``having()``, ``andHaving()``, ``orHaving()``. ``having()`` is an alias of ``andHaving()``. You can use them with array parameter of `conditional operators described in the previous section <database-configuration-and-data-manipulation.html#condition-operators>`_, for example, ::

    $result = db_select('post', 'p')
        ->groupBy('p.cat_id')
        ->having(array(
            'p.cat_id >' => 10,
            'p.status' => 1
        ))
        ->getResult();

This generates the following query: ::

    SELECT `p`.* FROM `post` `p`
    GROUP BY `p`.`cat_id`
    HAVING `p`.`cat_id` > 10 AND `p`.`status` = 1

You can create OR condition on having using ``orHaving()`` like this: ::

    $result = db_select('post', 'p')
        ->groupBy('p.cat_id')
        ->orHaving(array(
            'p.cat_id >' => 10,
            'p.status' => 1
        ))
        ->getResult();

Ordering Results
----------------

You can use ``orderBy('field', 'asc|desc')``. The first parameter contains the name of the field you would like to order by. The second parameter lets you set the direction of the result. Options are ``asc`` and ``desc``. Default to ``asc``.::

    $result = db_select('post', 'p')
        ->fields('p', array('id', 'title', 'created'))
        ->orderBy('p.title', 'asc)
        ->orderBy('p.created', 'desc')
        ->getResult();

    _pr($result); // array of results

This generates the following query: ::

    SELECT `p`.`id`, `p`.`title`, `p`.`created` FROM `post` `p`
    ORDER BY `p`.`title` ASC, `p`.`created` DESC

Counting Results
----------------

``db_count()`` lets you determine the number of rows in a particular table. ::

    $rowCount = db_count('post')
        ->where()->condition('deleted', null)
        ->fetch();

    echo $rowCount;

This generates the following query: ::

    SELECT COUNT(*) count FROM `post` WHERE deleted IS NULL

Limiting Results
----------------

``limit()`` permits to limit the number of rows you would like returned by the query: ::

    $result = db_select('post')
        ->limit(10)
        ->getResult();
    _pr($result); // array of results

This generates the following query to return the first 10 records from the table ``post``: ::

    SELECT * FROM `post` LIMIT 10

You can also set offset to ``limit()``: ::

    $result = db_select('post')
        ->limit(0, 10)
        ->getResult();

The following query will be executed: ::

    SELECT * FROM `post` LIMIT 0, 10


Aggregates
----------

There are aggregate functions available - ``db_min()``, ``db_max()``, ``db_sum()``, ``db_avg()``.

MAX
^^^

Syntax: ``db_max($table, $field, $alias = null)`` ::

    $max = db_max('post', 'view_count')->fetch();
    // SELECT MAX(`view_count`) max FROM `post` `post`

MIN
^^^

Syntax: ``db_min($table, $field, $alias = null)`` ::

    $min = db_min('post', 'view_count')->fetch();
    // SELECT MIN(`view_count`) min FROM `post` `post`

SUM
^^^

Syntax: ``db_sum($table, $field, $alias = null)`` ::

    $sum = db_sum('post', 'view_count')->fetch();
    // SELECT SUM(`view_count`) sum FROM `post` `post`

AVG
^^^

Syntax: ``db_avg($table, $field, $alias = null)`` ::

    $sum = db_avg('post', 'view_count')->fetch();
    // SELECT SUM(`view_count`) avg FROM `post` `post`

Aggregate functions together
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can use aggregate function together like below: ::

    $result = db_select('post', 'p')
        ->max('view_count', 'max')
        ->min('view_count', 'min')
        ->getResult();

This generates: ::

    SELECT MAX(`view_count`) max, MIN(`view_count`) min
    FROM `post` `p`

.. note::
    - More complex query examples can be found in `https://github.com/phplucidframe/phplucidframe/blob/master/tests/lib/query_builder.test.php <https://github.com/phplucidframe/phplucidframe/blob/master/tests/lib/query_builder.test.php>`_.
    - You may also check `how to retrieve data using native SQL <database-configuration-and-data-manipulation.html#retrieving-your-data>`_.
