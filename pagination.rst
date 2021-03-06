Pagination
==========

Listings with pagination are required in most of applications. PHPLucidFrame provides two ways of creating listings - with AJAX and without AJAX. There are two configuration variables in ``/inc/site.config.php`` which will be used here. ::

    # $lc_pageNumLimit: number of page numbers to be shown in pager
    $lc_pageNumLimit = 10;
    # $lc_itemsPerPage: number of items per page in pager
    $lc_itemsPerPage = 15;

For every grow-in-size and data-centric web application, displaying a reasonable number of records per page has always been a crucial part. PHPLucidFrame provides a quick, easy and handy way to paginate data to cure headaches of developers. LucidFrame offers a class Pager for pagination to make building paginated queries easier.

We start by getting the number of total records which is expected by the class Pager. ::

    # Count query for the pager
    $totalCount = db_count('post')
        ->where()->condition('deleted', null)
        ->fetch();

Once we retrieved the number of total records, we can create an instance from the Pager class. By default, the helper uses the query string for the current page number page. We can customize it by passing the name to the constructor like new ``Pager('pg')``.

The instance looks for the number of records per page ``$lc_itemsPerPage`` and the number of page limit ``$lc_pageNumLimit``. The total number of records has to be set to the instance as well. If we use images for navigation arrows, we can set the image directory path to the ``imagePath`` property of the instance, but we can also make them from CSS.

To incorporate AJAX functionality into pagination, you can make it easily by setting the ajax property to true. The ``calculate()`` method has to be invoked to calculate offset. ::

    # Prerequisite for the Pager
    $pager = _pager();
    $pager->set('itemsPerPage', _cfg('itemsPerPage')); // $lc_itemsPerPage
    $pager->set('pageNumLimit', _cfg('pageNumLimit')); // $lc_pageNumLimit
    $pager->set('total', $totalCount);                 // the total record count
    $pager->set('imagePath', WEB_ROOT.'images/pager/');// optional; if you use images for pagination
    $pager->set('ajax', true);                         // flag this is AJAX-enabled list
    $pager->calculate();                               // required to calculate offset

Then, we can use ``$pager->get('offset')`` and ``$pager->get('itemsPerPage')`` in our query ``LIMIT`` clause. ::

    $qb = db_select('post', 'p')
        ->where()->condition('p.deleted', null)
        ->orderBy('p.created', 'DESC')
        ->limit($pager->get('offset'), $pager->get('itemsPerPage'));

Finally, we call ``$pager->display()`` where we want to appear the pager. By default, the pager will be displayed using ``<table class="pager">`` tag, however, we can easily change it to ``<ul>`` or ``<div>`` by setting ``$pager->set('htmlTag', '<ul>')`` or ``$pager->set('htmlTag', '<div>')``. The default output HTML will look like: ::

    <table cellspacing="0" cellpadding="0" border="0" class="pager">
        <tbody>
            <tr>
                <td class="first-disabled">
                    <label>First</label>
                </td>
                <td class="prev-disabled">
                    <label>« Prev</label>
                </td>
                <td class="pages">
                    <span class="currentPage">1</span>
                    <span><a href="">2</a></span>
                </td>
                <td class="next-enabled">
                    <a href="">
                        <label>Next »</label>
                    </a>
                </td>
                <td class="last-enabled">
                    <a href="">
                        <label>Last</label>
                    </a>
                </td>
            </tr>
        </tbody>
    </table>

If we use ``imagePath``, the output HTML will be generated with ``<img />`` tag. The following images have to be available in our ``imagePath``:

- end.png
- end_disabled.png
- next.png
- next_disabled.png
- previous.png
- previous_disabled.png
- start.png
- start_disabled.png ::

    <table cellspacing="0" cellpadding="0" border="0" class="pager">
        <tbody>
            <tr>
                <td class="first-disabled"><img … /></td>
                <td class="prev-disabled"><img … /></td>
                <td class="pages">
                    <span class="currentPage">1</span>
                    <span><a href="">2</a></span>
                </td>
                <td class="next-enabled">
                    <a href=""><img … /></a>
                </td>
                <td class="last-enabled">
                    <a href=""><img … /></a>
                </td>
            </tr>
        </tbody>
    </table>

