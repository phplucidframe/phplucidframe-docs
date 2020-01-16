Schema Manager
==============

As of version 1.14, PHPLucidFrame added a new feature called **Schema Manager** to manage your site database. A schema file for your database can be defined in the directory ``/db``. The file name format is ``schema.[namespace].php`` where namespace is your database namespace defined in ``$lc_databases`` of ``/inc/config.php``. If ``[namespace]`` is omitted, "default" is used. The schema file syntax is an array of options and table names that must be returned to the caller. A sample schema file is available at ``/db/schema.sample.php`` in the release.

Default Options for Tables
--------------------------

The schema syntax starts with ``_options`` which is a set of defaults for all tables, but it can be overidden by each table definition. ::

    '_options' => array(
        // defaults for all tables; this can be overidden by each table
        'timestamps'    => true, // all tables will have 3 datetime fields - `created`, `updated`, `deleted`
        'constraints'   => true, // all FK constraints to all tables
        'engine'        => 'InnoDB', // db engine for all tables
        'charset'       => 'utf8', // charset for all tables
        'collate'       => _p('db.default.collation'), // collation for all tables; inherited from /inc/parameter/
    ),

Table Definition
----------------

After then, each table can be defined using table name as key and value as an array of field definition. The following is a snapshot of example schema definition for two tables (category and post) that have one-to-many relation. ::

    // array keys are table names without prefix
    'category' => array(
        'slug'          => array('type' => 'string', 'length' => 255, 'null' => false, 'unique' => true),
        'catName'       => array('type' => 'string', 'length' => 200, 'null' => false),
        'catName_en'    => array('type' => 'string', 'length' => 200, 'null' => true),
        'catName_my'    => array('type' => 'string', 'length' => 200, 'null' => true),
        'options' => array(
            'pk'  => array('catId'),     // type: integer, autoinc: true, null: false, unsigned: true
            'timestamps' => true,        // created, updated, deleted; override to _options.timestamps
            'charset'    => 'utf8',      // override to _options.charset
            'collate'    => 'utf8_unicode_ci',  // override to _options.collate
            'engine'     => 'InnoDB',           // override to _options.engine
        ),
        '1:m' => array(
            // one-to-many relation between `category` and `post`
            // there must also be 'm:1' definition at the side of `post`
            'post' => array(
                'name' => 'catId', // FK field name in the other table (defaults to "table_name + _id")
                //'unique'  => false,   // Unique index for FK; defaults to false
                //'default' => null,    // default value for FK; defaults to null
                'cascade'   => true,    // true for ON DELETE CASCADE; false for ON DELETE RESTRICT
            ),
        ),
    ),
    'post' => array(
        'slug'          => array('type' => 'string', 'length' => 255, 'null' => false, 'unique' => true),
        'postTitle'     => array('type' => 'string', 'null' => false),
        'postTitle_en'  => array('type' => 'string', 'null' => true),
        'postTitle_my'  => array('type' => 'string', 'null' => true),
        'postBody'      => array('type' => 'text', 'null' => false),
        'postBody_en'   => array('type' => 'text', 'null' => true),
        'postBody_my'   => array('type' => 'text', 'null' => true),
        'options' => array(
            'Pk' => array('postId'), // if this is not provided, default field name to `id`
        ),
        '1:m' => array(
            // one-to-many relation between `post` and `post_image`
            // there must also be 'm:1' definition at the side of `post_image`
            'post_image' => array(
                'name'      => 'postId',
                'cascade'   => true,
            ),
        ),
        'm:1' => array(
            'category', // reversed 1:m relation between `category` and `post`
            'user',     // reversed 1:m relation between `user` and `post`
        ),
        'm:m' => array(
            // many-to-many relation between `post` and `tag`
            // there must also be 'm:m' definition at the side of `tag`
            'tag' => array(
                'name'      => 'postId',
                'cascade'   => true,
            ),
        ),
    ),

The following describes the rule explanation of table schema array.

