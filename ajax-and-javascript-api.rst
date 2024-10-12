Ajax and Javascript API
=======================

PHPLucidFrame makes use of `jQuery <http://jquery.com/>`_ to take advantage of the increased interest in developing Web 2.0 rich media applications. There are a wide range of built-in Javascript API functions provided.

The framework has a Javascript object declared LC and it has some global variables available to use. You can check  them in the section `Core Defined Constants & Variables <core-defined-constants-variables.html>`_. Moreover, ``LC`` has there core objects - ``LC.Page``, ``LC.Form`` and ``LC.List``. They all provide utilities to make your pages, forms and lists easier.

PHPLucidFrame recommends the code organization in a Javascript file so-called ``app.js``, but it is by no means required and you are free to use your prefer file name. You can extend the global Javascript object ``LC.Page`` and you can create namespace for each page in your application. You are suggested to check the sample example code ``/app/js/app.js`` in the release. ::

    // /app/js/app.js

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

    <?php _js('app.js'); ?>

.. note:
    - LC.Page.initialize() is a reserved function.

The Page
--------

PHPLucidFrame has an object declared ``LC.Page`` and it provides API functions available to use.

LC.Page.languageSwitcher(lng)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The helper callback for `language switch <#switching-the-site-language>`_ required in multi-language sites.

**Parameters**:

+---------+--------+----------------------------------+
| Name    | Type   | Description                      |
+=========+========+==================================+
| ``lng`` | string | The language code to be switched |
+---------+--------+----------------------------------+

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

+--------------+----------+--------------------------------------------------------------------------------------+
| Name         | Type     | Description                                                                          |
+==============+==========+======================================================================================+
| ``id``       | string   | An HTML element id to attach Ajax pager.                                             |
+--------------+----------+--------------------------------------------------------------------------------------+
| ``callback`` | function | The functional object like ``{ start: function() { .. }, stop: function() { .. } }`` |
+--------------+----------+--------------------------------------------------------------------------------------+

LC.Page.pager(id)
^^^^^^^^^^^^^^^^^

Attach ajax to the pager element. Internal call after ``LC.Page.request()``. You can also use this for your own requirement.

**Parameters**:

+--------+--------+------------------------------------------+
| Name   | Type   | Description                              |
+========+========+==========================================+
| ``id`` | string | An HTML element id to attach Ajax pager. |
+--------+--------+------------------------------------------+

LC.Page.url(path)
^^^^^^^^^^^^^^^^^

Get the absolute URL corresponding to the given route path.

**Parameters**:

+----------+--------+-----------------+
| Name     | Type   | Description     |
+==========+========+=================+
| ``path`` | string | The route path. |
+----------+--------+-----------------+

**Returns**:

``<string>`` The complete absolute URL, for example, ``http://www.example.com/<language>/blog`` if ``Page.url('blog')``.

LC.Page.detectCSSFeature(featureName)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Check to see if CSS support is available in the browser.

**Parameters**:

+-----------------+--------+---------------------------------------------+
| Name            | Type   | Description                                 |
+=================+========+=============================================+
| ``featureName`` | string | The CSS feature/property name in camel case |
+-----------------+--------+---------------------------------------------+

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

+------------+--------+---------------------------------------+
| Name       | Type   | Description                           |
+============+========+=======================================+
| ``formId`` | string | HTML element id set to ``<form>`` tag |
+------------+--------+---------------------------------------+

LC.Form.getFormData(formId, id)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the embedded JSON form data.

**Parameters**:

+------------+---------+------------------------+
| Name       | Type    | Description            |
+============+=========+========================+
| ``formId`` | integer | HTML Form ID           |
+------------+---------+------------------------+
| ``id``     | integer | The data row unique id |
+------------+---------+------------------------+

This function is useful when passing data from PHP to Javascript in the form of JSON and get them in JS for further usage such as loading data into the form elements of the jQuery dialog

.. note::
    - You can check the completed example source code at `<https://github.com/phplucidframe/admin-boilerplate>`_

LC.Form.slug(str)
^^^^^^^^^^^^^^^^^

Generate slug value from the given string

**Parameters**:

+----------------+-------------+-------------------------------------+
| Name           | Type        | Description                         |
+===================+=============+==================================+
| ``str``        | string      | The string to generate slug         |
+----------------+-------------+-------------------------------------+

**Returns**:

``<string>`` The slug value

LC.Form.beforeSubmit
^^^^^^^^^^^^^^^^^^^^

Bind beforeSubmit hook to AJAX form

**Parameters**:

+--------------+----------+--------------------------------------------------------------------------+
| Name         | Type     | Description                                                              |
+==============+==========+==========================================================================+
| ``formId``   | string   | HTML form id                                                             |
+--------------+----------+--------------------------------------------------------------------------+
| ``callback`` | function | A callback function that handles the event before the form is submitted. |
+--------------+----------+--------------------------------------------------------------------------+

LC.Form.afterSubmit
^^^^^^^^^^^^^^^^^^^

Bind afterSubmit hook to AJAX form

**Parameters**:

+--------------+----------+-------------------------------------------------------------------------+
| Name         | Type     | Description                                                             |
+==============+==========+=========================================================================+
| ``formId``   | string   | HTML form id                                                            |
+--------------+----------+-------------------------------------------------------------------------+
| ``callback`` | function | A callback function that handles the event after the form is submitted. |
+--------------+----------+-------------------------------------------------------------------------+

The List
--------

As of PHPLucidFrame 3.0, ``LC.List`` is added to help create AJAX list easily.

LC.List.init(options)
^^^^^^^^^^^^^^^^^^^^^

Initialize an AJAX list

**Parameters**:

+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| Name                                    | Type   | Default                          | Description                                                                      |
+=========================================+========+==================================+==================================================================================+
| ``options``                             | object |                                  | The object with the following properties                                         |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.url``                         | string | ``LC.Page.url(LC.vars.baseDir)`` | The URL for AJAX request to render the list                                      |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.params``                      | string | ``{}``                           | Optional parameters to ``options.url``                                           |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.id``                          | string | ``list``                         | HTML id for the list container element                                           |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.modalStyle``                  | string | ``jquery``                       | Modal dialog style - jquery or bootstrap                                         |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.formModal``                   | string | ``#dialog-item``                 | HTML id of jQuery dialog modal to create/edit an entity                          |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.formModalCancelButton``       | string | ``#btn-cancel``                  | HTML id of the button to close the dialog modal                                  |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.formId``                      | string | ``dialog-form``                  | HTML id of the form in the dialog modal                                          |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.confirmModal``                | string | ``#dialog-confirm``              | HTML id of the confirm modal to delete an entity                                 |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.confirmModalTitle``           | string | Confirm Delete                   | Title of the confirm modal to delete an entity                                   |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.confirmModalMessage``         | string | Are you sure you want to delete? | Message of the confirm modal to delete an entity                                 |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.confirmModalPositiveMessage`` | string | Yes, continue                    | Button caption for positive action to delete an entity                           |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.confirmModalNegativeMessage`` | string | Close                            | Button caption to close the modal                                                |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.createButton``                | string | ``#btn-new``                     | HTML id of the button to launch the dialog modal to create a new entry           |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.editButtonClass``             | string | ``.table .actions .edit``        | HTML id/class of the button to launch the dialog modal to edit an entry          |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.deleteButtonClass``           | string | ``.table .actions .delete``      | HTML id/class of the button to delete an entry                                   |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.createCallback``              | string | null                             | A callback function to be invoked before the dialog modal is opened for creation |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.editCallback``                | string | null                             | A callback function to be invoked before the dialog modal is opened for editing  |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+
| ``options.deleteCallback``              | string | null                             | A callback function to be invoked after the entry is deleted                     |
+-----------------------------------------+--------+----------------------------------+----------------------------------------------------------------------------------+

LC.List.list(listId, [arg1, arg2])
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Load the list by AJAX

**Parameters**:

When three parameters are provided,

+------------+--------+----------------------------------------------------+
| Name       | Type   | Description                                        |
+============+========+====================================================+
| ``listId`` | string | HTML id of the list container; default is ``list`` |
+------------+--------+----------------------------------------------------+
| ``arg1``   | string | URL to request                                     |
+------------+--------+----------------------------------------------------+
| ``arg2``   | object | Parameters to URL                                  |
+------------+--------+----------------------------------------------------+

When two parameters are provided and the second parameter is string,

+------------+--------+----------------------------------------------------+
| Name       | Type   | Description                                        |
+============+========+====================================================+
| ``listId`` | string | HTML id of the list container; default is ``list`` |
+------------+--------+----------------------------------------------------+
| ``arg1``   | string | URL to request                                     |
+------------+--------+----------------------------------------------------+

When two parameters are provided and the second parameter is object,

+------------+--------+----------------------------------------------------+
| Name       | Type   | Description                                        |
+============+========+====================================================+
| ``listId`` | string | HTML id of the list container; default is ``list`` |
+------------+--------+----------------------------------------------------+
| ``arg1``   | object | Parameter to URL to request                        |
+------------+--------+----------------------------------------------------+

.. note::
    When no parameter is provided to the function, the first parameter ``listId`` would be ``list`` by default.

