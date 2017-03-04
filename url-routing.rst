URL Routing
===========

PHPLucidFrame typically requires ``mod_rewrite`` enabled on web server. As per the default configuration in ``/inc/route.config.php``, the app home page is by default set to ``/home`` which is mapped to the directory ``/app/home/`` and it is accessible via ``http://localhost/phplucidframe`` or ``http://www.example.com``. You can change it according to your need. ::

    // inc/route.config.php
    /**
     * The named route example `lc_home`
     * This will be overridden to `$lc_homeRouting` in /inc/config.php
     * If this is not defined here, `$lc_homeRouting` will be used
     * However, `$lc_homeRouting` is deprecated in 1.10 and it will be removed in 2.0
     * This is a recommended place to define routings if necessary
     */
    route('lc_home')->map('/', '/home');

PHPLucidFrame is already designed for friendly URL without any custom route defined. Let’s say for example, you have a page ``/app/post/index.php`` and URL ``http://www.example.com/post/1/edit``. The URL will be mapped to the file ``/app/post/index.php`` by passing 2 arguments - ``1`` and ``edit``. ::

    // http://www.example.com/post/1/edit => /app/post/index.php
    echo _arg(1); // 1
    echo _arg(2); // edit
    echo _url('post', array(1, 'edit')); // http://www.example.com/post/1/edit
