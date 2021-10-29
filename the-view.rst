The View
========

As of version 3.0.0, PHPLucidFrame added a default global View object for managing your layout file, rendering variables to your views, including head scripts/styles, loading your view templates. The View object is available by calling ``_app('view')``.

Creating View
-------------

A view is a visual output representation to user and is simply a web page, or a page fragment. You can create a view by placing a file ``view.php`` in a particular page directory, for example, a single post page ``http://www.example.com/post/{id}`` or ``http://localhost/acme/post/{id}`` may look like the below directory structure: ::

    /app
        /post
            |-- index.php
            |-- view.php <--

The ``view.php`` generally should contain HTML between ``<body>`` and ``</body>``. This may include header and footer fragments. But the header and footer may be also put into the layout file. See :ref:`Layout File` section.

Passing Data To view
--------------------

You can pass data to your view by using the ``addData()`` method of the View object. You can get the View object using ``$view = _app('view')`` and set data using ``$view->addData('name', $value)`` in a particular ``index.php``. For example, ``/app/post/index.php`` may look like this ::

    $id = _get('id');
    $view = _app('view');

    $post = db_findOrFail('post', $id);

    _app('title', $blog->title);

    $view->addData('pageTitle', $post->title); // This will be available as $pageTitle in view
    $view->addData('post', $post); // This will be available as $post in view

Alternatively, you can pass an array of data to view by directly assigning to the ``data`` property of the View object. ::

    $view->data = array(
        'pageTitle' => $post->title,
        'post'      => $post,
    );

Nested Views
------------

Views may also be nested. You can include another view in a view. Let's say for example, you have a view (fragement) to show recent posts in single post page. ::

    /app
        /post
            |-- index.php
            |-- recent-posts.php <--
            |-- view.php

You can include ``recent-posts.php`` in ``view.php`` like this ::

    <?php _app('view')->block('recent-posts') ?>

If ``recent-posts.php`` is needed to include in more than one page, you can move the file into ``/app/inc/tpl/`` and ``_app('view')->block('recent-posts')`` will automatically look for the file in that directory when it is not found in the current directory.

Layout File
-----------

Since verion 3.0, layout mode is enabled by default (``$lc_layoutMode = true`` in ``/inc/config.php``). The default layout file is configured as ``$lc_layoutName = 'layout'`` which points to ``app/inc/tpl/layout.php``.

Basically a layout file would have the below structure. ::

    <!DOCTYPE html>
    <html>
    <head>
        <title><?php echo _title() ?></title>
        <link rel="canonical" href="<?php echo _canonical() ?>" />
        <?php _hreflang() ?>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <?php _metaSeoTags() ?>
        <link rel="shortcut icon" href="<?php echo _img('favicon.ico'); ?>" type="image/x-icon" />
        <?php _css('base.css') ?>
        <?php _css('base.' . _lang() . '.css') ?>  <!-- you may not need this if your site doesn't have multi-languages ->
        <?php _css('responsive.css') ?>
        <?php _css('jquery.ui') ?>
        <?php _app('view')->headStyle() ?> <!-- styles added by a particular page ->
        <?php _js('jquery') ?>
        <?php _js('jquery.ui') ?>
        <?php _script() ?> <!-- this is required for global JS variables -->
        <?php _js('LC.js') ?>
        <?php _app('view')->headScript() ?>  <!-- scripts added by a particular page ->
        <?php _js('app.js') ?>
    </head>
    <body>
        <div>
            <header>
                <!-- your header stuffs -->
            </header>
            <section>
                <?php _app('view')->load() ?> <!-- This injects a particular view template here -->
            </section>
            </footer>
                <!-- your footer stuffs -->
            </footer>
        </div>
    </body>
    </html>

.. note::
    - You can check ``/app/inc/tpl/layout.php``.

You may have a separate layout file for a particular page, let's say for example, you have a login page which have a different layout other than the rest pages of the site. You can create a new layout file ``/app/inc/tpl/layout_login.php``.

You can set the new layout name for login page in ``/app/login/index.php`` such as ::

    _app('view')->layout = 'layout_login';

Then, the login page will use ``layout_login.php`` whereas the other pages use ``layout.php``.

Stylesheets & Scripts In Head
-----------------------------

You may include stylesheets and scripts for a particular page rather than globally including in the layout file. Then you can use ``addHeadStyle()`` and ``addHeadScript()`` of the View object in ``index.php`` ::

    /** app/post/index.php */

    # If locally stored files
    $view->addHeadStyle('select2.min.css');  // app/assets/css/select2.min.css or assets/css/select2.min.css
    $view->addHeadScript('select2.min.css'); // app/assets/js/select2.min.css or assets/js/select2.min.css

    # If CDN
    $view->addHeadStyle('https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/css/select2.min.css');
    $view->addHeadScript('https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.min.js');

Alternatively, you can use the helper functions - ``_addHeadStyle()`` and ``_addHeadScript()``. ::

    /** app/post/index.php */

    # If locally stored files
    _addHeadStyle('select2.min.css');  // app/assets/css/select2.min.css or assets/css/select2.min.css
    _addHeadScript('select2.min.css'); // app/assets/js/select2.min.css or assets/js/select2.min.css

    # If CDN
    _addHeadStyle('https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/css/select2.min.css');
    _addHeadScript('https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.min.js');