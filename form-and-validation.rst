Form & Validation
=================

You can implement a form in two ways – using AJAX and without using AJAX. PHPLucidFrame provides AJAX form submission by default.

Creating AJAX Form
------------------

Create a form tag as usual. If you do not set the attribute ``action``, LucidFrame will look for ``action.php`` in the same directory and will submit to it. Until this time of writing, ``id="formId"`` must be used. Your form should also have a message container in which any error message can be shown. ::

    <form name="your-form-name" id="your-form-id" method="post">
        <div class="message error"></div>

        <!-- HTML input elements here as usual -->

        <?php form_token(); ?>
    </form>

You can also provide the ``action`` attribute for your desired form handling file. ::

    <form name="your-form-name" id="your-form-id" action="<?php echo _url('path/to/action.php'); ?>" method="post">
        <div class="message error"></div>

        <!-- HTML input elements here as usual -->

        <?php form_token(); ?>
    </form>

If you don't want to clear data in form inputs after submit, you can set ``data-clear="off"`` to the form tag. ::

    <form name="your-form-name" id="your-form-id" data-clear="off" method="post">

One of the following two button implementation should be made to your AJAX form.

1. ``<input type="submit" /> or <button type="submit">..</button>``
2. ``<input type="button" class="submit" /> or <button type="button" class="submit">...</button>``

If your form has no submit type button and if you want the form submission when pressing "Enter" in any text box, set ``class="default-submit"`` to the form tag.

Creating A Generic Form Without AJAX
------------------------------------

You can make a generic form submission without AJAX using ``class="no-ajax"`` in the ``<form>`` tag. ::

    <form name="your-form-name" id="your-form-id" method="post" class="no-ajax">
        <div class="message error"></div>

        <!-- HTML input elements here as usual -->

        <?php form_token(); ?>
    </form>

Form Action Handling & Validation
---------------------------------

The following is a scaffold of AJAX form handling and validation. You can check the sample codes in the release. ::

    <?php
    /**
     * The action.php (optional) handles form submission.
     * It should perform form validation, create, update, delete of data manipulation to database.
     * By default, a form is initiated for AJAX and action.php is automatically invoked if the action attribute is not given in the <form> tag.
     */
    $success = false;

    if (_isHttpPost()) {
        $post = _post(); // Sanitize your inputs

        /** Form validation prerequisites here, for example */
        $validations = array(
            'title' => array(
                'caption'   => _t('Title'),
                'value'     => $post['title'],
                'rules'     => array('mandatory'),
            ),
            'body' => array(
                'caption'   => _t('Body'),
                'value'     => $post['body'],
                'rules'     => array('mandatory'),
            ),
        );

        if (form_validate($validations)) {
            /**
            Database operations here
            */

            if ($success) {
                form_set('success', true);
                form_set('message', _t('Your successful message is here'));

                // If you want to redirect to another page, use the option 'redirect'
                // form_set('redirect', _url('path/to/your/page'));
            }
        } else {
            form_set('error', validation_get('errors'));
        }
    }

    // Respond to the client
    form_respond('your-form-id'); // HTML form ID must be used here

If your form is a generic form without using AJAX, the last line in above code is not required in ``action.php``. Instead, you have to use ``form_respond('your-form-id', validation_get('errors'))`` at the end of the form in ``view.php`` in order to show error messages correctly. ::

    <form name="your-form-name" id="your-form-id" method="post" class="no-ajax">
        <div class="message error"></div>
        <!-- HTML input elements here as usual -->
        <?php form_token(); ?>
    </form>
    <?php form_respond('your-form-id', validation_get('errors')); ?>

Setting Data Validation
-----------------------

