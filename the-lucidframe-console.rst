The LucidFrame Console
======================

As of version 1.11.0, PHPLucidFrame introduces command line based utilities that don’t need to be accessible from a web browser. The LucidFrame console provides built-in commands available to use and it allows you to create custom command-line commands as well.

.. note::
    - A command-line (CLI) build of PHP must be available on the system if you plan to use the Console.
    - ``php`` must be available in your system environment variables.

Before get started, make sure you can run the LucidFrame console. Assuming that you are currently at the root of  your LucidFrame application, simply run the Console with no argument: ::

    $ cd /path/to/yourapp
    $ php lucidframe

This produces this help message: ::

    PHPLucidFrame 2.2.0 by Sithu K.

    2.2.0
    PHP Version: 5.6.23
    The MIT License
    Simple, lightweight & yet powerful PHP Application Framework
    Copyright (c) 2014-2017, phplucidframe.com

Running a Built-in Command
--------------------------

Since the version 1.11.0, PHPLucidFrame added a basic Console command to generate a secret hash key. You should run it once you installed LucidFrame to re-generate your own secret key for your application. ::

    $ php lucidframe secret:generate

You can check the help for the command using an option ``-h`` or ``--help`` which is available for every command implemented. ::

    $ php lucidframe secret:generate -h

That produces the help message for the command ``secret:generate`` as below: ::

    PHPLucidFrame 2.2.0 by Sithu K.

    Usage:
      secret:generate [options]

    Options:
      -h, --help     Display the help message
      -m, --method   The hashing algorithm method (e.g. "md5", "sha256", etc..) [default: "md5"]
      -d, --data     Secret text to be hashed.

    Help:
      Generate a secret hash key

Any secret text can be given to the command using the option ``-d`` or ``--data``, and the hash method using the option ``-m`` or ``--method``, for example, ::

    $ php lucidframe secret:generate --data=any_scret_string --method=sha256

You can check all available command list by running ::

    $ php lucidframe list

Creating a Basic Command
------------------------

You can create your own console command. Let's create a simple console greeting command that greets you from the command line. Create ``/app/cmd/GreetCommand.php`` and add the following to it: ::

    _consoleCommand('demo:hello')
        ->setDescription('Greet someone')
        ->addArgument('name', 'Who do you want to greet?')
        ->addOption('yell', 'y', 'If set, the task will yell in uppercase letters', null, LC_CONSOLE_OPTION_NOVALUE)
        ->setDefinition(function(\LucidFrame\Console\Command $cmd) {
            $name = $cmd->getArgument('name');
            $msg = $name ? 'Hello ' . $name : 'Hello';
            $msg .= '!';

            if ($cmd->getOption('yell')) {
                $msg = strtoupper($msg);
            }

            _writeln($msg);
        })
        ->register();

``demo:hello`` is the command name. It accepts one optional argument ``name`` and one optional option ``yell``. For argument, you don't have to specify ``name`` in your command call, but you will have to provide ``--yell`` for the option. You could check help for the command before trying out. ::

    $ php lucidframe demo:hello -h

Now let's try to test the new console command by running ::

    $ php lucidframe demo:hello Sithu

Since "Sithu" will be passed to the ``name`` argument, this will print the following output to the command line. ::

    Hello Sithu!

You can also use the ``--yell`` option to make everything uppercase. ::

    $ php lucidframe demo:hello Sithu --yell

This prints: ::

    HELLO SITHU!