If we use ``$pager->set('htmlTag', '<ul>')``, the output will look like: ::

    <ul class="pager">
        <li class="first-disabled">
            <label>First</label>
        </li>
        <li class="prev-disabled">
            <label>« Prev</label>
        </li>
        <li class="pages">
            <span class="currentPage">1</span>
            <span><a href="">2</a></span>
        </li>
        <li class="next-enabled">
            <a href="">
                <label>Next »</label>
            </a>
        </li>
        <li class="last-enabled">
            <a href="">
                <label>Last</label>
            </a>
        </li>
    </ul>

If we use ``$pager->set('htmlTag', '<div>')``, the output will look like: ::

    <div class="pager">
        <div class="first-disabled">
            <label>First</label>
        </div>
        <div class="prev-disabled">
            <label>« Prev</label>
        </div>
        <div class="pages">
            <span class="currentPage">1</span>
            <span><a href="">2</a></span>
        </div>
        <div class="next-enabled">
            <a href="">
                <label>Next »</label>
            </a>
        </div>
        <div class="last-enabled">
            <a href="">
                <label>Last</label>
            </a>
        </div>
    </div>

We can adjust and extend the default pager CSS in ``/css/base.css`` according to our needs or we can write it in our own.

Create an AJAX Listing Page
---------------------------

According to the framework-recommended page structure, you could have the following structure for your listing page. ::

    /path_to_webserver_document_root
        /app
            /post
                |-- index.php
                |-- list.php
                |-- view.php

In you ``/app/post/view.php`` you need to create an empty HTML container which AJAX will respond HTML to. ::

    <!-- app/post/view.php -->
    <?php include( _i('inc/header.php') ); ?>

    <h3><?php echo $pageTitle; ?></h3>

    <div id="list"></div> <!-- #list will be a first parameter to Page.request(). See later -->

    <?php include( _i('inc/footer.php') ); ?>

Create a small javascript snippet in your ``/app/js/app.js``. ::

    /** app/js/app.js */
    LC.Page.Post = {
        url : LC.Page.url('post'), /* mapping directory */
        /* Initialization of the Post page */
        init : function() {
            LC.Page.Post.list();
        },
        list : function() {
            /* LC.Page.request('HTML container ID', 'Requested URL', 'Optional Parameter in JSON {}'); */
            LC.Page.request('list', Page.Post.url + 'list.php');
        }
    }

Call the script at the end of ``/app/post/view.php`` ::

    <?php include( _i('inc/header.php') ); ?>

    <h3><?php echo $pageTitle; ?></h3>

    <div id="list"></div> <!-- #list will be a first parameter to Page.request(). See later -->

    <?php include( _i('inc/footer.php') ); ?>

    <script type="text/javascript">
        $(function() {
            LC.Page.Post.init();
        });
    </script>

Finally you have to write ``/app/post/list.php`` to request and respond by AJAX. In the script, query, paginate and display your data. ::

    <?php
    /** app/post/list.php */

    $get = _get($_GET);

    # Count query for the pager
    $totalCount = db_count('post')
        ->where()->condition('deleted', null)
        ->fetch();

    # Prerequisite for the Pager
    $pager = _pager();
    $pager->set('itemsPerPage', _cfg('itemsPerPage')); // $lc_itemsPerPage
    $pager->set('pageNumLimit', _cfg('pageNumLimit')); // $lc_pageNumLimit
    $pager->set('total', $totalCount);                 // the total record count
    $pager->set('imagePath', WEB_ROOT.'images/pager/');// optional; if you use images for pagination
    $pager->set('ajax', true);                         // flag this is AJAX-enabled list
    $pager->calculate();                               // required to calculate offset

    $qb = db_select('post', 'p')
        ->where()->condition('p.deleted', null)
        ->orderBy('p.created', 'DESC')
        ->limit($pager->get('offset'), $pager->get('itemsPerPage'));
    ?>

    <?php if ($qb->getNumRows()) { ?>
        <?php while ($row = $qb->fetchRow()) { ?>
            <p class="post">
                <h5>
                    <a href="<?php echo _url('post', array($row->postId, $row->slug)); ?>"><?php echo $row->title; ?></a>
                </h5>
                <p><?php echo $b->body; ?></p>
                <p>
                    <a href="<?php echo _url('post', array($row->postId, $row->slug)); ?>" class="button mini green"><?php echo _t('Read More'); ?></a>
                </p>
            </p>
        <?php } // while end ?>
        <!-- display the pager where you want to appear -->
        <div class="pager clearfix">
            <?php echo $pager->display(); ?>
            <div class="pagerRecords"><?php echo _t('Total %d records', $totalCount); ?></div>
        </div>
    <?php } else { ?>
        <div class="noRecord"><?php echo _t('There is no record.'); ?></div>
    <?php } ?>