LC.List.create(listId)
^^^^^^^^^^^^^^^^^^^^^^

Launch the dialog to create a new entity

**Parameters**

+------------+--------+------------------------------------------------------+
| Name       | Type   | Description                                          |
+============+========+======================================================+
| ``listId`` | string | HTML id of the list container; default is ``list``   |
+------------+--------+------------------------------------------------------+

LC.List.edit(id, listId)
^^^^^^^^^^^^^^^^^^^^^^^^

Launch the dialog to edit an existing entity

**Parameters**

+------------+--------+----------------------------------------------------+
| Name       | Type   | Description                                        |
+============+========+====================================================+
| ``id``     | mixed  | ID (from db) to edit (required)                    |
+------------+--------+----------------------------------------------------+
| ``listId`` | string | HTML id of the list container; default is ``list`` |
+------------+--------+----------------------------------------------------+

LC.List.remove(id, listId)
^^^^^^^^^^^^^^^^^^^^^^^^^^

Launch the dialog to confirm for deleting an entity

**Parameters**

+------------+--------+----------------------------------------------------+
| Name       | Type   | Description                                        |
+============+========+====================================================+
| ``id``     | mixed  | ID (from db) to edit (required)                    |
+------------+--------+----------------------------------------------------+
| ``listId`` | string | HTML id of the list container; default is ``list`` |
+------------+--------+----------------------------------------------------+

LC.List.doDelete(listId)
^^^^^^^^^^^^^^^^^^^^^^^^

Do delete action upon confirmation (after approval of the dialog launched by ``LC.List.remove()``)

**Parameters**

+------------+--------+----------------------------------------------------+
| Name       | Type   | Description                                        |
+============+========+====================================================+
| ``listId`` | string | HTML id of the list container; default is ``list`` |
+------------+--------+----------------------------------------------------+

LC.DependentUpdater
-------------------

Change another select dropdown (child) upon one select dropdown (parent) change using AJAX request.

**HTML Attributes for The Parent <select> Element**:

+---------------------+----------+--------------------------------------------------------------------------------+
| Name                | Required | Description                                                                    |
+=====================+==========+================================================================================+
| ``data-dependency`` | Yes      | The HTML element (``select``) ID of the child element                          |
+---------------------+----------+--------------------------------------------------------------------------------+
| ``data-url``        | Yes      | URL for AJAX request to retrieve data by the selected id of the parent element |
+---------------------+----------+--------------------------------------------------------------------------------+
| ``data-callback``   | No       | A js callback function to invoke after the AJAX request is completed.          |
+---------------------+----------+--------------------------------------------------------------------------------+

**HTML Attributes for The Child <select> Element**:

+----------------+----------+---------------------------------------------------------------+
| Name           | Required | Description                                                   |
+================+==========+===============================================================+
| ``data-value`` | No       | The value to be selected after the AJAX request is completed. |
+----------------+----------+---------------------------------------------------------------+

The example scenario is to generate townships by a region selected. Here is an example code: ::

    <!-- parent element -->
    <select class="form-control" id="region" name="region"
            data-dependency="#township"
            data-url="<?php echo _url('api/townships') ?>">
        <option value=""><?php echo _t('Select Region') ?></option>
        <option value="1">Yangon</option>
        <option value="2">Mandalay</option>
    </select>

    <!-- child element -->
    <select class="form-control" id="township" name="township">
        <option value=""><?php echo _t('Select Township') ?></option>
    </select>

You need to create ``/app/api/townships.php`` (the URL you would provide in the ``data-url`` attribute) with the following similar code to return a json response. ::

    $regionId = _arg('parentId');

    $qb = db_select('town', 't')
        ->fields('t', array('id', 'name'))
        ->where()
            ->condition('region_id', $regionId)
        ->orderBy('name');

    $result = array();
    while ($row = $qb->fetchRow()) {
        $result[$row->id] = $row->name;
    }

    _json($result);

Then, the township dropdown will be re-generated whenever the region dropdown is changed.

LC.eval
-------

Evaluates JavaScript code represented as a string.

**Parameters**

+---------------+--------+-------------------------------------------------+
| Name          | Type   | Description                                     |
+===============+========+=================================================+
| ``statement`` | string | The javascript expression as string to evaluate |
+---------------+--------+-------------------------------------------------+

LC.getKeyByValue
----------------

Get a key in an object by its value

**Parameters**

+------------+--------+---------------------------------------------------+
| Name       | Type   | Description                                       |
+============+========+===================================================+
| ``object`` | object | The object to search in                           |
+------------+--------+---------------------------------------------------+
| ``value``  | mixed  | The value to search in the first parameter object |
+------------+--------+---------------------------------------------------+