+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| Name                          | Default                       | Explanation                                                           |
+===============================+===============================+=======================================================================+
| ``{field_name}``              |                               | The field name (a valid field name for the underlying database        |
|                               |                               | table). Use the field name "slug" for the sluggable field.            |
|                               |                               | Generally, you don’t have to define primary key field. It will be     |
|                               |                               | added by default using the field name "id" with the following rule:   |
|                               |                               |                                                                       |
|                               |                               |   - type: integer                                                     |
|                               |                               |   - autoinc: true                                                     |
|                               |                               |   - null: false                                                       |
|                               |                               |   - unsigned: true                                                    |
|                               |                               |                                                                       |
|                               |                               | However, if you want to use other field type (e.g, string type) and   |
|                               |                               | rule for your primary key, you must define the field here using your  |
|                               |                               | own rule, for example,                                                |
|                               |                               | ``'id' => array('type' => 'string', 'length' => 64, 'null' => false)``|
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``{field_name}.type``         |                               | The data type (See `Data Type Mapping Matrix                          |
|                               |                               | <#data-type-mapping-matrix>`_ for the underlying database)            |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``{field_name}.length``       | ``255 for string``            | The length of the field                                               |
|                               | ``11 for int/integer``        |                                                                       |
|                               | ``1 for boolean``             |                                                                       |
|                               |                               |                                                                       |
|                               | ``array(0, 0) for decimal``   |                                                                       |
|                               |                               |                                                                       |
|                               | ``array(0, 0) for float``     |                                                                       |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``{field_name}.null``         | true                          | Allow ``NULL`` or ``NOT NULL``                                        |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``{field_name}.default``      |                               | The default value for the field                                       |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``{field_name}.unsigned``     | false                         | Unsigned or signed                                                    |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``{field_name}.autoinc``      | false                         | Auto-increment field                                                  |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``{field_name}.unique``       | false                         | Unique index for the field                                            |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``options``                   |                               | The array of the table options                                        |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``options.pk``                | ``array('id')``               | One or more primary key field names. The default primary key field    |
|                               |                               | name is "id". If you want to use a different name rather than "id"    |
|                               |                               | (e.g., user_id, post_id), you can define it here. The default primary |
|                               |                               | key field definition is                                               |
|                               |                               |                                                                       |
|                               |                               |   - type: integer                                                     |
|                               |                               |   - autoinc: true                                                     |
|                               |                               |   - null: false                                                       |
|                               |                               |   - unsigned: true                                                    |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``options.timestamps``        | true                          | Include 3 datetime fields - ``created``, ``updated``, ``deleted``;    |
|                               |                               | override to ``_optons.timestamps``                                    |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``options.charset``           | utf8                          | The charset for the table; override to ``_options.charset``           |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``options.collate``           | utf8_unicode_ci               | The charset for the table; override to ``_options.collate``           |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``options.engine``            | InnoDB                        | The charset for the table; override to ``_options.engine``            |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``options.unique``            |                               | Unique index for composite fields                                     |
|                               |                               |                                                                       |
|                               |                               | ``array('keyName' => array('fieldName1', 'fieldName2'))``             |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``1:m``                       |                               | One-to-Many relationship; if you define this, there must be ``m:1``   |
|                               |                               | definition at the many-side table                                     |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``1:m.{table_name}``          |                               | The name of the many-side table as array key with the following       |
|                               |                               | options.                                                              |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``1:m.{table_name}.name``     | table_name + "_id"            | The foreign key field name in the many-side table                     |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``1:m.{table_name}.unique``   | false                         | Unique index for the foreign key field                                |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``1:m.{table_name}.default``  | null                          | Default value for the foreign key field                               |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``1:m.{table_name}.cascade``  | false                         | - ``true`` for ``ON DELETE CASCADE``                                  |
|                               |                               | - ``false`` for ``ON DELETE RESTRICT``                                |
|                               |                               | - ``null`` for ``ON DELETE SET NULL``                                 |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``m:1``                       |                               | Array of table names that are reverse of one-to-many relations to     |
|                               |                               | ``1:m``                                                               |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``m:1.{table_name}``          |                               | The name of the one-side table                                        |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``m:m``                       |                               | Many-to-many relationship; if you define this, there must be ``m:m``  |
|                               |                               | definition at the other many-side table                               |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``m:m.{table_name}``          |                               | The name of the reference table                                       |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``m:m.{table_name}.table``    |                               | Optional pivot table name; if it is not defined, the two table names  |
|                               |                               | will be used concatenating with ``_to_`` such as ``table1_to_table2`` |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``m:m.{table_name}.name``     | table_name + "_id"            | The reference field name in the pivot table                           |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``m:m.{table_name}.cascade``  | false                         | - ``true`` for ``ON DELETE CASCADE``                                  |
|                               |                               | - ``false`` for ``ON DELETE RESTRICT``                                |
|                               |                               | - ``null`` for ``ON DELETE SET NULL``                                 |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``1:1``                       |                               | One-to-One relationship                                               |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``1:1.{table_name}``          |                               | The name of the reference table                                       |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``1:1.{table_name}.name``     | table_name + "_id"            | Foreign key field name that will be included in the table; it maps to |
|                               |                               | the primary key of the reference table                                |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+
| ``1:1.{table_name}.cascade``  | false                         | - ``true`` for ``ON DELETE CASCADE``                                  |
|                               |                               | - ``false`` for ``ON DELETE RESTRICT``                                |
|                               |                               | - ``null`` for ``ON DELETE SET NULL``                                 |
+-------------------------------+-------------------------------+-----------------------------------------------------------------------+

