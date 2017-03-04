Bootstrapping
=============

The ``/app`` directory is auto-bootstrapped with PHPLucidFrame environment. If you have a new directory out of the app directory, all script files in that directory are not auto-bootstrapped. However you can manually bootstrap them. Let’s say you have a directory ``/scripts`` at the same level of the ``/app`` directory and you have a script file ``/scripts/example.php``, the file should have the following content. ::

    <?php
    chdir('../'); // change directory to the root directory from the current directory
    require_once('bootstrap.php');

    // Do something here …

Then the script has been bootstrapped with PHPLucidFrame environment.
