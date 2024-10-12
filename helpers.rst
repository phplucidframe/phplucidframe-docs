Useful Helpers
==============

PHPLucidFrame provides some useful helper functions.

_app($name, $value = null)
--------------------------

Get/Set a global variable/object.

**Parameters**:

+---------------+-------------+------------------------------------------------+
| Name          | Type        | Description                                    |
+===============+=============+================================================+
| ``$name``     | string      | The name of the variable/object                |
+---------------+-------------+------------------------------------------------+
| ``$value``    | mixed       | (Optional) The value or the variable or object |
+---------------+-------------+------------------------------------------------+

When the second parameter is ommited, it is a getter function, otherwise it is a setting function.

**Return Values**:

The value stored globally

+-------------------+-----------------------------------------------+
| Getter Usage      | Description                                   |
+===================+==============+================================+
| ``_app('db')``    | To get the globally connected database object |
+-------------------+--------------+--------------------------------+
| ``_app('view')``  | To get the global View object                 |
+-------------------+--------------+--------------------------------+
| ``_app('auth')``  | To get the global authentication object       |
+-------------------+-------------+---------------------------------+
| ``_app('title')`` | To get the current page title                 |
+-------------------+--------------+--------------------------------+

+------------------------------------------------------------+------------------------------------------------------------+
| Setter Usage                                               | Description                                                |
+============================================================+============================================================+
| ``_app('db', new \LucidFrame\Core\Database($namespace));`` | To create a new global database object (it is rarely used) |
+------------------------------------------------------------+------------------------------------------------------------+
| ``_app('view', new \LucidFrame\Core\View())``              | To set a new View object (it is rarely used)               |
+------------------------------------------------------------+------------------------------------------------------------+
| ``_app('auth', $auth)``                                    | To set/update the authentication object globally           |
+------------------------------------------------------------+------------------------------------------------------------+
| ``_app('title', 'Home')``                                  | To set the current page title                              |
+------------------------------------------------------------+------------------------------------------------------------+

_cfg($key, $value = '')
----------------------------

Get/Set a config variable

**Parameters**:

+---------------+-------------+--------------------------------------------------------------------------------------------+
| Name          | Type        | Description                                                                                |
+===============+=============+============================================================================================+
| ``$key``      | string      | The config variable name without prefix                                                    |
+---------------+-------------+--------------------------------------------------------------------------------------------+
| ``$value``    | mixed       | (Optional) The value to set to the config variable; if it is omitted, it is Getter method. |
+---------------+-------------+--------------------------------------------------------------------------------------------+

When the second parameter is ommited, it is a getter function, otherwise it is a setting function.

**Return Values**:

The value of the config variable

_cfgOption($name, $key)
-----------------------

Get the value of the array config variable by its key

**Parameters**:

+---------------+-------------+------------------------------------------------------------+
| Name          | Type        | Description                                                |
+===============+=============+============================================================+
| ``$name``     | string      | The config array variable name without prefix              |
+---------------+-------------+------------------------------------------------------------+
| ``$key``      | string      | The key of the config array of which value to be retrieved |
+---------------+-------------+------------------------------------------------------------+

**Return Values**:

The value of a single column of the config array variable

_env($name, $default = '')
--------------------------

Get the parameter value by name defined in ``/inc/parameter/parameter.env.inc``

**Parameters**:

+---------------+-------------+----------------------------------------------------------------------------------+
| Name          | Type        | Description                                                                      |
+===============+=============+==================================================================================+
| ``$name``     | string      | The parameter name in dot annotation format such as ``prod.db.default.database`` |
+---------------+-------------+----------------------------------------------------------------------------------+
| ``$default``  | mixed       | The default value if the parameter name doesn't exist                            |
+---------------+-------------+----------------------------------------------------------------------------------+

**Return Values**:

The value defined in ``/inc/parameter/parameter.env.inc``

_p($name = 'env')
-----------------

Get the parameter value by name defined in ``/inc/parameter/(development|production|staging|test).php``

**Parameters**:

+---------------+-------------+--------------------------------------------------------------------------------------------------------------+
| Name          | Type        | Description                                                                                                  |
+===============+=============+==============================================================================================================+
| ``$name``     | string      | The parameter name defined as key in ``/inc/parameter/(development|production|staging|test).php``.           |
|               |             | The file development, production, staging or test will be determined according to the value from ``.lcenv``. |
|               |             | If ``$name`` is ``env`` (by default), it returns the current environment setting from ``.lcenv``.            |
+---------------+-------------+--------------------------------------------------------------------------------------------------------------+

You can use dot notation format for the name parameter such as ``db.default.database`` for multi-level array keys.

**Return Values**:

The value defined ``/inc/parameter/(development|production|staging|test).php``

_baseUrlWithProtocol()
----------------------

Get site base URL with protocol according to the config

_arg($index = null, $path = null)
---------------------------------

Return a component of the current path. When viewing a page http://www.example.com/foo/bar and its path would be "foo/bar", for example, ``_arg(0)`` returns "foo" and ``_arg(1)`` returns "bar"

