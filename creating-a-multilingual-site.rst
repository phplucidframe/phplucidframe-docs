Creating A Multi-lingual Site
=============================

Making your site supported multiple languages can reach a larger global audience. The internationalization and localization features in PHPLucidFrame makes it much easier.
In a single-language application, your code will look like this: ::

    <h1>Blog</h1>

For a multi-lingual application, you need to internationalize your code by using ``_t()`` function in your code: ::

    <h1><?php echo _t('Blog') ?></h1>

However, even though your application is single-language, you are always recommended to use ``_t()`` function so that you can easily switch from single-language to multi-language application at any time. You can pass multiple arguments to ``_t()`` like ``sprintf()`` for the dynamic value replacement.

PHPLucidFrame operates the translation process based on the configuration variables ``$lc_translationEnabled``, ``$lc_languages`` and ``$lc_defaultLang``. So, you don’t need to worry about overhead using ``_t()``.

Configuration of Internationalization
-------------------------------------

If you are code is ready for internationalization, you should then configure ``/inc/config.php`` for your language settings. There are three global configuration variables for this purpose - ``$lc_translationEnabled``, ``$lc_languages`` and ``$lc_defaultLang``. ::

    # $lc_translationEnabled - Enable/Disable language translation
    $lc_translationEnabled = true;
    # $lc_languages: Site languages (leave this blank for single-language site)
    $lc_languages = array(
        /* 'lang_code' => 'lang_name' */
        'en' => 'English',
        'my' => 'Myanmar',
    );
    # $lc_defaultLang: Default site language (leave blank for single-language site)
    # One of the key of $lc_languages
    $lc_defaultLang = 'en';

You can also use optional sub-language-code, in capital letters, that specifies the national variety (e.g., en-GB or en-US or zh-CN) for ``$lc_languages``. The sub-codes are typically linked with a hyphen.

Creating PO files
-----------------

The next step is to create your `po files <http://en.wikipedia.org/wiki/Gettext>`_, which contain all translatable strings in your application. PHPLucidFrame will look for your po files in the following location. ::

    /i18n/<language-code>.po

To create or edit your po files it’s recommended that you do not use your favorite editor. There are free tools such as `PoEdit <http://www.poedit.net/>`_ which make editing and updating your po files an easy task; especially for updating an existing po file.

Basically, you don’t need to have po file for the default language. As per the code sample in the release, there are two languages - English and Myanmar. As English is default language, there is no ``en.po`` file in the directory. You can copy and rename the file ``default.en.po`` to ``[your-language-code].po``. For example, if you want to create Japanese translation file, you would rename it to ``ja.po`` and add your translation strings in the file.

There are two types of translations:

- language-string-based translation
- custom key-based translation

Typically, you could use **language-string-based** translation in case a string is same meaning across the pages. For example, you have the "Cancel" buttons in several pages and if they have same meaning, you could have the following one statement in your language po file: ::

    msgid "Cancel"
    msgstr "Your language meaning for Cancel"

You just need to use the only msgid string for all of your "Cancel" buttons. ::

    <button type="button" name="btnCancel"><?php echo _t('Cancel'); ?></button>

However, you may want to set different meaning for different "Cancel" buttons depending on pages or actions. Then, you should use **custom key-based** translation. ::

    msgid "cancel-at-this-page"
    msgstr "Your language meaning for Cancel"

    msgid "cancel-at-another-page"
    msgstr "Your language different meaning for Cancel"

In this case, you must use the appropriate key for your "Cancel" buttons in your coding and you must also have a po file for your default language. ::

    <button type="button" name="btnCancel">
        <?php echo _t('cancel-at-this-page'); ?>
    </button>

    <!-- at another page -->
    <button type="button" name="btnCancel">
        <?php echo _t('cancel-at-another-page'); ?>
    </button>

.. note::
    - If you updated your po file, you have to clear browser cookie or session to take effect.

Translation of Long Paragraphs
------------------------------

The po files are useful for short messages, if you find you want to translate long paragraphs, or even whole pages - PHPLucidFrame has a way to handle it. Let’s say you have a page "About Us" for the whole page translation, you can create a template file in the directory ``/i18n/ctn/<language-code>/``. ::

    /i18n/ctn/<language-code>/about-us.<language-code>

Then, you just need to use ``_tc()`` function where you want to appear the paragraphs. It will render the current default language file content. ::

    echo _tc('about-us');

This function is available to use for dynamic value replacement, for example, you could have the placeholders ``:contact-url`` and ``:home-url`` in the file content, you can pass the values to the function to replace them. ::

    echo _tc('about-us', array(':contact-url' => _url('contact'), ':home-url' => HOME));

Switching the Site Language
---------------------------

Letting your site visitors switch their preferred language is a must for multi-language sites; by clicking the flag or by selecting the option from a language drop-down. There is a Javascript API available - ``LC.Page.languageSwitcher()`` by passing the language code being switched, for example, ::

    $(function() {
        $('#language-combo').change(function () {
            LC.Page.languageSwitcher($(this).val());
        });
    });

You could also generate hyperlinks to click on the language flags. For this case, you can use the framework function ``_self(NULL, $theLanguageCodeToBeSwitched)`` to get the current URL replaced by the language code. You could check the sample code in at ``/app/inc/tpl/header.php``.

.. note::
    - For dynamic content generation from database, you are suggested to check the sample codes in the release at ``/app/admin``.
    - PHPLucidFrame stores all translation strings read from po file in session. If you updated the po file, you need to clear your session to take it affect.

