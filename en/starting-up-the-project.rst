Day 1: Starting up the Project
==============================

Introduction
------------

The `symfony <http://symfony.com/>`_ Framework has 
been an Open-Source project for more than four years and has 
become one of the most popular PHP frameworks thanks to its 
great features and great documentation.

This book describes the creation of a web application with the
Symfony2 framework, step-by-step from the specifications to the
implementation. It is targeted at beginners who want to learn
Symfony2, understand how it works, and also learn about the best web
development practices.

The application to be designed could have been yet another blog
engine. But we want to use Symfony2 on a useful project. The goal is
to demonstrate that Symfony2 can be used to develop professional
applications with style and little effort.

We will keep the content of the project secret for another day as
we already have much for now. However, let's give it a name:
**Jobeet**.

Each day of this book is meant to last between one and two hours,
and will be the occasion to learn Symfony2 by coding a real website,
from start to finish. Every day, new features will be added to the
application, and we'll take advantage of this development to
introduce you to new Symfony2 functionalities as well as good
practices in Symfony2 web development.

This Book is different
----------------------

Remember the early days of PHP4. Ah, la
`Belle Epoque <http://en.wikipedia.org/wiki/Belle_Époque>`_! PHP
was one of the first languages dedicated to the web and one of the
easiest to learn.

But as web technologies evolve at a very fast pace, web developers
need to keep up with the latest best practices and tools. The best
way to learn is of course by reading blogs, tutorials, and books.
We have read a lot of these, be they written for PHP, Python, Java,
Ruby, or Perl, and many of them fall short when the author starts
giving snippets of codes as examples.

You are probably used to reading warnings like:

"For a real application, don't forget to add validation and proper
error handling."

or

"Security is left as an exercise to the reader."

or

"You will of course need to write tests."

What? These things are serious business. They are perhaps the most
important part of any piece of code. And as a reader, you are left
alone. Without these concerns taken into account, the examples are
much less useful. You cannot use them as a good starting point.
That's bad! Why? Because security, validation, error handling, and
tests, just to name a few, take care to code right.

In this book, you will never see statements like those as we will
write tests, error handling, validation code, and be sure we
develop a secure application. That's because Symfony2 is about code,
but also about best practices and how to develop professional
applications for the enterprise. We will be able to afford this
luxury because Symfony2 provides all the tools needed to code these
aspects easily without writing too much code.

Validation, error handling, security, and tests are first-class
citizens in Symfony2, so it won't take us too long to explain. This
is just one of many reasons why to use a framework for "real life"
projects.

All the code you will read in this book is code you could use for a
real project. We encourage you to copy and paste snippets of code
or steal whole chunks.

What for Today?
---------------

We won't write PHP code. But even without writing a single line of
code, you will start understanding the benefits of using a
framework like Symfony2, just by bootstrapping a new project.

The objective of this day is to setup the development environment
and display a page of the application in a web browser. This
includes installation of Symfony2, creation of an application, and
web server configuration.

As this book will mostly focus on the Symfony2 framework, we will
assume that you already have a solid knowledge of PHP 5 and Object
Oriented programming.

Prerequisites
-------------

Before installing Symfony2, you need to check that your computer has
everything installed and configured correctly. Take the time to
conscientiously read this day and follow all the steps required to
check your configuration, as it may save your day further down the
road.

Third-Party Software
~~~~~~~~~~~~~~~~~~~~

First of all, you need to check that your computer has a friendly
working environment for web development. At a minimum, you need a
web server (Apache, for instance), a database engine (MySQL,
PostgreSQL, SQLite, or any
`PDO <http://www.php.net/PDO>`_-compatible database engine), and
PHP 5.3.2 or later.

Command Line Interface
~~~~~~~~~~~~~~~~~~~~~~

The Symfony2 framework comes bundled with a console component that
automates a lot of work for you. If you are a Unix-like OS user,
you will feel right at home. If you run a Windows system, it will
also work fine, but you will just have to type a few commands at
the ``cmd`` prompt.

