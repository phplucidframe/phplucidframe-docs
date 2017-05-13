Ajax and Javascript API
=======================

PHPLucidFrame makes use of `jQuery <http://jquery.com/>`_ to take advantage of the increased interest in developing Web 2.0 rich media applications. There are a wide range of built-in Javascript API functions provided.

The framework has a Javascript object declared LC and it has some global variables available to use. You can check  them in the section `Core Defined Constants & Variables <core-defined-constants-variables.html>`_. ``LC`` has two core objects - ``LC.Page`` and ``LC.Form``. Both provides utilities to make your pages and forms easier.

PHPLucidFrame recommends the code organization in a Javascript file so-called ``site.js``, but it is by no means required and you are free to use your prefer file name. You can extend the global Javascript object ``LC.Page`` and you can create namespace for each page in your application. You are suggested to check the sample example code ``/app/js/site.js`` in the release. ::

    // /app/js/site.js

    LC.Page.init = function() {
        // do some fancy stuff.
    }

    LC.Page.Blog = {
        url: LC.Page.url('blog'), /* mapping directory or route */

        /* Initialization of the blog page */
        init: function() {
            LC.Page.Blog.list();
        },

        /* Load list by Ajax */
        list: function() {
            LC.Page.request('list', LC.Page.Blog.url + 'list.php');
        }
    }

    $(function() {
        LC.Page.init();
    });

The file can be included in ``/inc/tpl/head.php`` or ``/app/inc/tpl/head.php`` to use it globally. ::

    <?php _js('site.js'); ?>

.. note:
    - LC.Page.initialize() is a reserved function.

The Page
--------

PHPLucidFrame has an object declared ``LC.Page`` and it provides API functions available to use.

LC.Page.languageSwitcher(lng)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The helper callback for `language switch <#switching-the-site-language>`_ required in multi-language sites.

**Parameters**:

+---------------+-------------+-------------------------------------------------------------------------+
| Name          | Type        | Description                                                             |
+===============+=============+=========================================================================+
| ``lng``       | string      | The language code to be switched                                        |
+---------------+-------------+-------------------------------------------------------------------------+

LC.Page.request(id, url, params, callback)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Ajax helper. It is a wrapper function to ``jQuery.get`` or ``jQuery.post``.

**Parameters**:

+---------------+-------------+---------------------------------------------------------------------------------+
| Name          | Type        | Description                                                                     |
+===============+=============+=================================================================================+
| ``id``        | string      | An HTML element id to attach Ajax and respond HTML to. If ``POST/GET`` is given,|
|               |             | no HTML is responded and any script can be executed upon the request complete.  |
+---------------+-------------+---------------------------------------------------------------------------------+
| ``url``       | string      | URL to be requested                                                             |
+---------------+-------------+---------------------------------------------------------------------------------+
| ``params``    | object      | The object of query string passing to the page requested, e.g,                  |
|               |             | ``{ key: value, key2, value2 }``                                                |
+---------------+-------------+---------------------------------------------------------------------------------+
| ``callback``  | function    | The callback function to be executed upon page request complete.                |
+---------------+-------------+---------------------------------------------------------------------------------+

LC.Page.throbber.register(id, callback)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Register a custom throbber for ajax requests by overriding the default throbber provided by LucidFrame which is triggered whenever Ajax request is thrown.

**Parameters**:

+---------------+-------------+--------------------------------------------------------------------------------------+
| Name          | Type        | Description                                                                          |
+===============+=============+======================================================================================+
| ``id``        | string      | An HTML element id to attach Ajax pager.                                             |
+---------------+-------------+--------------------------------------------------------------------------------------+
| ``callback``  | function    | The functional object like ``{ start: function() { .. }, stop: function() { .. } }`` |
+---------------+-------------+--------------------------------------------------------------------------------------+

LC.Page.pager(id)
^^^^^^^^^^^^^^^^^

Attach ajax to the pager element. Internal call after ``LC.Page.request()``. You can also use this for your own requirement.