PHPLucidFrame provides a number of functions that aid in form validation. There are several validation rules provided and using them can be quite easy. First of all, a validation array has to be defined and the syntax of the validation array is: ::

    $validations = array(
        'htmlIdOrName' => array( // The HTML id or name of the input element
            'caption'    => _t('Your Element Caption'); // The caption to show in the error message
            'value'      => $value, // The value to be validated
            'rules'      => array(), // Array of validation rules defined, e.g., array('mandatory', 'email')
            'min'        => '', // The required property for the rule 'min', 'minLength', 'between'
            'max'        => '', // The required property for the rule 'max', 'maxLength', 'between'
            'protocol'   => '', // The required property for the rule 'ip'
            'maxSize'    => '', // The required property for the rule 'fileMaxSize'
            'maxWidth'   => '', // The required property for the rule 'fileMaxWidth', 'fileMaxDimension'
            'maxHeight'  => '', // The required property for the rule 'fileMaxHeight' 'fileMaxDimension'
            'width'      => '', // The required property for the rule 'fileExactDimension'
            'height'     => '', // The required property for the rule 'fileExactDimension'
            'extensions' => '', // The required property for the rule 'fileExtension'
            'dateFormat' => '', // The required property for the rule 'date', 'datetime'
            'pattern'    => '', // The required property for the rule 'custom'
            'table'      => '', // The required property for the rule 'unique'
            'field'      => '', // The required property for the rule 'unique'
            'id'         => '', // The optional property for the rule 'unique'
            'parameters' => array(
                // The arguments (starting from the second) passing to the custom validation functions
                // this may be needed when you set your custom rule in the property 'rules'
                'validate_customRule' => array('param2', 'param3')
            ),
            'messages'   => array(
                // to overwrite the default validation messages OR
                // to define the custom message for the custom validation rules
                'coreRule' => _t('The overwritten message here'), // 'coreRule' means the core validation rule provided by LucidFrame, e.g., mandatory, email, username, etc.
                'validate_customRule' => _t('Your custom message here')
            )
        ),
        'anotherInputHtmlIdOrName' => array(
            // similiar options described above ...
        ),
    );

The validation array should be passed to ``form_validate()`` to be processed. ::

    if (form_validate($validations)) { // or validation_check($validations)
        // ...
    }

.. note::
    - ``validation_check()`` doesn't check the form token generated by ``form_token()``.

Sanitizing Form Inputs
----------------------

You can sanitize form inputs using ``_post()``. ::

    if (_isHttpPost()) {
        $name = _post('name'); // $_POST['name']
        $email = _post('email'); // $_POST['email']
    }

You can also sanitize all inputs by calling ``_post()`` without parameter. ::

    if (_isHttpPost()) {
        $post = _post(); // Array of $_POST will be sanitized
        // $post['name']
        // $post['email']
    }

Core Validation Rules
---------------------

The core validation rules are defined in ``/lib/helpers/validation_helper.php`` and you could also define your own custom validation functions in ``/app/helpers/validation_helper.php`` which will be auto-loaded.

alphaNumeric
^^^^^^^^^^^^
The field must only contain letters and numbers (integers). Spaces are not allowed to include.

alphaNumericDash
^^^^^^^^^^^^^^^^
The field must only contain letters, numbers (integers) and dashes.

alphaNumericSpace
^^^^^^^^^^^^^^^^^
The field must only contain letters, numbers (integers) and spaces.

between
^^^^^^^
This rule checks the data for the field is within a range. The required options - min, max. ::

    $validations = array(
        'vote' => array( // vote is HTML input element name or id
            'caption' => _t('Vote');
            'value'   => $valueToCheck,
            'rules'   => array('mandatory', 'between'),
            'max'     => 0,
            'max'     => 5,
        ),
    ); // The error message will be shown as "'Vote' should be between 0 and 5".

custom
^^^^^^
It is used when a custom regular expression is needed. The required option - ``pattern``. ::

    $validations = array(
        'phone' => array(
            'caption'  => _t('Phone');
            'value'    => $valueToCheck,
            'rules'    => array('custom'),
            'pattern'  => '/^\(?([0-9])*\)?([ 0-9\-])*([0-9])+$/',
            'messages' => array(
                'custom' => _t('Phone number should have a valid format, e.g., (123) 456 7890'),
                // if this is not specified, the default message "'Phone' should be a valid format." will be shown.
            ),
        ),
    );

