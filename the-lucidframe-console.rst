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

    PHPLucidFrame 3.0.0 by Sithu K.

    3.0.0
    PHP Version: 7.3.5
    The MIT License
    Simple, lightweight & yet powerful PHP Application Framework
    Copyright (c) 2014-2021, phplucidframe.com

Running a Built-in Command
--------------------------

Since the version 1.11.0, PHPLucidFrame added a basic Console command to generate a secret hash key. You should run it once you installed LucidFrame to re-generate your own secret key for your application. ::

    $ php lucidframe secret:generate

You can check the help for the command using an option ``-h`` or ``--help`` which is available for every command implemented. ::

    $ php lucidframe secret:generate -h

That produces the help message for the command ``secret:generate`` as below: ::

    PHPLucidFrame 3.0.0 by Sithu K.

    Usage:
      secret:generate [options]

    Options:
      -h, --help     Display the help message                       

    Help:
      Generate a secret key

If you want to generate a new secret key, run, ::

    $ php lucidframe secret:generate

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

As of version 2.2, it supports class to add command definition. You can create a class in ``/app/cmd/classes/``. Let's say ``/app/cmd/classes/Greet.php`` ::

    <?php

    use LucidFrame\Console\CommandInterface;
    use LucidFrame\Console\Command;

    class Greet implements CommandInterface
    {
        public function execute(Command $cmd)
        {
            $name = $cmd->getArgument('name');
            $msg = $name ? 'Hello ' . $name : 'Hello';
            $msg .= '!';

            if ($cmd->getOption('yell')) {
                $msg = strtoupper($msg);
            }

            _writeln($msg);
        }
    }

Then you need to connect that class to the command definition. Set class name to ``setDefinition()`` in ``/app/cmd/GreetCommand.php`` you created above. ::

    _consoleCommand('demo:hello')
        ->setDescription('Greet someone')
        ->addArgument('name', 'Who do you want to greet?')
        ->addOption('yell', 'y', 'If set, the task will yell in uppercase letters', null, LC_CONSOLE_OPTION_NOVALUE)
        ->setDefinition('Greet')
        ->register();

It is useful when your command has more complicated functions and you can write logic neatly in your class.