.. note::

    Unix shell commands can come in handy in a
    Windows  environment. If you would like to use tools like
    ``tar``, ``gzip`` or ``grep`` on Windows, you can install
    `Cygwin <http://cygwin.com/>`_. The adventurous may also like to
    try Microsoft's
    `Windows Services for Unix <http://technet.microsoft.com/en-gb/interopmigration/bb380242.aspx>`_.


PHP Configuration
~~~~~~~~~~~~~~~~~

As PHP configurations can vary a lot from one OS to another, or
even between different Linux distributions, you need to check that
your PHP configuration meets the Symfony2 minimum requirements.

First, ensure that you have PHP 5.3.2 at a minimum installed by
using the ``phpinfo()`` built-in function or by running ``php -v``
on the command line. Be aware that on some configurations, you
might have two different PHP versions installed: one for the
command line, and another for the web.

Downloading and Installing Symfony2
-----------------------------------

Ready? Now let's start by downloading Symfony2. To get started even faster, we are
going to use the "Symfony Standard Edition". It is a Symfony2 project where all the
required libraries and some simple controllers are already included; the basic
configuration is also already done, so that you can start experimenting with Symfony2
immediately.

Download the `Symfony Standard Edition <http://symfony.com/download>`_, 
and unpack it in your root web directory. You
should now have a ``Symfony/`` directory::

    www/ <- your web root directory
        Symfony/ <- the unpacked archive
            app/
                cache/
                config/
                logs/
            src/
                Acme/
                    DemoBundle/
                        Controller/
                        Resources/
            vendor/
                 symfony/
            web/
            
To avoid some headaches further down the line, check that your configuration
can run a Symfony2 project smoothly by requesting the following URL:

    http://localhost/Symfony/web/config.php

Make sure you don't have any **Major Problems** listed, and
follow the optional recommendations listed if any.

.. figure:: ../images/01/welcome.png
   :alt: This should be your welcome screen

   This should be your welcome screen

Now, click on ``Configure your Symfony Application online``, 
and let's configure your symfony database connection:

.. figure:: ../images/01/configure.png
   :alt: Fill up all the required fields

   Fill up all the required fields
   
Click on ``next step``, and let's generate a CSRF key:

Now let's generate a a CSRF key  to prevent your site from getting CSRF attacks. 

.. note::
    If you know nothing about `CSRF <http://wikipedia.org/wiki/CSRF>`_,
    take the time to learn more about this security vulnerability.

.. figure:: ../images/01/csrf.png
   :alt: Generate a CSRF key

   Generate a CSRF key

    
Click again on ``next step``, and you're done!
Symfony2 should congratulate you for your hard work so far!

.. note::
    If your parameters.ini is not writable, Symfony2 will
    indicate that you need to paste the configuration options into the
    ``parameters.ini`` file located at: ``app/config/parameters.ini`` 

.. figure:: ../images/01/parameters.png
   :alt: Your initial configuration

   Your initial configuration

Your done! and  did you know you were actually running on Symfony2
all along? This configuration steps are actually a Symfony2 Bundle.
You'll learn the meaning of a ``Bundle`` in later chapters, everything
in Symfony2 revolves around ``Bundles``.

You can now read the documentation, re-run the configuration again
or run the demo to play with a Symfony2 mini Application (and see the
code inside it!)

.. figure:: ../images/01/congratulations.png
   :alt: Congratulations, Symfony2 is installed!

   Congratulations, Symfony2 is installed!

.. tip::

    If you create the Symfony2 project directory under the web
    root directory as we explained above, you won't need to configure
    your web server. Of course, for production environments, we 
    strongly advise you to configure your web server as explained in
    the :ref: `web server configuration section <web-server-configuration>`.

.. note::    
    
    Windows users are advised to run Symfony2 and to setup
    their new project in a path which contains no spaces. Avoid using
    the ``Documents and Settings`` directory, including anywhere under
    ``My Documents``.