date
^^^^
This checks the field is a valid date. The option is ``dateFormat`` - ``y-m-d``, ``d-m-y`` or ``m-d-y`` where separators can be a period, dash, forward slash, but not allowed space. Default is ``y-m-d``. ::

    $validations = array(
        'date' => array(
            'caption'   => _t('Date');
            'value'     => $valueToCheck,
            'rules'     => array('date'),
            'dateFormat'=> 'd-m-y', // if not given, the default is y-m-d
        ),
    );

datetime
^^^^^^^^
This checks the field is a valid date and time. The option is ``dateFormat`` - ``y-m-d``, ``d-m-y`` or ``m-d-y`` where separators can be a period, dash, forward slash, but not allowed space. Default is ``y-m-d``. The option ``timeFormat`` can also given - ``12`` or ``24``. See `time <#id6>`_. ::

    $validations = array(
        'date' => array(
            'caption'   => _t('Date');
            'value'     => $valueToCheck,
            'rules'     => array('datetime'),
            'dateFormat'=> 'd-m-y', // if not given, the default is y-m-d
            'timeFormat'=> '24', // 12 or 24; if not given, default is both which validates against both format
        ),
    );

domain
^^^^^^
This checks the field is a valid domain (alpha-numeric and dash only). It must start with letters and end with letters or numbers. ::

    $domain = array(
        'domain' => array(
            'caption'   => _t('Domain');
            'value'     => $valueToCheck,
            'rules'     => array('mandatory', 'domain'),
        ),
    ); // The error message will be shown as "'Sub-domain' should be a valid domain name with letters, numbers and dash only.".

email
^^^^^
This checks the field is a valid email address. ::

    $validations = array(
        'email' => array(
            'caption'   => _t('Email');
            'value'     => $valueToChecck,
            'rules'     => array('mandatory', 'email'),
        ),
    ); // The error message will be shown as "'Email' should be a valid format, e.g., username@example.com".

fileExtension
^^^^^^^^^^^^^
This rule allows you to check the uploaded file extension. The required option is ``extension`` - array of extensions. See example at `fileMaxDimension <#id4>`_.

fileMaxSize
^^^^^^^^^^^
This rule checks the uploaded file size meets the maximum allowed size. The require option is ``maxSize`` in MB. See example at `fileMaxDimension <#id4>`_.

fileMaxDimension
^^^^^^^^^^^^^^^^
This rule checks the width and height of the uploaded image file to not exceed the maximum image dimension allowed. The required options are ``maxWidth`` and ``maxHeight`` in pixels. ::

    $validations = array(
        'logo' => array(
            'caption'    => _t('Logo');
            'value'      => $valueToCheck, // $_FILES['logo']
            'rules'      => array('fileExtension', 'fileMaxSize', 'fileMaxDimension'),
            'extensions' => array('jpg', 'jpeg', 'png', 'gif'), // for the rule 'fileExtension'
            'maxSize'    => 20 // 20MB for the rule 'fileMaxSize'
            'maxWidth'   => 1280, // for the rule 'fileMaxDimension'
            'maxHeight'  => 986 // for the rule 'fileMaxDimension',
        ),
    );

fileExactDimension
^^^^^^^^^^^^^^^^^^
This rule checks the width and height of the uploaded image file to meet the image dimension specified. The required options are ``width`` and ``height`` in pixels.

fileMaxWidth
^^^^^^^^^^^^
This rule checks the width of the uploaded image file to not exceed the maximum image width allowed. The required option is ``maxWidth`` in pixels.

fileMaxHeight
^^^^^^^^^^^^^
This rule checks the height of the uploaded image file to not exceed the maximum image width allowed. The required option is ``maxHeight`` in pixels.

integer
^^^^^^^
The rule checks the field is a positive or negative integer. No decimal is allowed.

ip
^^
This rule checks the field is a valid IPv4 or IPv6 address. The required property is ``protocol`` - ``v4``, ``ipv4``, ``v6``, ``ipv6`` or ``both`` (default). ::

    $validations = array(
        'ip_addr' => array(
            'caption'  => _t('IP Address');
            'value'    => $valueToCheck,
            'rules'    => array('ip'),
            'protocol' => 'ipv4',
        ),
    );

