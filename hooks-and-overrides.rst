Hooks And Overrides
===================

PHPLucidFrame allows you to define hooks and overrides so that you can interact with core and enhance the functionality of core.

Hooks
-----

**Hooks** allow you to interact with the LucidFrame core. A hook is a PHP function which has a defined set of parameters and a specified result type. It is executed upono certain condition. It is very similar to event observers. The available hooks to implement are explained here.

__script()
^^^^^^^^^^

It can be defined in ``/app/helpers/utility_helper.php`` and executed after the core function ``_script()`` in ``/lib/helpers/utility_helpers.php`` runs. Use this to make global PHP variables available to the global Javascript variable ``LC``.

__getLang()
^^^^^^^^^^^

Get the language to process. Read ``lang`` from query string. Basically, it is useful for admin content management by language.

It can be defined in ``/app/helpers/utility_helper.php`` and executed after the core function ``_getLang()`` in ``/lib/helpers/utility_helpers.php`` runs.

__validation_messages()
^^^^^^^^^^^^^^^^^^^^^^^

Define customer validation messages by rule as key. It can be defined in ``/app/helpers/validatio_helper.php``. For example, ::

    /**
    * Hook for custom validation messages
    * @return string[]
    */
    function __validation_messages()
    {
        return array(
            # rule => message
            'validate_emailRetyped'     => 'Your re-typed email address does not match.',
            'validate_confirmPassword'  => '"%s" does not match.',
        );
    }

db_insert_<table_name>($table, $data=array(), $useSlug=true)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Customize database insert operation for a specific table, for example, if you define ``db_insert_post()`` for the ``post`` table, it will be automatically executed when you call ``db_insert('post', …)``.

It can be defined in ``/app/helpers/db_helper.php`` and executed when ``db_insert()`` in ``/lib/helpers/db_helper.mysqli.php`` is called.

db_update_<table_name>($table, $data=array(), $useSlug=true)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Customize database update operation for a specific table, for example, if you define ``db_update_post()`` for the ``post`` table, it will be automaticaclly executed when you call ``db_update('post', …)``.

It can be defined in ``/app/helpers/db_helper.php`` and executed when ``db_update()`` in ``/lib/helpers/db_helper.mysqli.php`` is called.

db_delete_<table_name>($table, $data=array(), $useSlug=true)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Customize database delete operation of **a single record** for a specific table, for example, if you define ``db_delete_post()`` for the ``post`` table, it will be automatically executed when you call ``db_delete('post', …)``.

It can be defined in ``/app/helpers/db_helper.php`` and executed when ``db_delete()`` in ``/lib/helpers/db_helper.mysqli.php`` is called.

db_delete_multi_<table_name>($table, $data=array(), $useSlug=true)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Customize database delete operation of **multiple records** for a specific table, for example, if you define ``db_delete_multi_post()`` for the ``post`` table, it will be automatically executed when you call ``db_delete('post', …)``.

It can be defined in ``/app/helpers/db_helper.php`` and executed when ``db_delete_multi()`` in ``/lib/helpers/db_helper.mysqli.php`` is called

Overrides
---------

**Overrides** allow you to rewrite some functionalities of the LucidFrame core. An override is a PHP function which has a defined set of parameters and a specified result type. The available override to implement are explained here.

__flush($buffer, $mode)
^^^^^^^^^^^^^^^^^^^^^^^
Overrides the core ``ob_start`` callback function ``_flush()`` in ``/lib/helpers/utility_helpers.php``. You can use this function to manipulate the output buffer before sending it to browser. It can be defined in ``/app/helpers/utility_helper.php``.

__metaSeoTags($tags)
^^^^^^^^^^^^^^^^^^^^

Overrides the core function ``_metaSeoTags()`` in ``/lib/helpers/utility_helpers.php``. You can use this function to manipulate the output buffer before sending it to browser.  It can be defined in ``/app/helpers/utility_helper.php``.

