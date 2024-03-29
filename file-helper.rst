File Helper
===========

PHPLucidFrame provides a basic file upload handling using generic form and AsynFileUploader that can be used in AJAX form.

File Upload Form and File Handling
----------------------------------

PHPLucidFrame provides a basic file upload handling using generic form. First of all, you could define maximum file upload size, upload directory and required dimension (if image upload). ::

    # /inc/constants.php
    define('MAX_FILE_UPLOAD_SIZE', 20); // in MB

    define('PHOTO_DIR', FILE . 'photos/'); // assuming that you have this directory
    define('WEB_PHOTO_DIR', WEB_ROOT . 'files/photos/');

    # /app/inc/site.config.php
    // $lc_photoDimensions - desired width and height for the uploaded photos
    $lc_photoDimensions = array('400x300', '200x150'); // 3 photos will be uploaded according to the defined dimensions

Since 1.6, a new configuration variable ``$lc_imageFilterSet`` is added. ::

    # $lc_imageFilterSet – Default image filter setting that applies to image upload
    $lc_imageFilterSet = array(
        'maxDimension' => '800x600', // or null for client original image size to keep, but not recommended
        'resizeMode'   => FILE_RESIZE_BOTH,
        'jpgQuality'   => 75
    );

The uploaded images will be stored under the following directories according to the above configuration: ::

    /path_to_webserver_document_root
        /files
            /photos
                /400x300  <- 400x300 thumbnails
                /200x150  <- 200x150 thumbnails
                |-- xxxxxx.jpg <- primary/original images according to $lc_imageFilterSet[maxDimension]
                |-- ………
                |-- xxxxxx.jpg

.. note::
    - The upload directory must be writable.

Generic File Upload
-------------------

According to the framework-recommended page structure, you could have the following structure for your file uploading page: ::

    /path_to_webserver_document_root
        /app
            /photo
            |-- action.php
            |-- index.php
            |-- view.php

Your ``/app/photo/index.php`` will look like this: ::

    <?php
    /** app/photo/index.php */

    $view = _app('view');

    $pageTitle = _t('Photo Uploader');
    _app('title', $pageTitle);

    // You could have query here to retrieve the existing file upload data from db

    $view->data = array(
        'pageTitle' => $pageTitle
    );

Here is how your ``/app/photo/view.php`` will go: ::

    <!-- app/photo/view.php -->
    <form method="post" name="form-upload" id="form-upload" enctype="multipart/form-data" class="no-ajax">
        <?php if ($msg = flash_get()) { ?>
            <?php echo $msg; ?>
        <?php } else { ?>
            <div class="message error"></div>
        <?php } ?>
        <table cellpadding="0" cellspacing="0" class="form">
            <tr>
                <td class="label"><?php echo _t('Photo'); ?></td>
                <td class="labelSeparator">:</td>
                <td class="entry">
                    <input type="file" name="photo" id="photo" />
                </td>
            </tr>
            <tr>
                <td colspan="2"></td>
                <td class="entry">
                    <button type="submit" id="btn-upload" name="btn-upload"><?php echo _t('Upload'); ?></button>
                </td>
            </tr>
        </table>
        <?php form_respond('form-upload', validation_get('errors')); ?>
    </form>

.. note::
    - You will have to add ``class="no-ajax"`` and ``enctype="multipart/form-data"`` to the form tag because this file upload process needs normal HTTP request.
    - ``action.php`` has to be included explicitly.

Finally, you need the form upload process handling in ``/app/photo/action.php`` like below: ::

    <?php
    /** app/photo/action.php */

    if (_isHttpPost()) {
        $photo = $_FILES['photo'];

        $validations = array(
            'photo' => array(
                'caption'       => _t('Photo'),
                'value'         => $photo,
                'rules'         => array('mandatory', 'fileExtension', 'fileMaxSize'),
                'extensions'    => array('jpg', 'jpeg', 'png', 'gif'),
                'maxSize'       => MAX_FILE_UPLOAD_SIZE,
                'messages'      => array(
                    'mandatory' => _t('Please select a photo.')
                ),
            ),
        ),

        if (form_validate($validations)) {
            $file = _fileHelper();

            // set image dimension to resize
            $file->set('dimensions', _cfg('photoDimension'));

            // set file upload directory; this should be defined in /inc/constants.php
            $file->set('uploadDir', PHOTO_DIR); // optional; default to `/files/tmp/`

            // image resize mode:
            // FILE_RESIZE_BOTH (by default) - resize to the fitted dimension to the given dimension
            // FILE_RESIZE_WIDTH - resize to the given width, but height is aspect ratio of the width
            // FILE_RESIZE_HEIGHT - resize to the given height, but width is aspect ratio of the height
            $file->set('resize', FILE_RESIZE_BOTH);

            $uploads = $file->upload($photo);
            /**
            $upload will return in the format:
            array(
                'name'              => 'Name of the input element',
                'fileName'          => 'The uploaded file name',
                'originalFileName'  => 'The original file name',
                'extension'         => 'The selected and uploaded file extension',
                'dir'               => 'The uploaded directory',
            )
            */
            if ($uploads) {
                $data = array(
                    'image' => $uploads['fileName'];
                );

                if (db_save('your_table', $data)) {
                    form_set('success', true);
                    flash_set(_t('The photo has been uploaded.'));

                    _redirect(); // or _redirect('self')
                    // redirect to the current page itself
                    // and will show the flash message set above.
                }
            } else {
                $error = $file->getError();
                Validation::addError('photo', $error['message']);

                form_set('error', validation_get('errors'));
            }
        } else {
            form_set('error', validation_get('errors'));
        }
    }

