User Authentication
===================

User authentication is one of the critical parts of almost every web application. PHPLucidFrame provides a flexible way of identifying and checking user authentication. There is a configuration variable available in ``/inc/config.php`` - ``$lc_auth``. You can set up your authentication components corresponding to your user data table to load it. ::

    /*
     * Auth Module Configuration
     */
    # $lc_auth: configuration for the user authentication
    # This can be overidden by defining $lc_auth in /inc/site.config.php
    $lc_auth = array(
        'table' => '',  // table name, for example, user
        'fields' => array(
            'id' => '',     // PK field name, for example, user_id
            'role' => '',    // User role field name, for example, user_role
        ),
        'perms' => array(
            // allowed permissions
        ),
        'block' => array(
            // blocked permissions
        ),
        /* // for example
            'perms' => array( // editor is not allowed for post deletion
                'editor' => array('post-list', post-add', 'post-edit'),
                'admin' => array('post-list', post-add', 'post-edit', 'post-delete')
            ),
            'block' => array( // editor is denied for post deletion
                'editor' => array('post-delete'),
                'admin' => array() // no action is blocked for admin
            )
            // you don't have to use both - "perms" and "block",
            // according to the above configuration, both are same concept
            // just pick up the one that suits to your need
        */
    );

Hashing Passwords
-----------------

Hashing passwords are always required in every secured web application. When user data are inserted in user registration process, it is advisable to hash user input password using the core function ``_encrypt()``. ::

    $theEncryptedPassword = _encrypt($theUserInputPassword);

When validating user input password in log-in process, you should also use ``_encrypt()`` to check it against the encrypted password stored in the database.

Logging In and Logging Out
--------------------------

Upon user login form handling of the user inputs, you could query and get the authenticate user id and then you could pass it to ``auth_create()``. ::

    auth_create($theUserID);

It will load all of data from the table configured in ``$lc_auth`` for the given authenticated user and make it available as an object accessible from ``_app('auth')``. You can also get it from ``auth_get()``.

If you already have all of your user data, you can pass it to ``auth_create()`` as second argument. It is useful for multiple table joined result of your user data. The configuration in ``$lc_auth`` works only for one table. ::

    auth_create($theUserID, $theUserDataObject);

Besides the user data result available as properties in the returning auth object from ``_app('auth')``, it also contains session id, token and permissions:

- ``_app('auth')->sessId`` - The session id for the current session returning from ``session_id()``.
- ``_app('auth')->token`` - The generated token of the authentication object
- ``_app('auth')->permissions`` - The permissions allowed according to the defined role and perms in ``$lc_auth``.
- ``_app('auth')->blocks`` - The permissions blocked according to the defined role and block in ``$lc_auth``.

Sometimes, you may need to update the auth object for the user data changes, for example, user’s name changed. For that case, you can use ``auth_set()`` by passing the updated user data object. ::

    $auth = _app('auth');
    $auth->fullname = $theUpdatedFullName;
    auth_set($auth);

The function ``auth_clear()`` is available for use to destroy the current session and to allow user signed out.

.. note::
    - For login sample code, see `https://github.com/phplucidframe/admin-boilerplate/tree/master/login <https://github.com/phplucidframe/admin-boilerplate/tree/master/login>`_.
    - For logout sample code, see `https://github.com/phplucidframe/admin-boilerplate/blob/master/logout/index.php <https://github.com/phplucidframe/admin-boilerplate/blob/master/logout/index.php>`_.

Checking Anonymous User or Logged-in User
-----------------------------------------

PHPLucidFrame provides an easy way to check if the user is anonymous or logged-in. ::

    if (auth_isAnonymous()) {
        // do something
    }

    // or

    if (auth_isLoggedIn()) {
        // do something
    }

Access Control with Permissions and User Roles
----------------------------------------------

You might assign specific permissions to each user role in the configuration ``$lc_auth['fields']['role']`` and ``$lc_auth['perms']`` to fine tune the security, use and administration of the site.

PHPLucidFrame allows you to check the authenticate user is belong to a particular user role by using ``auth_role()`` or multiple user roles by using ``auth_roles()``, for example, ::

    if (auth_role('editor')) {
        // if user is editor, do something
    } else {
        // redirect to the access-denied page
    }

    if (auth_roles('admin', 'editor')) {
        // if user is admin or editor, do something
    } else {
        // redirect to the access-denied page
    }

And it also allows you to check the user is accessible to a particular page or section by using ``auth_access()``, for example, ::

    if (auth_access('content-delete')) {
        // if user has permission to delete content, do content delete
    }

    if (auth_block('content-delete')) {
        // if user is denied to delete content
    }

You could define custom wrapper functions in ``/app/helpers/auth_helper.php`` for checking the user roles, for example, ::

    /**
     * Check if the current logged-in user is admin or not
     */
    function auth_isAdmin() {
        return auth_role('admin');
    }

    /**
     * Check if the current logged-in user is editor or not
     */
    function auth_isEditor() {
        return auth_role('editor');
    }

You can also check the URL route path or name to prevent the user from being accessed to a page or a function. You can implement this as middleware. The following middleware will be invoked in all routes under ``/admin`` except ``/admin/login`` and ``/admin/logout`` ::

    // app/middleware/auth.php

    $baseDir = _cfg('baseDir'); // Let says _cfg('baseDir') is '/admin'

    _middleware(function () {
        if (auth_isAnonymous()) {
            flash_set('You are not authenticated. Please log in.', '', 'error');
            _redirect$baseDir . '/login');
        }
    })->on('startWith', $baseDir)
        ->on('except', array($baseDir . 'login', $baseDir . 'logout'));

The following example is to allow post delection for admin only. ::

    // app/middleware/auth.php

    _middleware(function () {
        if (!auth_role('admin')) {
            _page403();
        }
    })->on('equal', 'post_delete');

The following example is to allow users section (all routes containing a URI segment "users") for admin only. ::

    _middleware(function () {
        if (!auth_role('admin')) {
            _page403();
        }
    })->on('contain', 'users');