auth_create($id, $data = null)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``auth_create()`` in ``/lib/helpers/auth_helper.php``. Create user authentication object. It can be defined ``/app/helpers/auth_helper.php``.

auth_getUserInfo($id)
^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``auth_getUserInfo()`` in ``/lib/helpers/auth_helper.php``. Get user record from db to create auth session. It can be defined ``/app/helpers/auth_helper.php``.

auth_role($role)
^^^^^^^^^^^^^^^^

Overrides the function ``auth_role()`` in ``/lib/helpers/auth_helper.php``. Check if the authenticate user has the specific user role. It can be defined in ``/app/helpers/auth_helper.php``.

auth_roles($role, ....)
^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``auth_roles()`` in ``/lib/helpers/auth_helper.php``. Check if the authenticated user has the specific user role(s). It can be defined in ``/app/helpers/auth_helper.php``.


auth_permissions($role)
^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``auth_permissions()`` in ``/lib/helpers/auth_helper.php``. Get the permissions of a particular role. It can be defined in ``/app/helpers/auth_helper.php``.


auth_can($perm)
^^^^^^^^^^^^^^^^^^

Overrides the function ``auth_can()`` in ``/lib/helpers/auth_helper.php``. Check if the authenticate uses has a particular permission. It can be defined in ``/app/helpers/auth_helper.php``.

flash_set($msg, $name = '', $class = 'success')
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``flash_set()`` in ``/lib/helpers/session_helper.php``. Set the flash message in session. It can be defined in ``/app/helpers/session_helper.php``.

flash_get($name = '', $class = 'success')
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``flash_get()`` in ``/lib/helpers/session_helper.php``.  Get the flash message from session and then delete it. It can be defined in ``/app/helpers/session_helper.php``.

_pr($input, $pre=true)
^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``_pr()`` in ``/lib/helpers/utility_helper.php``. Convenience method for ``print_r`` to display information about a variable in a way that's readable by humans. It can be defined in ``/app/helpers/utility_helper.php``

_fstr($value, $glue = ', ', $lastGlue = 'and')
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``_fstr()`` in ``/lib/helpers/utility_helper.php``. Format a string. It can be defined in ``/app/helpers/utility_helper.php``.

_fnum($value, $decimals = 2, $unit = '')
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``_fnum()`` in ``/lib/helpers/utility_helper.php``. Format a number. It can be defined in ``/app/helpers/utility_helper.php``.

_fnumSmart($value, $decimals = 2, $unit = '')
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``_fnumSmart()`` in ``/lib/helpers/utility_helper.php``. Format a number in a smarter way, i.e., decimal places are omitted where necessary. It can be defined in ``/app/helpers/utility_helper.php``.

_fdate($date, $format = '')
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``_fdate()`` in ``/lib/helpers/utility_helper.php``. Format a date. It can be defined in ``/app/helpers/utility_helper.php``.

_fdatetime($dateTime, $format = '')
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``_fdatetime()`` in ``/lib/helpers/utility_helper.php``. Format a date/time. It can be defined in ``/app/helpers/utility_helper.php``.

_ftimeAgo($time, $format = 'M j Y')
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``_ftimeAgo()`` in ``/lib/helpers/utility_helper.php``. Display elapsed time in wording. It can be defined in ``/app/helpers/utility_helper.php``.

_msg($msg, $class = 'error', $return = null, $display = 'display:block')
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``_msg()`` in ``/lib/helpers/utility_helper.php``. Print or return the message formatted with HTML. It can be defined in ``/app/helpers/utility_helper.php``.

_randomCode($length=5, $letters = array())
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``_randomCode()`` in ``/lib/helpers/utility_helper.php``. Generate a random string from the given array of letters. It can be defined in ``/app/helpers/utility_helper.php``.

_slug($string, $table = '', $condition = null)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Overrides the function ``_slug()`` in ``/lib/helpers/utility_helper.php``. Generate a slug of human-readable keywords. It can be defined in ``/app/helpers/utility_helper.php``.