AsynFileUploader (Asynchronous File Uploader)
---------------------------------------------

The file helper in the previous section is not compatible with AJAX form. Since version 1.3, PHPLucidFrame added a new feature "**AsynFileUploader**" that helps you to upload a file in smarter way with instant preview and that is compatible with AJAX form.

Firstly, you can have a few image-related configurations in ``/app/inc/site.config.php`` as described in the previous section `File Upload Form and File Handling <#file-upload-form-and-file-handling>`_.

Create an instance of the class **AsynFileUploader** in ``/app/photo/index.php`` and pass it to view. For example, see `/app/example/asyn-file-uploader/index.php <https://github.com/phplucidframe/phplucidframe/blob/master/app/example/asyn-file-uploader/index.php>`_.::

    <?php
    /** app/photo/index.php */

    $view = _app('view');

    $pageTitle = _t('AsynFileUploader');
    _app('title', $pageTitle);

    # The constructor argument
    # string/array The input file name or The array of property/value pairs
    $photo = _asynFileUploader('photo');

    # Button caption; default to "Choose File"
    $photo->setCaption('Choose Image');

    # Max file upload size; default to 10MB
    $photo->setMaxSize(MAX_FILE_UPLOAD_SIZE);

    # Image dimension to resize
    # $lc_photoDimensions could be defined in /app/inc/site.config.php (see in the previous section).
    # This is not required for the non-image file uploads
    $photo->setDimensions($lc_photoDimensions);

    # Allowed file extensions; default to any file
    $photo->setExtensions(array('jpg', 'jpeg', 'png', 'gif'));

    # The file uploaded directory; default to /files/tmp
    # PHOTO_DIR could be defined in /app/inc/site.config.php (see in the previous section).
    $photo->setUploadDir(PHOTO_DIR);

    # The button #btnSubmit will be disabled while the upload is in progress
    $photo->setButtons('btn-upload');

    # The uploaded file name is displayed or not below the file upload button; default is true
    $photo->isFileNameDisplayed(false);

    # The uploaded file name is allowed to delete or not; default is true;
    # The delete icon will be displayed when it is true
    $photo->isDeletable(false);

    # The OnUpload hook which could be defined in /app/helpers/file_helper.php
    # This hook runs when the file is uploaded (See The onUpload hook section)
    $photo->setOnUpload('example_photoUpload');

    # The OnDelete hook which could be defined in /app/helpers/file_helper.php
    # This hook runs when the file is delete (See The onDelete hook section)
    $photo->setOnDelete('example_photoDelete');

    // You could have query here to retrieve the existing file upload data from db

    # If there is any previously uploaded file, set it using setValue()
    # @see /app/example/asyn-file-uploader/index.php
    # @see /app/example/asyn-file-uploader/view.php
    if (!empty($image)) {
        $photo->setValue($image->pimgFileName, $image->pimgId);
    }

    $view->data = array(
        'pageTitle' => $pageTitle,
        'photo' => $photo
    );

Call the instance method ``html()`` at where you want to display the file uploader. Normally it is in your view layer, for example, `/app/example/asyn-file-uploader/view.php <https://github.com/phplucidframe/phplucidframe/blob/master/app/example/asyn-file-uploader/view.php>`_. ::

    <form id="form-async-upload" method="post">
        <div class="message error"></div>
        <div class="table">
            <div class="row">
                <?php $photo->html() ?>
            </div>
            <div class="row">
                <input type="submit" id="btn-upload" name="btn-upload" value="<?php echo _t('Submit'); ?>" class="button green" />
            </div>
        </div>
        <?php form_token(); ?>
    </form>

As the form In the above coding is attached to AJAX and the form action attribute is not explicitly defined, it will submit to action.php in the same level directory when the button ``#btn-upload`` is clicked, for example, `/app/example/asyn-file-uploader/action.php <https://github.com/phplucidframe/phplucidframe/blob/master/app/example/asyn-file-uploader/action.php>`_.

