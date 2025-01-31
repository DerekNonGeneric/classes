CMD5
====

Python's CMD (https://docs.python.org/2/library/cmd.html) is a very
useful package to create command line shells. However it
does not allow the dynamic integration of newly defined
commands. Furthermore, additions to CMD need to be done within the
same source tree. To simplify developping commands by a number of
people and to have a dynamic plugin mechnism, we developed cmd5.  It
is a rewrite on our ealier effords in cloudmesh client and cmd3.



Resources
---------

The source code for cmd5 is located in github:

* https://github.com/cloudmesh/cmd5

Creating a Python Development Environment
-----------------------------------------

We recommend that you use a virtualenv either with virtualenv or
pyenv. This is in detail documented in the Section :ref:`section_pyenv`.


Installation from source
------------------------


Cmd5 can easily deployed with pip::

  pip install cloudmesh.cmd5

In case you like to generate easily new cmd 5 commands we also
recommend you install the cloudmesh sys command with::

  pip install cloudmesh.sys


In case you like to work with the source please cone the following
directories from github::

  mkdir -p ~/github
  cd ~/github

  git clone https://github.com/cloudmesh/cloudmesh.common.git
  git clone https://github.com/cloudmesh/cloudmesh.cmd5.git
  git clone https://github.com/cloudmesh/cloudmesh.sys.git  

  cd ~/github/cloudmesh.common
  pip install .

  cd ~/github/cloudmesh.cmd5
  pip install .

  cd ~/github/cloudmesh.sys
  pip install .

The common directory contains some useful libraries, the cmd5
repository contains the shell, while the sys directory contains a
command to generate extensions to cloudmesh.


Execution
---------

To run the shell you can activate it with the cms command. cms stands
for cloudmesh shell::

    (ENV2) $ cms

It will print the banner and enter the shell::

    +-------------------------------------------------------+
    |   ____ _                 _                     _      |
    |  / ___| | ___  _   _  __| |_ __ ___   ___  ___| |__   |
    | | |   | |/ _ \| | | |/ _` | '_ ` _ \ / _ \/ __| '_ \  |
    | | |___| | (_) | |_| | (_| | | | | | |  __/\__ \ | | | |
    |  \____|_|\___/ \__,_|\__,_|_| |_| |_|\___||___/_| |_| |
    +-------------------------------------------------------+
    |                  Cloudmesh CMD5 Shell                 |
    +-------------------------------------------------------+

    cms>


To see the list of commands you can say::

    cms> help

To see the manula page for a specific command, please use::

    help COMMANDNAME

Create your own Extension
-------------------------

One of the most important features of CMD5 is its ability to extend it
with new commands.  This is done via packaged name spaces. We
recommend you name is cloudmesh.mycommand, where mycommand is the name
of the command that you like to create. This can easily be done while
using the *sys* command::

  cms sys command generate mycommand

It will download a template form cloudmesh called cloudmesh.bar and
generate a new directory cloudmesh.mycommand with all the needed files
to create your own command and register it dynamically with
cloudmesh. All you have to do is to cd into the directory and install
the code::

  cd cloudmesh.mycommand
  pip install .

Adding your own command is easy. It is important that all objects are
defined in the command itself and that no global variables be used in
order to allow each shell command to stand alone. Naturally you should
develop API libraries outside of the cloudmesh shell command and reuse
them in order to keep the command code as small as possible. We place
the command in::

    cloudmsesh/mycommand/command/mycommand.py

The directory `cloudmsesh/mycommand/command` must only have the
`mycommand.py` file and an `__init__.py` file in it. No other file should
be placed here. A ample api with a `Manager` class is placed in::

    cloudmsesh/mycommand/api/manager.py

You can use this template as start for your commands.

An example for the bar command is presented at:

* https://github.com/cloudmesh/cloudmesh.bar/blob/master/cloudmesh/bar/command/bar.py

It shows how simple the command definition is (bar.py)::

    from __future__ import print_function
    from cloudmesh.shell.command import command
    from cloudmesh.shell.command import PluginCommand

    class BarCommand(PluginCommand):

        @command
            def do_bar(self, args, arguments):
        """
        ::

          Usage:
                bar --file=FILE
                bar list

          This command does some useful things.

          Arguments:
              FILE   a file name

          Options:
              -f      specify the file

        """
        arguments.FILE = arguments['--file'] or None

        print(arguments)

        m = Manager()


        if arguments.FILE:
            print("option a")
            m.list(arguments.FILE)

        elif arguments.list:
            print("option b")
            m.list("just calling list without parameter")

An important difference to other CMD solutions is that our commands
can leverage (besides the standrad definition), docopts as a way to
define the manual page. This allows us to use arguments as dict and
use simple if conditions to interpret the command. Using docopts has
the advantage that contributors are forced to think about the command
and its options and document them from the start. Previously we did
not use but argparse and click. However we noticed that for our
contributors both systems lead to commands that were either not properly
documented or the developers delivered ambiguous commands that
resulted in confusion and wrong ussage by subsequent users. Hence, we do
recommend that you use docopts for documenting cmd5 commands. 
The transformation is enabled by the @command decorator that generates
a manual page and creates a proper help message for the shell
automatically. Thus there is no need to introduce a sepaarte help
method as would normally be needed in CMD while reducing the effort it
takes to contribute new commands in a dynamic fashion.

.. _e-cmd5:

Excersise
---------

ECMD5.1:
    Install cmd5 on your computer.

ECMD5.2:
    Write a new command with your firstname as the command name.

ECMD5.3:
    Write a new command and experiment with docopt syntax and argument
    interpretation of the dict with if conditions.

ECMD5.4:
    If you have useful extensions that you like us to add by default,
    please work with us.