Data Type Mapping Matrix
------------------------

The following table shows the matrix that contains the mapping information for how a specific type is mapped to the database.

+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| Type Name          | MySQL Data Type         | Explanation                                                                            |
+====================+=========================+========================================================================================+
| smallint           | SMALLINT                | Maps and converts 2-byte integer values.                                               |
|                    |                         |                                                                                        |
|                    |                         | - Unsigned integer with a range of **0** to **65535**                                  |
|                    |                         | - Signed integer with a range of **−32768** to **32767**                               |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| int/integer        | INT                     | Maps and converts 4-byte integer values.                                               |
|                    |                         |                                                                                        |
|                    |                         | - Unsigned integer with a range of **0** to **4294967295**                             |
|                    |                         | - Signed integer with a range of **−2147483648** to **2147483647**                     |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| bigint             | BIGINT                  | Maps and converts 8-byte integer values.                                               |
|                    |                         |                                                                                        |
|                    |                         | - Unsigned integer with a range of **0** to **18446744073709551615**                   |
|                    |                         | - Signed integer with a range of **−9223372036854775808** to **9223372036854775807**   |
|                    |                         |                                                                                        |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| decimal            | NUMERIC(p,s)            | Maps and converts numeric data with fixed (exact) point precision.                     |
|                    |                         | The precision (p) represents the number of significant digits that                     |
|                    |                         | are stored for values. The scale (s) represents the number of digits                   |
|                    |                         | that can be stored following the decimal point.                                        |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| float              | DOUBLE(p,s)             | Maps and converts numeric data with floating (approximate) point                       |
|                    |                         | precision. The precision (p) represents the number of significant                      |
|                    |                         | digits that are stored for values. The scale (s) represents the                        |
|                    |                         | number of digits that can be stored following the decimal point.                       |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| string             | VARCHAR                 | Maps and converts string data with a maximum length.                                   |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| char               | CHAR                    | Maps and converts string data with a fixed length.                                     |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| binary             | VARBINARY               | Maps and converts binary string data with a maximum length.                            |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| text               | TEXT                    | Maps and converts string data without a maximum length.                                |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| blob               | BLOB                    | Maps and converts binary string data withou a maximum length.                          |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| array              | TEXT                    | Maps and converts array data based on PHP serialization. It uses                       |
|                    |                         | serialization to represent an exact copy of your array as string                       |
|                    |                         | the database and values retrieved from the database are always                         |
|                    |                         | converted to PHP’s array type using deserialization.                                   |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| json               | TEXT                    | Maps and converts array data based on PHP's JSON encoding functions.                   |
|                    |                         | It stores a valid UTF8 encoded JSON format string and values received                  |
|                    |                         | from the database are always the return value from PHP's                               |
|                    |                         | ``json_decode()`` function.                                                            |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| boolean            | TINYINT(1)              | Maps and converts boolean data. If you know that the data to be stored                 |
|                    |                         | always is a boolean (``true`` or ``false``), you should consider                       |
|                    |                         | using this type.                                                                       |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| date               | DATE                    | Maps and converts date data without time and timezone information                      |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| datetime           | DATETIME                | Maps and converts date and time data without timezone information                      |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+
| time               | TIME                    | Maps and converts time data without date and timezone information                      |
+--------------------+-------------------------+----------------------------------------------------------------------------------------+

Loading Your Schema
-------------------

Assuming that you have created your application database and you have defined your schema in ``/db/schema.php`` for the database, you can load or import the database using LucidFrame console tool by running the command: ::

    $ php lucidframe schema:load

It will import the database defined under the namespace "default". If you want to load another database defined under a different namespace, for example "sample", you just need to provide the namespace in the command such as ::

    $ php lucidframe schema:load sample

Exporting Your Schema
---------------------