The following is an example code for the possible ``action.php`` where you will have to use the name which you provided to the **AsynFileUploader** constructor in the previous code example, i.e., photo. LucidFrame automatically adds some  additional file upload information upon form submission that you can get them from the ``POST`` array. See the code below. ::

    <?php
    /** app/photo/action.php */

    if (_isHttpPost()) {
        $post = _post();

        $validations = array(
            'photo' => array(
                'caption' => _t('Image') ,
                'value' => $post['photo'],
                'rules' => array('mandatory') ,
            )
        );

        if (form_validate($validations) === true) {

            // # You can get the uploaded file information as below
            // $post['photo']            = The uploaded file name saved in disk
            // $post['photo-id']         = The ID in database related to the previously uploaded file
            // $post['photo-dimensions'] = (Optional) Array of dimensions used to resize the images uploaded
            // $post['photo-dir']        = The directory where the file(s) are saved, encoded by base64_encode()
            // $post['photo-fileName']   = The same value of $post['photo']
            // $post['photo-anyKey']     = if you set it using AsynFileUploader->setHidden('anyKey', 'anyValue')

            // ## Do database operation here ###
            $data = array(
                'image' => $post['photo'];
            );

            if (db_save('your_table', $data)) {
                form_set('success', true);
                form_set('message', _t('The photo has been saved.'));
            }
        } else {
            form_set('error', validation_get('errors'));
        }
    }

    form_respond('form-async-upload');

PHP Hooks for AsynFileUploader
------------------------------

There are some available hooks to be run during file handling process of AsynFileUploader.

The onUpload hook
^^^^^^^^^^^^^^^^^

The hook is to do database operation regarding to the uploaded files and it runs just after file is uploaded. It can be defined in ``/app/helpers/file_helper.php`` and the hook function name has to be given in the method call ``setOnUpload()``. The two arguments will be passed to your function.

+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| Argument      | Type      | Description                                                                                                                   |
+===============+===========+===============================================================================================================================+
| Argument 1    | array     | The array of the following keys of the uploaded file information:                                                             |
|               |           |                                                                                                                               |
|               |           | - ``name`` Name of the input element                                                                                          |
|               |           | - ``fileName`` The uploaded file name                                                                                         |
|               |           | - ``originalFileName`` The original file name                                                                                 |
|               |           | - ``extension`` The selected and uploaded file extension                                                                      |
|               |           | - ``dir`` The uploaded directory                                                                                              |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| Argument 2    | array     | The POSTed information:                                                                                                       |
|               |           |                                                                                                                               |
|               |           | - ``{name}`` Array of the file names uploaded and saved in drive                                                              |
|               |           | - ``{name}-id`` Optional array of the database value IDs (if a file have previously been uploaded)                            |
|               |           | - ``{name}-dimensions`` Optional array of the file dimensions in WxH (it will not be available if it is not an image file)    |
|               |           | - ``{name}-{field_name}`` Optional hidden values                                                                              |
|               |           |                                                                                                                               |
|               |           | If you set the name "**photo**" to the **AsynFileUploader** constructor, the keys will be ``photo``, ``photo-id`` and         |
|               |           | ``photo-dimensions``. If you set ``AsynFileUploader->setHidden('key', 'value')``, you can get it here using ``photo-key``.    |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+

The hook must return an array of IDs.

For example, assuming that ``post`` table has ``image`` field which stores an uploaded image file name, that field will be updated when a new image is uploaded for an existing post by using ``onUpload`` hook as below: ::

    // app/post/index.php
    $post = db_find('post', $id);

    $image = _asynFileUploader('image');
    $image->setOnUpload('post_imageUpload');
    $image->setHidden('postId', $post->id); // This will be available to the second argument (array) to post_imageUpload() as key "image-postId"
    if ($post->image) {
        $image->setValue($post->image, $post->id);
    }

    // app/helpers/file_helper.php
    function post_imageUpload($file, $post)
    {
        if (isset($post['image-postId']) && $post['image-postId']) {
            # Save new file names in db
            db_update('post', array(
                'id' => $post['image-postId'],
                'image' => $file['fileName']
            ), $useSlug = false);

            return $post['photo-postId'];
        }

        return 0;
    }

The onDelete hook
^^^^^^^^^^^^^^^^^

This hook is to do database operation regarding to the deleted files. It runs when delete button is clicked and just after file is deleted. It can be defined in ``/app/helpers/file_helper.php`` and the hook function name has to be given in the method call ``setOnDelete()``. An argument will be passed to your function:

+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| Argument      | Type      | Description                                                                                                                   |
+===============+===========+===============================================================================================================================+
| Argument 1    | mixed     | The ID related to the file deleted to delete from the database table.                                                         |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+

For example, assuming that ``post`` table has ``image`` field which stores an uploaded image file name, that field will be nulled when the image is deleted by using ``onDelete`` hook as below: ::

    // app/post/index.php
    $post = db_find('post', $id);

    $image = _asynFileUploader('image');
    $image->setOnDelete('post_imageDelete');
    if ($post->image) {
        $image->setValue($post->image, $post->id);
        // The second arugment to setValue() will be avaialble to the onDelete hook post_imageDelete()
    }

    // app/helpers/file_helper.php
    function post_imageDelete($id)
    {
        if ($id) {
            return db_update('post', array(
                'id' => $id,
                'image' => null,
            ));
        }

        return false;
    }

.. note::
    - See `the example code at /app/helpers/file-helper.php <https://github.com/phplucidframe/phplucidframe/blob/master/app/helpers/file_helper.php>`_.

Javascript Hooks for AsynFileUploader
-------------------------------------

Besides the server-side hooks, there are some available javascript hooks to be run during file handling process of AsynFileUploader. They are ``afterUpload``, ``afterDelete`` and ``onError``. Each can be defined using ``LC.AsynFileUploader.addHook(name, hook, function)`` where the parameters are:

+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| Argument      | Type      | Description                                                                                                                   |
+===============+===========+===============================================================================================================================+
| name          | string    | The name you given for ``AsynFileUploader``.                                                                                  |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| hook          | string    | The hook name ``afterUpload``, ``afterDelete`` and ``onError``.                                                               |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| function      | function  | The callback function to be called                                                                                            |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+

The afterUpload hook
^^^^^^^^^^^^^^^^^^^^

The hook runs just after file is uploaded. It can be defined using ``LC.AsynFileUploader.addHook(yourInputName, 'afterUpload', callback)``. The following two arguments ``name`` and ``file`` will be passed to your callback function.

+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| Argument      | Type      | Description                                                                                                                   |
+===============+===========+===============================================================================================================================+
| name          | string    | The input element name you given for ``AsynFileUploader``                                                                     |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| file          | object    | The uploaded file information.                                                                                                |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| file.name     | string    | The file input name                                                                                                           |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| file.id       | string    | The HTML id for the file browsing button                                                                                      |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| file.value    | string    | The uploaded file name                                                                                                        |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| file.savedId  | mixed     | The ID in the database related to the uploaded file (if any)                                                                  |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| file.fileName | string    | The original file name to be displayed                                                                                        |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| file.extension| string    | The uploaded file extension                                                                                                   |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| file.url      | string    | The actual file URL                                                                                                           |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| file.caption  | string    | The caption if the uploaded file is image                                                                                     |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+

The afterDelete hook
^^^^^^^^^^^^^^^^^^^^

The hook runs just after file is deleted. It can be defined using ``LC.AsynFileUploader.addHook(yourInputName, 'afterDelete', callback)``. The following two arguments ``name`` and ``data`` will be passed to your callback function.

+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| Argument      | Type      | Description                                                                                                                   |
+===============+===========+===============================================================================================================================+
| name          | string    | The input element name you given for ``AsynFileUploader``                                                                     |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| data          | object    | The uploaded file information.                                                                                                |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| data.name     | string    | The file input name                                                                                                           |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| data.success  | boolean   | ``true`` if file deletion succeeded; otherwise ``false``                                                                      |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| data.error    | string    | The error message if file deletion failed                                                                                     |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| data.id       | mixed     | The ID deleted from database                                                                                                  |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| data.value    | string    | The file name deleted from hard drive                                                                                         |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+

The onError hook
^^^^^^^^^^^^^^^^

The hook runs when the file upload fails with error. It can be defined using ``LC.AsynFileUploader.addHook(yourInputName, 'onError', callback)``. The two arguments ``name`` and ``error`` will be passed to your callback function.

+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| Argument      | Type      | Description                                                                                                                   |
+===============+===========+===============================================================================================================================+
| name          | string    | The input element name you given for ``AsynFileUploader``                                                                     |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| error         | object    | The error object                                                                                                              |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| error.id      | string    | The HTML ID which is generally given the validation key option in PHP                                                         |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| error.plain   | string    | The error message in plain format                                                                                             |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+
| error.html    | mixed     | The error message in HTML format                                                                                              |
+---------------+-----------+-------------------------------------------------------------------------------------------------------------------------------+

.. note::
    - If you defined this, the error message will not be shown until you code to show the error message in the callback function.
    - See `the example code in /app/example/asyn-file-uploader/index.php <https://github.com/phplucidframe/phplucidframe/blob/master/app/example/asyn-file-uploader/index.php>`_