Installation Verification
~~~~~~~~~~~~~~~~~~~~~~~~~

Now that Symfony2 is installed, check that everything is working by using the
Symfony2's console component to display the symfony version (note the capital `V`):

.. code-block:: text

    $ php app/console -V

On Windows:

.. code-block:: text

    c:\> php app/console -V

.. tip::

    If you are curious about what this command line tool can do for you, type
    ``php app/console list`` to list the available options and tasks:

    .. code-block:: text

        $ php app/console list

    On Windows:

    .. code-block:: text

        c:\> php app/console list
    
You can also go into Symfony2's shell mode (not available on Windows) by typing:

.. code-block:: text

    $ php app/console -s
    
    And press ``Ctrl+D`` to exit shell mode. 

The symfony console component is the developer's best friend. It provides a lot of
utilities that improve your productivity for day-to-day activities like
generating code, and much more.

Directory Structure Rights
~~~~~~~~~~~~~~~~~~~~~~~~~~

Before trying to access your newly created project, you need to set
the write permissions on the ``app/cache/`` and ``app/logs/`` directories to
the appropriate levels, so that your web server can write to them:

.. code-block:: text

    $ cd app/
    $ chmod 777 cache/ logs/

.. tip :: 

    **Tips for People using a SCM Tool**

    Symfony2 only ever writes in two directories of a Symfony2 project,
    ``app/cache/`` and ``app/logs/``. The content of these directories should be
    ignored by your SCM (by editing the ``svn:ignore`` property if you
    use Subversion for instance, or the ``.gitignore`` file if you use git).
    
    In git, your .gitignore would have these two lines:

    .. code-block:: text

        /app/cache/*
        /app/logs/*

Creating the Application Bundle
-------------------------------

Now, lets create the main application bundle by running the
``init:bundle`` command:

.. code-block:: text

    $ php app/console init:bundle "Acme\JobeetBundle" src

.. tip::

    Because the console shortcut file is executable, Unix users
    can replace all occurrences of ``php app/console`` by 
    ``app/console`` from now on.

    It's also easier to type and see all the commands in 
    ``shell mode`` (not available on Windows) by using
    ``app/console -s``. It has autocompletion too!


Based on the bundle name given as an *argument*, the
``init:bundle`` command creates the default directory structure
needed for the application under the ``src/Acme/JobeetBundle``
directory:

======================= ==============
 Directory              Description 
======================= ==============
``Controller/``         The bundle controllers 
``Resources/views/``    The application templates
``Resources/config/``   The bundle configuration files (routing, services, etc)
======================= ==============

Web Server Configuration: The ugly Way
----------------------------------------

If you have created the project directory somewhere under the
web root directory of your web server, you can already access the
project in a web browser.

Of course, as there is no configuration, it is very fast to set up,
but try to access the ``app/config/config.yml`` file in your browser
to understand the bad consequences of such a lazy attitude. If the
user knows that your website is developed with Symfony2, he will
have access to a lot of sensitive files.

**Never ever use this setup on a production server**, and read the
next section to learn how to configure your web server properly.


.. _web-server-configuration:

Web Server Configuration: The secure Way
----------------------------------------

A good web practice is to put under the web root directory only the
files that need to be accessed by a web browser, like stylesheets,
Javascripts and images. By default, we recommend to store these
files under the ``web/`` sub-directory of a Symfony2 project.

If you have a look at this directory, you will find
the two front controller files ``app.php`` and ``app_dev.php``.
The front controllers are the only PHP files that need to be under
the web root directory. All other PHP files can be hidden from the
browser, which is a good idea as far as Security is concerned.

Creating The Project Directory
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Before installing Symfony2, you first need to create a directory that will host
all the files related to Jobeet

.. code-block:: text

    $ mkdir -p /home/sfprojects/jobeet
    $ cd /home/sfprojects/jobeet

Or on Windows:

.. code-block:: text

    c:\> mkdir c:\dev\sfprojects\jobeet
    c:\> cd c:\dev\sfprojects\jobeet
    

Web Server Configuration
~~~~~~~~~~~~~~~~~~~~~~~~

Now it is time to change your Apache configuration, to
make the new project accessible to the world.

Locate and open the ``httpd.conf`` configuration file and add the
following configuration at the end:

.. code-block:: text

    # Be sure to only have this line once in your configuration
    NameVirtualHost 127.0.0.1:8080
    
    # This is the configuration for your project
    Listen 127.0.0.1:8080
    
    <VirtualHost 127.0.0.1:8080>
      DocumentRoot "/home/sfprojects/jobeet/web"
      DirectoryIndex app.php
      <Directory "/home/sfprojects/jobeet/web">
        AllowOverride All
        Allow from All
      </Directory>
    </VirtualHost>


On Windows, you need to replace the
``/home/sfprojects/jobeet/web`` with:

.. code-block:: text

    c:\dev\sfprojects\jobeet\web


This configuration makes Apache listen to port ``8080`` on your
machine, so, after restarting apache, the website will be
accessible at the following URL:

.. code-block:: text

    http://localhost:8080/

You can change ``8080`` to any number, but favour numbers greater
than ``1024`` as they do not require administrator rights.

    
.. tip::

    **Configure a dedicated Domain Name**

    If you are an administrator on your machine, it is better to setup
    Virtual Host instead of adding a new port each time you start a new
    project. Instead of choosing a port and add a ``Listen`` statement,
    choose a domain name (for instance the real domain name with
    ``.localhost`` added at the end) and add a ``ServerName`` statement:

    .. code-block:: text

        # This is the configuration for your project
        <VirtualHost 127.0.0.1:80>
          ServerName www.jobeet.com.localhost
          <!-- same configuration as before -->
        </VirtualHost>

    The domain name ``www.jobeet.com.localhost`` used in the Apache
    configuration has to be declared locally. If you run a Linux
    system, it has to be done in the ``/etc/hosts`` file. If you run
    Windows XP, this file is located in the
    ``C:\WINDOWS\system32\drivers\etc\`` directory.

    Add in the following line:

    .. code-block:: text

        127.0.0.1 www.jobeet.com.localhost


Test the New Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~

Restart Apache, and check that you now have access to the new
application by opening a browser and typing
``http://localhost:8080/app_dev.php/``, or
``http://www.jobeet.com.localhost/app_dev.php/`` depending on the
Apache configuration you chose in the previous section.

.. figure:: ../images/01/congratulations.png
   :alt: Congratulations, you've successfully configured Symfony2!
   
   Congratulations, you've successfully configured Symfony2!

.. tip::
    If you have the Apache ``mod_rewrite`` module installed,
    you can remove the ``app.php/`` part of the URL. This is possible
    thanks to the rewriting rules configured in the ``web/.htaccess``
    file.


You should try to access the application in the development
environment (see the next section for more information about
environments). Type in the following URL:

.. code-block:: text

    http://www.jobeet.com.localhost/app_dev.php/

The web debug toolbar should show in the bottom.

.. figure:: ../images/01/congratulations.png
   :alt: Web Debug Toolbar

   Web Debug Toolbar

The Environments
-----------------

If you have a look at the ``web/`` directory, you will find two PHP
files: ``app.php`` and ``app_dev.php``. These files are
called **front controllers**; all requests to the application are
made through them. But why do we have two front controllers
for each application?

Both files point to the same application but for different
**environments**. When you develop an application, except if you
develop directly on the production server, you need several
environments:


-  The **development environment**: This is the environment used by
   **web developers** when they work on the application to add new
   features, fix bugs, ...

-  The **test environment**: This environment is used to
   automatically test the application.

-  The **staging environment**: This environment is used by the
   **customer** to test the application and report bugs or missing
   features.

-  The **production environment**: This is the environment
   **end users** interact with.


What makes an environment unique? In the development environment
for instance, the application needs to log all the details of a
request to ease debugging, but the cache system must be disabled as
all changes made to the code must be taken into account right away.
So, the development environment must be optimized for the
developer. The best example is certainly when an
Exception Handling occurs. To help the
developer debug the issue faster, Symfony2 displays the exception
with all the information it has about the current request right
into the browser:

.. figure:: ../images/01/exception.png
   :alt: An exception in the dev environment

   An exception in the dev environment

But on the production environment, the cache layer must be
activated and, of course, the application must display customized
error messages instead of raw exceptions. So, the production
environment must be optimized for performance and the user
experience.

.. tip::
    If you open the front controller files, you will see that
    their content is the same except for the environment setting:

.. code-block:: html+php

        <?php
        // web/app.php

        require_once __DIR__.'/../app/bootstrap_cache.php';
        require_once __DIR__.'/../app/AppKernel.php';
        //require_once __DIR__.'/../app/AppCache.php';

        use Symfony\Component\HttpFoundation\Request;

        //$kernel = new AppCache(new AppKernel('prod', false));
        $kernel = new AppKernel('prod', false);
        $kernel->handle(Request::createFromGlobals())->send();


Using the Web Debug Toolbar
~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the development environment, the web debug toolbar is available at
the bottom of all pages. It displays a good summary of the profiling
data that gives you instant access to a lot of useful information when
something does not work as expected.

If the summary provided by the Web Debug Toolbar is not enough, click on
the token link (a string made of 13 random characters) to access the Web
Profiler.

.. figure:: ../images/01/token.png
   :alt: Click on this token to access the Web Profiler

Using the Web Profiler
~~~~~~~~~~~~~~~~~~~~~~

The Web Profiler is a visualization tool for profiling data that you can
use in development to debug your code and enhance performance; but it can
also be used to explore problems that occur in production. It exposes all
information collected by the profiler in a web interface.

.. figure:: ../images/01/Symfony2-web-profiler.png
   :alt: the web profiler

   the web profiler

Git
---

It is a good practice to use source version control when developing
a web application. Using a source version control allows us to:


-  work with confidence
-  revert to a previous version if a change breaks something
-  allow more than one person to work efficiently on the project
-  have access to all the successive versions of the application

In this section, we will describe how to use
`Git <http://git-scm.com/>`_ with Symfony2. If you
use another source code control tool, it will be quite easy to
adapt what we describe for Git.

.. tip::

    You can start work with git locally, without the need to host
    your repository on a server. If you want to use a remote server
    to keep/share your sources, I recommend `Github <http://www.github.com>`_


First, initialize the project:

.. code-block:: text

    $  cd /home/sfprojects/jobeet
    $  git init .

Then, remove the content of the ``cache/`` and ``log/`` directories
as we don't want to put them into the repository.

.. code-block:: text

    $ rm -rf app/cache/* app/logs/*

Now, make sure to set the write permissions on the cache and logs
directories to the appropriate levels so that your web server can
write to them:

.. code-block:: text

    $ chmod 777 app/cache/ app/logs/
    
As we will never want to commit files located in the ``app/cache/`` and
``app/logs/`` directories, you need to specify an ignore list:
    
Create a .gitignore file on the root directory and add:

.. code-block:: text
   
    app/cache/*
    app/logs/*

Now, stage all the files and directories:

.. code-block:: text

    $ git add .  

Finally, commit these changes to
your local repository:

.. code-block:: text

    $ git commit -m "My first commit"

You're done!

Final Thoughts
--------------

Well, time is over! Even if we have not yet started talking about
Symfony2, we have setup a solid development environment, we have
talked about web development best practices, and we are ready to
start coding.

Tomorrow, we will reveal what the application will do and talk
about the requirements we need to implement for Jobeet.