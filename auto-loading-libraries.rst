Auto-loading Libraries
======================

There are two ways of auto-loading third-party libraries:

#. Using Composer
#. Using Custom Autoloader


Composer
--------

Composer support is automatically initialized by default. It looks for Composer's autoload file at ``/vendor/autoload.php``. You just need to add libraries to ``composer.json``.

Custom Autoloader
-----------------

If you are not using composer, you can update ``/inc/autoload.php`` to load a library. The following is a few steps to do:

#. Download any third-party library
#. Put it in the folder ``/third-party``
#. Load the file using the helper function ``_loader()`` in ``/inc/autoload.php``

Let's say for example, you are trying to integrate the library `PHP-JWT <https://github.com/firebase/php-jwt>`_.

#. Download the library from downloading the library from `<https://github.com/firebase/php-jwt/tags>`_.
#. Unzip and put the folder in ``/third-party`` as ``/third-party/php-jwt-x.y.z`` (``x.y.z`` is the library version number you downloaded). However, you can also rename it as ``/third-party/php-jwt``.
#. Add ``_loader('JWT',  THIRD_PARTY . 'php-jwt-x.y.z/src/');`` in ``/inc/autoload.php``.  This will load ``/third-party/php-jwt-x.y.z/src/JWT.php``.
#. Then, you can try the following code. ::

    use Firebase\\JWT\\JWT;

    $key = "example_key";
    $payload = array(
        "iss" => "http://example.org",
        "aud" => "http://example.com",
        "iat" => 1356999524,
        "nbf" => 1357000000
    );

    /**
    * IMPORTANT:
    * You must specify supported algorithms for your application. See
    * https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40
    * for a list of spec-compliant algorithms.
    */
    $jwt = JWT::encode($payload, $key);
    _pr($jwt);

    $decoded = JWT::decode($jwt, $key, array('HS256'));
    _pr($decoded);

    /*
    NOTE: This will now be an object instead of an associative array. To get
    an associative array, you will need to cast it as such:
    */
    $decoded_array = (array) $decoded;
    _dpr($decoded_array);