**Parameters**:

+---------------+-------------+-------------------------------------------------------------------------+
| Name          | Type        | Description                                                             |
+===============+=============+=========================================================================+
| ``id``        | string      | An HTML element id to attach Ajax pager.                                |
+---------------+-------------+-------------------------------------------------------------------------+

LC.Page.url(path)
^^^^^^^^^^^^^^^^^

Get the absolute URL corresponding to the given route path.

**Parameters**:

+---------------+-------------+-------------------------------------------------------------------------+
| Name          | Type        | Description                                                             |
+===============+=============+=========================================================================+
| ``path``      | string      | The route path.                                                         |
+---------------+-------------+-------------------------------------------------------------------------+

**Returns**:

``<string>`` The complete absolute URL, for example, ``http://www.example.com/<language>/blog`` if ``Page.url('blog')``.

LC.Page.detectCSSFeature(featureName)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Check to see if CSS support is available in the browser.

**Parameters**:

+-------------------+-------------+-------------------------------------------------------------------------+
| Name              | Type        | Description                                                             |
+===================+=============+=========================================================================+
| ``featureName``   | string      | The CSS feature/property name in camel case                             |
+-------------------+-------------+-------------------------------------------------------------------------+

**Returns**:

``<boolean>`` true if the CSS feature is supported; otherwise false.

The Form
--------

PHPLucidFrame has a global object ``LC.Form`` which provides several useful functions.

Ajaxing your form
^^^^^^^^^^^^^^^^^

Forms are by default initialized for ajax submission if they have a ``type="submit"`` button or a ``type="button"`` button which has ``class="submit"``. By adding a class ``no-ajax`` to the form tag, you can detach Ajax from the form.

jQuery Datepicker
^^^^^^^^^^^^^^^^^

If HTML element has a class ``datepicker``, it will bind to jQuery datepicker.

jQuery Button
^^^^^^^^^^^^^

If HTML element has a class ``jqbutton``, it will bind to jQuery UI button theme.

LC.Form.clear(formId)
^^^^^^^^^^^^^^^^^^^^^

Clear the form element values and form messages.

**Parameters**:

+-------------------+-------------+-------------------------------------------------------------------------+
| Name              | Type        | Description                                                             |
+===================+=============+=========================================================================+
| ``formId``        | string      | HTML element id set to ``<form>`` tag                                   |
+-------------------+-------------+-------------------------------------------------------------------------+

LC.Form.data(id)
^^^^^^^^^^^^^^^^

Get the embedded JSON form data.

**Parameters**:

+-------------------+-------------+-------------------------------------------------------------------------+
| Name              | Type        | Description                                                             |
+===================+=============+=========================================================================+
| ``id``            | integer     | The data row unique id                                                  |
+-------------------+-------------+-------------------------------------------------------------------------+

This function is useful when passing data from PHP to Javascript in the form of JSON and get them in JS for further usage such as loading data into the form elements of the jQuery dialog. The HTML hierarchy must be ``#row-{unique-id} .row-data``. Here is an example snippet: ::

    <?php while($row = db_fetchObject($result)) { ?>
        <?php
        $data = array(
            'catId' => $row->catId,
            'catName' => $row->catName,
        );
        ?>
        <tr id="row-<?php echo $row->catId; ?>">
            <td class="tableLeft colAction">
                <span class="row-data" style="display:none"><?php echo json_encode($data); ?></span>
                <a href="javascript:" class="edit" title="Edit" onclick="LC.Page.Category.edit(<?php echo $row->catId; ?>)">
                    <span><?php echo _t('Edit'); ?></span>
                </a>
            </td>
            <td class="colAction">
                <a href="#" class="delete" title="Delete" onclick="LC.Page.Category.remove(<?php echo $row->catId; ?>)">
                    <span><?php echo _t('Delete'); ?></span>
                </a>
            </td>
            <td class="colName">
                <?php echo $row->catName; ?>
            </td>
        </tr>
    <?php } ?>
