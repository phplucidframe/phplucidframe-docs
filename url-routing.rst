URL Routing
===========

PHPLucidFrame typically requires ``mod_rewrite`` enabled on web server. As per the default configuration in ``/inc/route.config.php``, the app home page is by default set to ``/home`` which is mapped to the directory ``/app/home/`` and it is accessible via ``http://localhost/phplucidframe`` or ``http://www.example.com``. You can change it according to your need. ::

    // inc/route.config.php
    /**
     * The named route example `lc_home`
     */
    route('lc_home')->map('/', '/home');

PHPLucidFrame is already designed for friendly URL without any custom route defined. Let’s say for example, you have a page ``/app/post/index.php`` and URL ``http://www.example.com/post/1/edit``. The URL will be mapped to the file ``/app/post/index.php`` by passing 2 arguments - ``1`` and ``edit``. ::

    // http://www.example.com/post/1/edit => /app/post/index.php
    echo _arg(1); // 1
    echo _arg(2); // edit
    echo _url('post', array(1, 'edit')); // http://www.example.com/post/1/edit

Custom Routes
-------------

If you are not enough with PHPLucidFrame basic routing and if you need your own custom routes, you can easily define them in ``/inc/route.config.php``. The following example shows the route key ``lc_blog_show`` of the route path ``/blog/{id}/{slug}`` mapping to ``/app/blog/show/index.php`` by passing two arguments ``id`` and ``slug`` with the requirements of ``id`` to be digits and ``slug`` to be alphabets/dashes/underscores. ::

    // inc/route.config.php
    /**
     * The named route example `lc_blog_show`
     * This is an example routed to the directory `/app/blog/show`
     */
    route('lc_blog_show')->map('/blog/{id}/{slug}', '/blog/show', 'GET', array(
        'id'    => '\d+', # {id} must be digits
        'slug'  => '[a-zA-Z\-_]+' # {slug} must only contain alphabets, dashes and underscores
    ));

Then you can get the argument values from ``_get('id')`` and ``_get('slug')`` in ``/app/blog/show/index.php``. ::

    // app/blog/show/index.php
    $id   = _get('id');
    $slug = _get('slug');

Here is the custom routing configuration syntax: ::

    route($name)->map($path, $to, $method = 'GET', $patterns = null)

Argument for ``route()``:

+-----------------+----------------+-----------------------------------------------------------------------------------------------+
| Argument Name   | Type           | Description                                                                                   |
+=================+================+===============================================================================================+
| ``$name``       | string         | Any unique route name to the mapped $path                                                     |
+-----------------+----------------+-----------------------------------------------------------------------------------------------+

Arguments for ``map()``:

+-----------------+----------------+-----------------------------------------------------------------------------------------------+
| Argument Name   | Type           | Description                                                                                   |
+=================+================+===============================================================================================+
| ``$path``       | string         | URL path with optional dynamic variables such as ``/post/{id}/edit``                          |
+-----------------+----------------+-----------------------------------------------------------------------------------------------+
| ``$to``         | string|Closure | The real path to a directory or file under ``/app`` or Closure that responds HTML/JSON        |
+-----------------+----------------+-----------------------------------------------------------------------------------------------+
| ``$method``     | string         | ``GET``, ``POST``, ``PUT`` or ``DELETE`` or any combination with ``|`` such as ``GET|POST``.  |
|                 |                | Default to ``GET``.                                                                           |
+-----------------+----------------+-----------------------------------------------------------------------------------------------+
| ``$pattern``    | array|null     | Array of the regex patterns for variables in $path such as ``array('id' => '\d+')``           |
+-----------------+----------------+-----------------------------------------------------------------------------------------------+

As of version 3.4, you can define a route that renders a HTML page using a closure. ::

    route('lc_about')->map('/about', function() {
        # /about maps to /app/example/pages/about.php
        $pageTitle = _t('About');
        _app('title', $pageTitle);

        return _app('view')->block('example/pages/about'); // <-- renders app/example/pages/about.php
    });

Route Groups
------------

PHPLucidFrame 2.0 supports route groups using prefix which allows you to prepend a URI prefix to a large number of routes. In the following example, the URI prefix ``/api/posts`` is added to all routes defined inside ``route_group()``: ::

    route_group('/api/posts', function () {
        route('lc_post')->map('/', '/example/api/post', 'GET');
        route('lc_post_create')->map('/', '/example/api/post/create', 'POST');
        route('lc_post_update')->map('/{id}', '/example/api/post/update', 'PUT', array('id' => '\d+'));
        route('lc_post_delete')->map('/{id}', '/example/api/post/delete', 'DELETE', array('id' => '\d+'));
    });

