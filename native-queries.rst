Native Queries
==============

PHPLucidFrame provides several db helper functions to retrieve data from your database using native queries. You can use the following functions to retrieve your data:

- `db_query() <http://www.phplucidframe.com/api-doc/latest/function-db_query.html>`_ which peforms a query on the database.
- `db_fetchAssoc() <http://www.phplucidframe.com/api-doc/latest/function-db_fetchAssoc.html>`_ which fetchs a result row as an associate array.
- `db_fetchArray() <http://www.phplucidframe.com/api-doc/latest/function-db_fetchArray.html>`_ which fetchs a result row as a numeric array.
- `db_fetchObject() <http://www.phplucidframe.com/api-doc/latest/function-db_fetchObject.html>`_ which fetchs a result row as an object.
- `db_fetch() <http://www.phplucidframe.com/api-doc/latest/function-db_fetch.html>`_ which performs a query on the database and return the first field value only.
- `db_fetchResult() <http://www.phplucidframe.com/api-doc/latest/function-db_fetchResult.html>`_ which performa a query on the database and return the first result row as object.
- `db_extract() <http://www.phplucidframe.com/api-doc/latest/function-db_extract.html>`_ which performs a query on the database and return the array of all results.

.. note:: Instead of using native query to fetch data, QueryBuilder is recommended. See `Query Builder <query-builder>`_ usage.

The following is an example to retrieve multiple result rows: ::

    $sql = 'SELECT * FROM ' . db_table('post') . ' ORDER BY title';
    if ($result = db_query($sql)){
        while($row = db_fetchAssoc($result)){
            // do somethings here...
        }
    }

    // Extract all data into an array of objects
    // db_extract() invokes db_fetchObject() by default internally
    $sql = 'SELECT * FROM ' . db_table('post') . ' ORDER BY title';
    $posts = db_extract($sql); // The second or third argument can be given one of these: LC_FETCH_OBJECT (default), LC_FETCH_ASSOC, LC_FETCH_ARRAY
    _pr($posts);

    // Extract all data into key/value pair of array
    $sql = 'SELECT id AS key, title AS value FROM ' . db_table('post') . ' ORDER BY title';
    $posts = db_extract($sql);
    _pr($posts);
    /*
    array(
      $id => $title
    )
    */

The following is an example to retrieve a single result: ::

    // Retrieving a single-row result
    $sql = 'SELECT * FROM ' . db_table('post') . ' WHERE id = :id';
    if ($post = db_fetchResult($sql, array(':id' => $id))) {
        _pr($post);
        // $post->id;
        // $post->title;
    }

    // Retrieving the result count
    $sql = 'SELECT COUNT(*) FROM ' . db_table('post');
    $count = db_count($sql);

    // Retrieving a field
    $sql = 'SELECT MAX(id) FROM ' . db_table('post');
    $max = db_fetch($sql);

