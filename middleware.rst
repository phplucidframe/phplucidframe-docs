Middleware
==========

As of version 2.0, PHPLucidFrame added middleware support. You can handle a HTTP request through middleware. You can define middlewares to execute before or after a HTTP request. For example, you can include a middleware that authenticates a user. If the user is not authenticated, the middleware will redirect the user to the login page, otherwise, it will allow the request to proceed. All middlewares should be located and defined in the ``/app/middleware`` directory.

Before Middleware
-----------------

A `before` middleware is an event executed before a request. Here is its definition syntax: ::

    _middleware(function () {
        // Do something before the page request
        // for example, checking bearer token from HTTP Authorization
        // $authorization = _requestHeader('Authorization')
    });

    _middleware(function () {
        // Do something before the page request
    }, 'before');  // 'before' is optional and default

After Middleware
----------------

An `after` middleware is an event executed after a request. Here is its definition syntax: ::

    _middleware(function () {
        // Do something at the end of the page request
    }, 'after');

.. note::
    - You can check some example middlewares at `<https://github.com/phplucidframe/phplucidframe/blob/master/app/middleware/example.php>`_

Let's create a middleware ``/app/middleware/auth.php`` that contains the following code: ::

    <?php

    if (route_start('admin')) {
        /**
         * This is an example middleware running before every page request
         * that route starts with "admin"
         */
        _middleware(function () {
            // set flash message and redirect to the login page if user is anonymous
            if (auth_isAnonymous()) {
                flash_set('You are not authenticated. Please log in.', '', 'error');
                _redirect('login');
            }

            // otherwise, it will proceed the request
        });
    }

It will be executed on every page that URI starts with "admin" and it verifies the user is authenticated. If the user is not authenticated, it will redirect to the login page.