**Parameters**:

+-------------+-----------+-----------------------------------------------------------------------------------------+
| Name        | Type      | Description                                                                             |
+=============+===========+=========================================================================================+
| ``$index``  | mixed     | The index of the component, where each component is separated by a '/' (forward-slash), |
|             |           | and where the first component has an index of 0 (zero).                                 |
+-------------+-----------+-----------------------------------------------------------------------------------------+
| ``$path``   | string    | A path to break into components. Defaults to the path of the current page.              |
+-------------+-----------+-----------------------------------------------------------------------------------------+

**Return Values**:

The component specified by ``$index``, or ``null`` if the specified component was not found. If called without arguments, it returns an array containing all the components of the current path.

_entity($table, $dbNamespace = null)
------------------------------------

Get default entity object from the schema

**Parameters**:

+------------------+-------------+--------------------------------------+
| Name             | Type        | Description                          |
+==================+=============+======================================+
| ``$table``       | string      | The mapped table name without prefix |
+------------------+-------------+--------------------------------------+
| ``$dbNamespace`` | string|null | The current db namespace             |
+------------------+-------------+--------------------------------------+

**Return Values**:

The empty stdClass object with field names as properties

_addJsVar($name, $value = '')
-----------------------------

Passing values from PHP to Javascript making available to ``LC.vars``

**Parameters**:

+---------------+-------------+-------------------------------------------------------------------------+
| Name          | Type        | Description                                                             |
+===============+=============+=========================================================================+
| ``$name``     | string      | The JS variable name that will be available to ``LC.vars``,             |
|               |             | e.g., if $name is 'foo', it will be available as ``LC.vars.foo`` in JS. |
+---------------+-------------+-------------------------------------------------------------------------+
| ``$value``    | mixed       | The value for the JS variable                                           |
+---------------+-------------+-------------------------------------------------------------------------+

_addHeadStyle($file)
--------------------

Add CSS file to be included in head section

**Parameters**:

+---------------+-------------+-----------------------------------------------------------------------------------------------------------------------+
| Name          | Type        | Description                                                                                                           |
+===============+=============+=======================================================================================================================+
| ``$file``     | string      | An absolute file path or file name only.                                                                              |
|               |             | The file name only will be prepended the folder name ``css/`` and it will be looked in every sub-sites ``css`` folder |
+---------------+-------------+-----------------------------------------------------------------------------------------------------------------------+

_addHeadScript($file)
---------------------

Add JS file to be included in head section

**Parameters**:

+---------------+-------------+-----------------------------------------------------------------------------------------------------------------------+
| Name          | Type        | Description                                                                                                           |
+===============+=============+=======================================================================================================================+
| ``$file``     | string      | An absolute file path or file name only.                                                                              |
|               |             | The file name only will be prepended the folder name ``js/`` and it will be looked in every sub-sites ``js`` folder   |
+---------------+-------------+-----------------------------------------------------------------------------------------------------------------------+

_json(array $data, $status = 200, $return = false)
--------------------------------------------------

Header sent as content type ``application/json``

**Parameters**:

+---------------+-------------+-------------------------------------+
| Name          | Type        | Description                         |
+===============+=============+=====================================+
| ``$data``     | array       | Array of data to be encoded as JSON |
+---------------+-------------+-------------------------------------+
| ``$status``   | int         | HTTP status code, default to 200    |
+---------------+-------------+-------------------------------------+
| ``$return``   | boolean     | Return json data or not             |
+---------------+-------------+-------------------------------------+


_requestHeader($name)
---------------------

Get a HTTP request header by name

**Parameters**:

+------------------+-------------+--------------------------------------+
| Name             | Type        | Description                          |
+==================+=============+======================================+
| ``$name``        | string      | The HTTP header name                 |
+------------------+-------------+--------------------------------------+

For example, to get the value from HTTP Authorization in header: ::

    $authorization = _requestHeader('Authorization');

**Return Values**:

The HTTP header value from the request

_r()
----

Get the current routing path, for example,

.. note::
    - ``example.com/foo/bar`` would return ``foo/bar``
    - ``example.com/en/foo/bar`` would also return ``foo/bar``
    - ``example.com/1/this-is-slug`` (if accomplished by RewriteRule) would return the underlying physical path

**Return Values**:

The route path starting from the site root

_rr()
-----

The more realistic function to get the current routing path on the address bar regardless of RewriteRule behind, for example,

.. note::
    - ``example.com/foo/bar`` would return ``foo/bar``
    - ``example.com/en/foo/bar`` would also return ``foo/bar``
    - ``example.com/foo/bar?id=1`` would also return ``foo/bar``
    - ``example.com/1/this-is-slug`` would return ``1/this-is-slug``

**Return Values**:

The route path starting from the site root

_url($path = null, $queryStr = array(), $lang = '')
---------------------------------------------------

Get the absolute URL path

**Parameters**:

