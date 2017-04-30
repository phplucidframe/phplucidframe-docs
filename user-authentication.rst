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

You can update it in ``config.php`` (copy of ``config.default.php``) or you can copy it to ``/inc/site.config.php`` (copy of ``site.config.default.php``) and override it.

Hashing Passwords
-----------------

Hashing passwords are always required in every secured web application. When user data are inserted in user registration process, it is advisable to hash user input password using the core function ``_encrypt()``. ::

    $theEncryptedPassword = _encrypt($theUserInputPassword);

When validating user input password in log-in process, you should also use ``_encrypt()`` to check it against the encrypted password stored in the database.

Logging In and Logging Out
--------------------------

Upon user login form handling of the user inputs, you could query and get the authenticate user id and then you could pass it to ``auth_create()``. You are suggested to check the example codes (``/app/admin/login``) in the release. ::

    auth_create($theUserID);

It will load all of data from the table configured in ``$lc_auth`` for the given authenticated user and make it available in the global object ``$_auth``. You can also get it from ``auth_get()``.

If you already have all of your user data, you can pass it to ``auth_create()`` as second argument. It is useful for multiple table joined result of your user data. The configuration in ``$lc_auth`` works only for one table. ::

    auth_create($theUserID, $theUserDataObject);

Besides the user data result available as properties in the returning ``$_auth`` object, it also contains session id, timestamp and permissions:

- ``$_auth->sessId`` - The session id for the current session returning from ``session_id()``.
- ``$_auth->timestamp`` - The created date/time of this authentication object
- ``$_auth->permissions`` - The permissions allowed according to the defined role and perms in ``$lc_auth``.
- ``$_auth->blocks`` - The permissions blocked according to the defined role and block in ``$lc_auth``.

Sometimes, you may need to update the ``$_auth`` object for the user data changes, for example, user’s name changed. For that case, you can use ``auth_set()`` by passing the updated user data object. ::

    $_auth->fullname = $theUpdatedFullName;
    auth_set($_auth);

The function ``auth_clear()`` is available for use to destroy the current session and to allow user signed out. You can check the sample code ``/app/admin/logout`` in the release.

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

PHPLucidFrame allows you to check the authenticate user is belong to a particular user role by using ``auth_role()`` and allows you to check the user is accessible to a particular page or section by using ``auth_access()``, for example, ::

    if (auth_role('editor')) {
        // if user is editor, do something
    } else {
        // redirect to the access-denied page
    }

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

You can also check the URL routing path to not allow user access to a page. You are suggested to check the code sample /app/admin/inc/authenticate.php in the release. ::

    // Let's say the current URL is http://www.example.com/admin/post/edit/99
    if (auth_role('editor') && _arg(1) == 'post' && _arg(2) == 'edit') {
        // redirect to the access-denied page
        // the editor is not allowed to access this page.
    }