Create a Generic Listing Page without AJAX
------------------------------------------

Sometimes, you may not want to use AJAX list. You can easily disable LucidFrame pagination helper for AJAX. In this case, you don’t need to have ``/app/post/list.php`` like in the above example. Instead, you should have ``/app/post/query.php``. Retrieve your data in ``query.php`` and then render your HTML in ``/app/post/view.php``. ::

    /path_to_webserver_document_root
        /app
            /post
                |-- index.php
                |-- query.php
                |-- view.php

Include ``query.php`` in your ``/app/post/index.php`` like below. You don’t need to write Javascript in this case. ::

    <?php
    /** app/post/index.php */

    $pageTitle = _t('Latest Posts');

    include('query.php');
    ?>
    <!DOCTYPE html>
    <html lang="<?php echo _lang(); ?>">
    <head>
        <title><?php echo _title($pageTitle); ?></title>
        <?php include( _i('inc/head.php') ); ?>
    </head>
    <body>
        <?php include('view.php'); ?>
    </body>
    </html>

In ``query.php``, retrieve and paginate your data. ::

    <?php
    /** app/post/query.php */

    # Count query for the pager
    $totalCount = db_count('post')
        ->where()->condition('deleted', null)
        ->fetch();

    # Prerequisite for the Pager
    $pager = _pager();
    $pager->set('itemsPerPage', _cfg('itemsPerPage')); // $lc_itemsPerPage
    $pager->set('pageNumLimit', _cfg('pageNumLimit')); // $lc_pageNumLimit
    $pager->set('total', $totalCount);                 // the total record count
    $pager->set('imagePath', WEB_ROOT.'images/pager/');// optional; if you use images for pagination
    $pager->set('ajax', false);                        // trun off AJAX
    $pager->calculate();                               // required to calculate offset

    $qb = db_select('post', 'p')
        ->where()->condition('p.deleted', null)
        ->orderBy('p.created', 'DESC')
        ->limit($pager->get('offset'), $pager->get('itemsPerPage'));

Finally, your ``view.php`` will look like this: ::

    <!-- app/post/view.php -->
    <?php include( _i('inc/header.php') ); ?>

    <h3><?php echo $pageTitle; ?></h3>
    <div id="list">
    <?php if ($totalCount) { ?>
        <?php while ($row = $qb->fetchRow()) { ?>
            <p class="post">
                <h5>
                    <a href="<?php echo _url('post', array($row->postId, $row->slug)); ?>"><?php echo $row->title; ?></a>
                </h5>
                <p><?php echo $b->body; ?></p>
                <p>
                    <a href="<?php echo _url('post', array($row->postId, $row->slug)); ?>" class="button mini green"><?php echo _t('Read More'); ?></a>
                </p>
            </p>
        <?php } // while end ?>
        <!-- display the pager where you want to appear -->
        <div class="pager clearfix">
            <?php echo $pager->display(); ?>
            <div class="pagerRecords"><?php echo _t('Total %d records', $totalCount); ?></div>
        </div>
    <?php } else { ?>
        <div class="noRecord"><?php echo _t('There is no record.'); ?></div>
    <?php } ?>
    </div>

    <?php include( _i('inc/footer.php') ); ?>