The above route groups definition is equal to these individual route definitions: ::

    route('lc_post')->map('/api/posts', '/example/api/post', 'GET');
    route('lc_post_create')->map('/api/posts', '/example/api/post/create', 'POST');
    route('lc_post_update')->map('/api/posts/{id}', '/example/api/post/update', 'PUT', array('id' => '\d+'));
    route('lc_post_delete')->map('/api/posts/{id}', '/example/api/post/delete', 'DELETE', array('id' => '\d+'));

.. note::
    - You can define your custom routes in ``/inc/route.config.php`` or ``/app/inc/route.config.php``

Accessing URL
-------------

You can get the current routing path using a function ``_r()`` and you can get a component of the current path using ``_arg()``. ::

    // url is www.example.com
    echo _r();    // home
    echo _arg(0); // home

    // url is www.example.com/user/1
    echo _r();    // user/1
    echo _arg(0); // user
    echo _arg(1); // 1

PHPLucidFrame also provides to use URL component key preceding by a dash ``-``. For example, ``http://www.example.com/posts/-page/1`` which reflects ``http://www.example.com/posts?page=1`` ::

    // url is www.example.com/posts/-page/1/-sort/title/asc
    echo _r();    // posts/-page/1/-sort/title
    echo _arg(0); // posts
    echo _arg(1); // -page
    echo _arg(2); // 1
    echo _arg(3); // -sort
    echo _arg(4); // title
    echo _arg(5); // asc

    // The following is a formal way of getting the URI component "page"
    echo _arg('page'); // 1
    // The following is a formal way of getting the URI component "sort"
    _pr(_arg('sort')); // array( 'title', 'asc' )
    // _pr() is an convenience method for print_r.

Creating and Getting URL
------------------------

You can use the function ``_url()`` or ``route_url()`` to make an absolute URL. ::

    echo _url('user', array(1));
    // http://www.example.com/user/1

    echo _url('posts', array('page' => 1, 'sort' => array('title','asc'));
    // http://www.example.com/posts/-page/1/-sort/title/asc

    echo _url(); // same as echo _self();
    // it would return the current URL

When you have a custom route defined in ``/inc/route.config.php`` as described above at :ref:`Custom Routes`, you can use the route name as below: ::

    _url('lc_blog_show', array('id' => 1, 'slug' => 'hello-world'))
    // http://www.example.com/blog/1/hello-world

Redirecting URL
---------------

You can use the function ``_redirect()`` to redirect to a URL. ::

    // redirect to the home page according to $lc_homeRouting in /inc/config.php
    // 'home' is a constant whatever you defined for $lc_homeRouting
    _redirect('home');

    // redirect to http://www.example.com/user/1
    _redirect('user', array(1));

    // redirect to http://www.example.com/posts/-page/1/-sort/title/asc
    _redirect('posts', array('page' => 1, 'sort' => array('title','asc')));

    // assuming that the current URL is http://www.example.com/posts/-page/1/-sort/title/asc
    // you can redirect to the current page itself by updating the query strings 'page' and 'sort'
    // in this case, you can use NULL or an empty string for the first parameter to _redirect()
    // redirect to http://www.example.com/posts/-page/2/-sort/title/desc
    _redirect(NULL, array('page' => 2, 'sort' => array('title','desc'));

    // redirect to the current page itself
    _redirect(); // or _redirect('self');

    // permanent redirect to the new page
    _redirect301('path/to/a/new/replaced/page');

    // redirect to 401 page
    _page401(); // or _redirect('401')

    // redirect to 403 page
    _page403(); // or _redirect('403')

    // redirect to 404 page
    _page404(); // or _redirect('404')

Check more details in ``/lib/helpers/utility_helper.php`` and ``/lib/helpers/route_helper.php``.

Custom URL Rewrite
------------------

.. note:: This needs knowledge of Apache ``.htaccess`` rewrite rule syntax.

You may also write RewriteRule in ``.htaccess`` of the root directory, but by no means required. ::

    # www.example.com/en/99/foo-bar to ~/app/post/?lang=en&id=99&slug=foo-bar
    # www.example.com/zh-CN/99/foo-bar to ~/app/post/?lang=zh-CN&id=99&slug=foo-bar
    RewriteRule ^(([a-z]{2}|[a-z]{2}-[A-Z]{2})/)?([0-9]+)/(.*)$ app/index.php?lang=$1&id=$3&slug=$4&route=post [NC,L]

As the default routing name of LucidFrame is **route** and according to the RewriteRule above, ``route=post`` will map to the file ``/app/post/index.php`` or ``/app/post.php`` given the three URI components – ``lang``, ``id`` and ``slug``. For example, if the requested URL is ``www.example.com/en/99/foo-bar``, this will be rewritten to ``/app/post/index.php?lang=en&id=99&slug=foo-bar`` or ``/app/post.php?lang=en&id=99&slug=foo-bar``. In this case you can get the **id** and **slug** using ``_get()`` or ``_arg()``: ::

    $id = _get('id'); // or _arg('id')
    $slug = _get('slug'); // or _arg('slug')
