Database Seeding
================

Database seeding is a very useful feature to initialize your database with default data or sample data. The seeding feature is available since PHPLucidFrame 1.14.

By default, LucidFrame has two directories - ``/db/seed/default`` and ``/db/seed/sample``. If you have only one database for your application, ``/db/seed/default`` is the right folder where you should create your seeding files. ``/db/seed/sample`` has the sample seeding files for the sample database which would be the namespace ``sample``.

Seeding Syntax
--------------

The following is seeding file syntax. ::

    <?php
    use LucidFrame\Core\Seeder; // required ony if you have any reference field to insert and to use Seeder::setReference()

    // Must return the array
    return array(
        'order' => 1, // Execution order: lower number will be executed in greater priority, especially for reference fields
        'record-1' => array( // "record-1" can be used for the reference field in the other file
            // a record is an array of field and value
            // field  => value
            'field1'  => 'value2',
            'field2'  => 'value2',
            'field3'  => Seeder::setReference('record-key-from-previously-executed-seed'),
        ),
        'record-2' => array(
            'field1'  => 'value2',
            'field2'  => 'value2',
            'field3'  => Seeder::setReference('record-key-from-previously-executed-seed'),
        ),
    );

The record key ``record-1`` should be unique for each record and is to be used for reference field in other seeding file. Typically it could be the format ``{table-name}-{number}``, e.g., ``category-1``, ``category-2``, ``post-1``, ``post-2``, etc. However, it is just a naming convention and it does not tie to any rule.

Seeding Example
---------------

Let’s say we have 4 tables to be populated with a set of data - ``category``, ``post``, ``tag`` and ``post_to_tag``. The relationship between category and post is one-to-many relationship; ``post`` and ``tag` have many-to-many relationship. So, there must be 4 seeding PHP files with the names of respective table names. ::

    /db
        /default
            |-- category.php
            |-- post.php
            |-- post_to_tag.php
            |-- tag.php

The followings are example contents for each seeding file.

**category.php** ::

    <?php
    // Data for the table "category"
    return array(
        'order' => 1, // Execution order: this file will be executed firstly
        'category-1' => array( // a record is an array of field and value
            // field => value
            'slug'     => 'technology',
            'catName'  => 'Technology',
        ),
        'category-2' => array(
            'slug'     => 'framework',
            'catName'  => 'Framework',
        ),
    );

**post.php** ::

    <?php
    // Data for the table "post"
    use LucidFrame\Core\Seeder; // required if you have any reference field to insert

    return array(
        'order' => 2, // this file will be executed after seeding is executed for the table "category"
        'post-1' => array(
            'catId'     => Seeder::setReference('category-1'), // reference field that will be inserted with category id that will be created by the previous category seeding execution
            'slug'      => 'welcome-to-the-lucidframe-blog',
            'postTitle' => 'Welcome to the LucidFrame Blog',
            'postBody'  => 'LucidFrame is a mini application development framework - a toolkit for PHP developers. It provides logical structure and several helper utilities for web application development. It uses a module architecture to make the development of complex applications simplified.',
        ),
    );

**tag.php** ::

    <?php
    // Data for the table "tag"
    return array(
        'order' => 3, // this file will be executed in third
        'tag-1' => array(
            'slug'    => 'php',
            'tagName' => 'PHP',
        ),
        'tag-2' => array(
            'slug'    => 'mysql',
            'tagName' => 'MySQL',
        ),
    );

**post_to_tag.php** ::

    <?php
    // Data for the many-to-many table "post_to_tag"
    use LucidFrame\Core\Seeder;

    return array(
        'order' => 4, // this file will be executed lastly in all of four files
        'post-to-tag-1' => array(
            'postId'    => Seeder::setReference('post-1'), // reference field to the table "post"
            'tagId'     => Seeder::setReference('tag-1'),  // reference field to the table "tag"
        ),
        'post-to-tag-2' => array(
            'postId'    => Seeder::setReference('post-1'),
            'tagId'     => Seeder::setReference('tag-2'),
        ),
    );

.. note::
    - You can check the example seeding files at `/db/seed/sample <https://github.com/phplucidframe/phplucidframe/blob/master/db/seed/sample>`_.

Executing Seeds
---------------

When you have defined your seeding files, you can load your seeding data into your database using LucidFrame console tool by running the following command: ::

    $ php lucidframe db:seed

If your database has a different namespace other than "default", you can also provide the namespace in the command such as ::

    $ php lucidframe db:seed sample