mandatory
^^^^^^^^^
This checks the field is required. ``0`` is allowed. If you don’t want to allow ``0``, use the rule `notAllowZero <#id5>`_ in combination. ::

    $validations = array(
        'name' => array(
            'caption'   => _t('Name');
            'value'     => $nameValueToCheck,
            'rules'     => array('mandatory'),
        ),
        'country' => array(
            'caption'   => _t('Country');
            'value'     => $countryValueToCheck,
            'rules'     => array('mandatory'),
            'messages'  => array(
                'mandatory' => _t('Country must be selected.') // this overwrites the default message
            ),
        )
    );

mandatoryOne
^^^^^^^^^^^^
This checks at least one field of the field group is required. ::

    <!-- HTML -->
    <div id="phones">
        <input type="text" name="phones[]" />
        <input type="text" name="phones[]" />
    <div>

    ### PHP ###
    $post = _post($_POST);

    $validations = array(
        'phones[]' => array( // HTML id of the group element
            'caption'   => _t('Phone(s)');
            'value'     => $post['phones'],
            'rules'     => array('mandatoryOne'),
        ),
    );

mandatoryAll
^^^^^^^^^^^^
This checks all fields of the field group is required. ::

    <!-- HTML -->
    <div id="phones">
        <input type="text" name="phones[]" />
        <input type="text" name="phones[]" />
    <div>

    ### PHP ###
    $post = _post($_POST);

    $validations = array(
        'phones[]' => array( // HTML id of the group element
            'caption'   => _t('Phone(s)');
            'value'     => $post['phones'],
            'rules'     => array('mandatoryAll'),

max
^^^
This rule checks the data for the field is equal or less than a specific maximum number. The required option - ``max``. ::

    $validations = array(
        'max_vote' => array(
            'caption' => _t('Max. Vote');
            'value'   => $valueToCheck,
            'rules'   => array('mandatory', 'max'),
            'max'     => 5,
        ),
    );

maxLength
^^^^^^^^^
This rule checks the field string length is less than a specific length. The required option - ``max``. ::

    $validations = array(
        'password' => array(
            'caption' => _t('Password');
            'value'   => $valueToCheck,
            'rules'   => array('mandatory', 'minLength', 'maxLength'),
            'min'     => 8,
            'max'     => 20,
        ),
    );

min
^^^
This rule checks the data for the field is equal or greater than a specific minimum number. The required option - ``min``. ::

    $validations = array(
        'no_of_page' => array(
            'caption' => _t('No. of Pages');
            'value'   => $valueToCheck,
            'rules'   => array('min'),
            'min'     => 100,
        ),
    ); // The error message will be shown as "'No. of Pages' should be greater than or equal to 100.".

minLength
^^^^^^^^^
This rule checks the field string length is greater than a specific length. The required option - ``min``. ::

    $validations = array(
        'password' => array(
            'caption' => _t('Password');
            'value'   => $valueToCheck,
            'rules'   => array('mandatory', 'minLength'),
            'min'     => 8,
        ),
    );

exactLength
^^^^^^^^^^^
This rule checks that a string/array's length is equal to the specific length. The required option - ``length``. ::

    $validations = array(
        'code' => array(
            'caption'   => _t('Code'),
            'value'     => $valueToCheck,
            'rules'     => array('mandatory', 'exactLength'),
            'length'    => 6,
        ),
    );

naturalNumber
^^^^^^^^^^^^^
The rule checks the field is a positive integer starting from 1. No decimal is allowed.

notAllowZero
^^^^^^^^^^^^
This ensures that the field is not zero.

numeric
^^^^^^^
It checks the field is numeric.

numericDash
^^^^^^^^^^^
The field must only contain numbers (integers) and dashes.

numericSpace
^^^^^^^^^^^^
The field must only contain numbers (integers) and spaces.

positiveRationalNumber
^^^^^^^^^^^^^^^^^^^^^^
It checks the field is a positive numbers. It allows decimals.

rationalNumber
^^^^^^^^^^^^^^
It checks the field is a positive or negative numbers. It allows decimals.

time
^^^^
This checks the field is a valid 24-hr or 12-hr format. The optional option is ``timeFormat`` - ``12``, ``24`` or ``both`` where ``both`` is default. ::

    $validations = array(
        'time' => array(
            'caption'   => _t('Time');
            'value'     => $valueToCheck,
            'rules'     => array('time'),
            'timeFormat'=> '24',
        ),
    );

url
^^^
This rule checks for valid URL formats. It supports **http**, **http(s)** and **ftp(s)**. "**www**" must be included. ::

    $validations = array(
        'website'  => array(
            'caption' => _t('Company Website');
            'value'   => $valueToCheck,
            'rules'   => array('url'),
        ),
    );

username
^^^^^^^^
The rule is used to make sure that the field must not contain any special character, start with letters, end with letters and numbers. It can contain underscores (``_``), dashes (``-``) and periods (``.``) in the middle. ::

    $validations = array(
        'username' => array(
            'caption'   => _t('Username');
            'value'     => $valueToCheck,
            'rules'     => array('mandatory', 'username'),
        ),
    );

unique
^^^^^^
The rule is used to check if any duplicate record exists for a specific field in the database. ::

    $validations = array(
        'username' => array(
            'caption'   => _t('Username');
            'value'     => $valueToCheck,
            'rules'     => array('mandatory', 'username', 'unique'),
            'table'     => 'user', // table name to check in
            'field'     => 'username', // the field to be checked
            'id'        => $id, // Optional: id to be excluded in check
        ),
    );

wholeNumber
^^^^^^^^^^^
The rule checks the field is a positive integer starting from ``0``. No decimal is allowed. ::

    $validations = array(
        'price' => array(
            'caption'   => _t('Price');
            'value'     => $valueToCheck,
            'rules'     => array('mandatory', 'wholeNumber'),
        ),
    ); // The error message will be shown as "'Price' should be a positive integer.".


Custom Validation Rules
-----------------------

In addition to the core validation rules, you could also define your own custom validation functions in ``/app/helpers/validation_helper.php``. They will be auto-loaded. The custom validation rule must start with ``validate_``.

For example, ::

    $validations = array(
        'username' => array(
            'caption'   => _t('Username');
            'value'     => $valueToCheck,
            'rules'     => array('mandatory', 'username', 'validate_duplicateUsername'),
            'parameters' => array(
                'validate_duplicateUsername' => array($theEditId), // $theEditId will be the second argument to validate_duplicateUsername()
            ),
            'messages' => array(
                'validate_duplicateUsername' => _t('Username already exists. Please try another one.'),
            ),
        ),
    );

Then, you must define a function ``validate_duplicateUsername()`` in ``/app/helpers/validation_helper.php``, for example, ::

    /**
     * Custom validation function to check username is duplicate
     * @param string $value Username to be checked
     * @param integer $id The edit id if any
     * @return boolean TRUE for no duplicate; FALSE for duplicate
     */
    function validate_duplicateUsername($value, $id = 0) {
        $value = strtolower($value);
        if (empty($value)) {
            return true;
        }

        $qb = db_count('user')
            ->where()
            ->condition('LOWER(username)', strtolower($value));
        if ($id) {
            $qb->condition('id <>', $id);
        }

        return $qb->fetch() ? false, true;
    }

Alternatively, if you don't want to define a function, you could add it right in your form action handling as the code snippet below. In this case, you have to call ``Validation::addError('htmlIdOrName', 'Error message to be shown')``, but it is not recommended. ::

    if (form_validate($validations)) {
        $qb = db_count('user')
            ->where()
            ->condition('LOWER(username)', strtolower($value));

        if ($id) {
            $qb->condition('id <>', $id);
        }

        if ($qb->fetch()) {
            validation_addError('txtUsername', _t('Username already exists. Please try another one.'));
        } else {
            // No duplicate && success
        }
    }