+---------------+-------------+--------------------------------------------------------------+
| Name          | Type        | Description                                                  |
+===============+=============+==============================================================+
| ``$path``     | string      | Routing path such as "foo/bar"; null for the current path    |
+---------------+-------------+--------------------------------------------------------------+
| ``$queryStr`` | array       | Query string as key/value array                              |
+---------------+-------------+--------------------------------------------------------------+
| ``$lang``     | string      | Language code to be prepended to $path such as "en/foo/bar". |
|               |             | It will be useful for site language switch redirect.         |
+---------------+-------------+--------------------------------------------------------------+

**Return Values**:

The absolute URL path

_redirect($path = null, $queryStr = array(), $lang = '', $status = null)
------------------------------------------------------------------------

Header redirect to a specific location

**Parameters**:

+---------------+-------------+--------------------------------------------------------------+
| Name          | Type        | Description                                                  |
+===============+=============+==============================================================+
| ``$path``     | string      | Routing path such as "foo/bar"; null for the current path    |
+---------------+-------------+--------------------------------------------------------------+
| ``$queryStr`` | array       | Query string as key/value array                              |
+---------------+-------------+--------------------------------------------------------------+
| ``$lang``     | string      | Language code to be prepended to $path such as "en/foo/bar". |
|               |             | It will be useful for site language switch redirect.         |
+---------------+-------------+--------------------------------------------------------------+
| ``$status``   | int         | The HTTP status code                                         |
+---------------+-------------+--------------------------------------------------------------+

_page404()
----------

Redirect to 404 page

_shorten($str, $length = 50, $trail = '...')
--------------------------------------------

Shorten a string for the given length

**Parameters**:

+---------------+-------------+--------------------------------------------------------------+
| Name          | Type        | Description                                                  |
+===============+=============+==============================================================+
| ``$str``      | string      | A plain text string to be shortened                          |
+---------------+-------------+--------------------------------------------------------------+
| ``$length``   | int         | The character count. Default is 50.                          |
+---------------+-------------+--------------------------------------------------------------+
| ``$trail``    | string      | To append ``...`` or not. ``null`` to not show               |
+---------------+-------------+--------------------------------------------------------------+

**Return Values**:

The shorten text string


_fdate($date = '', $format = '')
--------------------------------

Format a date

**Parameters**:

+---------------+-------------+-----------------------------------------------------------------------+
| Name          | Type        | Description                                                           |
+===============+=============+=======================================================================+
| ``$date``     | string|int  | A date string or seconds to be formatted                              |
+---------------+-------------+-----------------------------------------------------------------------+
| ``$format``   | string      | The date format; The config variable will be used if it is not passed |
+---------------+-------------+-----------------------------------------------------------------------+

When both parameters is not provided, the current formatted date will be returned.

**Return Values**:

The formatted date


_fdatetime($dateTime = '', $format = '')
----------------------------------------

Format a date/time

**Parameters**:

+---------------+-------------+----------------------------------------------------------------------------+
| Name          | Type        | Description                                                                |
+===============+=============+============================================================================+
| ``$dateTime`` | string|int  | A date/time string or seconds to be formatted                              |
+---------------+-------------+----------------------------------------------------------------------------+
| ``$format``   | string      | The date/time format; The config variable will be used if it is not passed |
+---------------+-------------+----------------------------------------------------------------------------+

When both parameters is not provided, the current formatted date/time will be returned.

**Return Values**:

The formatted date/time

_randomCode($length = 5, $letters = array(), $prefix = '')
----------------------------------------------------------

Generate a random string from the given array of letters.

**Parameters**:

+---------------+-----------+----------------------------------------------------------------+
| Name          | Type      | Description                                                    |
+===============+===========+================================================================+
| ``$length``   | int       | The length of required random string                           |
+---------------+-----------+----------------------------------------------------------------+
| ``$letters``  | array     | Array of letters from which randomized string is derived from. |
|               |           | Default is a to z and 0 to 9.                                  |
+---------------+-----------+----------------------------------------------------------------+
| ``$prefix``   | string    | Prefix to the generated string                                 |
+---------------+-----------+----------------------------------------------------------------+

**Return Values**:

The random string of required length

_slug($string, $table = '', array $condition = array())
-------------------------------------------------------

Generate a slug of human-readable keywords

**Parameters**:

+---------------+-----------+-----------------------------------------------------------------------------+
| Name          | Type      | Description                                                                 |
+===============+===========+=============================================================================+
| ``$string``   | string    | Text to slug                                                                |
+---------------+-----------+-----------------------------------------------------------------------------+
| ``$table``    | string    | Table name to check in. If it is empty, no check in the table               |
+---------------+-----------+-----------------------------------------------------------------------------+
| ``$condition``| array     | Condition to append table check-in, e.g, ``array('fieldName !=' => value)`` |
+---------------+-----------+-----------------------------------------------------------------------------+

**Return Values**:

The generated slug

_isHttpPost()
-------------

Check if HTTP request method is POST and has request data

**Return Values**:

``TRUE`` if HTTP request method is POST and it has request data, otherwise ``FALSE``

.. note::
    - See more helper functions at `<http://www.phplucidframe.com/api-doc/latest/>`_