You can export or dump your database loaded by your schema definition. The LuicdFrame console command ``schema:export`` will help you. ::

    $ php lucidframe schema:export

It will export the database of the namespace "default" in the directory ``/db/generated/`` as ``.sql`` file. You can also provide the namespace in the command such as ::

    $ php lucidframe schema:export sample

Managing Schema Changes
-----------------------

As of version 2.2.0, PHPLucidFrame provides a way to manage schema changes. It helps you to programmatically deploy new versions of your database schema easily in a standardized way.

Let’s say an example, we use the sample database as our default and we are adding a new field ``wechatUrl`` in the table ``social_profile``. Let's edit the file ``/db/schema.sample.php`` ::

    'social_profile' => array(
        'facebookUrl'  => array('type' => 'string', 'length' => 100, 'null' => true),
        'twitterUrl'   => array('type' => 'string', 'length' => 100, 'null' => true),
        'gplusUrl'     => array('type' => 'string', 'length' => 100, 'null' => true),
        'linkedinUrl'  => array('type' => 'string', 'length' => 100, 'null' => true),
        'wechatUrl'    => array('type' => 'string', 'length' => 100, 'null' => true), // <- add this
        '1:1' => array(
            // one-to-one relation between `social_profile` and `user`
            // no need to define 1:1 at the side of `user`
            'user' => array(
                'name'      => 'uid',
                'cascade'   => true,
            ),
        ),
    ),

Then, run ``schema:diff sample`` and it will generate a file with extension **sqlc** in ``/db/version/sample`` ::

    $ php lucidframe schema:diff sample
    PHPLucidFrame 2.2.0 by Sithu K.

    ./db/version/sample/20170406223436.sqlc is exported.
    Check the file and run `php lucidframe schema:update sample`
    Done.

You can open that **sqlc** file and check its content. Finally, you can run ``schema:update sample`` to apply this changes in your underlying database. ::

    $ php lucidframe schema:update sample
    PHPLucidFrame 2.2.0 by Sithu K.

    IMPORTANT! Backup your database before executing this command.
    Some of your data may be lost. Type "y" or "yes" to continue: y

    Executing 20170406223436

    Your schema has been updated.
    Done.

The following example will show you in another scenario where renaming the fields. Let’s say we are remove ``Url`` from all field names of the table ``social_profile`` such as ::

    'social_profile' => array(
        'facebook'  => array('type' => 'string', 'length' => 100, 'null' => true),
        'twitter'   => array('type' => 'string', 'length' => 100, 'null' => true),
        'gplus'     => array('type' => 'string', 'length' => 100, 'null' => true),
        'linkedin'  => array('type' => 'string', 'length' => 100, 'null' => true),
        'wechat'    => array('type' => 'string', 'length' => 100, 'null' => true),
        '1:1' => array(
            // one-to-one relation between `social_profile` and `user`
            // no need to define 1:1 at the side of `user`
            'user' => array(
                'name'      => 'uid',
                'cascade'   => true,
            ),
        ),
    ),

Again, run ``schema:diff sample`` and you will be confirmed for renaming fields. ::

    $ php lucidframe schema:diff sample
    PHPLucidFrame 2.2.0 by Sithu K.


    Type "y" to rename or type "n" to drop/create for the following fields:

    Field renaming from `facebookUrl` to `social_profile.facebook`: y
    Field renaming from `twitterUrl` to `social_profile.twitter`: y
    Field renaming from `gplusUrl` to `social_profile.gplus`: y
    Field renaming from `linkedinUrl` to `social_profile.linkedin`: y
    Field renaming from `wechatUrl` to `social_profile.wechat`: y

    ./db/version/sample/20170406224852.sqlc is exported.
    Check the file and run `php lucidframe schema:update sample`
    Done.

Now you can see there are two **sqlc** files in the directory ``/db/version/sample``. Then, as suggested above, you just need to run ``schema:update sample`` to update your database schema. ::

    $ php lucidframe schema:update sample
    PHPLucidFrame 2.2.0 by Sithu K.

    IMPORTANT! Backup your database before executing this command.
    Some of your data may be lost. Type "y" or "yes" to continue: y

    Executing 20170406224852

    Your schema has been updated.
    Done.

That’s it! You now have two version files of your schema changes stored in ``/db/version/sample``.

If you are of team of developers and your team uses version control system, those **sqlc** files should be tracked in your VCS to make it available to other developers in the team. When they get the files, they simply needs to run the command ``schema:update`` to synchronize their databases as yours.
