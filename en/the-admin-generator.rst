Day 12: The Bakckend
====================

With the addition we made in day 11 on Jobeet, the frontend
application is now fully useable by job seekers and job posters.
It's time to talk a bit about the backend application. Today,
thanks to the AdminBundle, we will develop a complete backend interface for Jobeet in
just one hour.

Installation of the AdminBundle
-------------------------------

This bundle, created by Thomas Rabaix, provides functionality for a backend application. 
Also we need to install it dependencies: MenuBundle, jQueryBundle and BluePrintBundle. 
Lets do it!

First, make sure you have ``Sonata`` and ``Knplabs`` directories, if not create them:

.. code-block:: bash

    $ mkdir src/Sonata
    $ mkdir src/Knplabs

Then, add the dependent bundles to the ``src/`` directory with git:

.. code-block:: bash

    $ git submodule add git@github.com:sonata-project/AdminBundle.git src/Sonata/AdminBundle
    $ git submodule add git@github.com:sonata-project/jQueryBundle.git src/Sonata/jQueryBundle
    $ git submodule add git@github.com:sonata-project/BluePrintBundle.git src/Sonata/BluePrintBundle
    $ git submodule add git@github.com:knplabs/MenuBundle.git src/Knplabs/Bundle/MenuBundle

Next, be sure to enable the bundles in your application kernel at ``app/AppKernel.php``

.. code-block:: php

    $bundles = array(
        ...
        new Sonata\jQueryBundle\SonatajQueryBundle(),
        new Sonata\BluePrintBundle\SonataBluePrintBundle(),
        new Sonata\AdminBundle\SonataAdminBundle(),
        new Knplabs\Bundle\MenuBundle\KnplabsMenuBundle(),
    );

We also need to register the new namespaces in ``app/autoload.php``

.. code-block:: php

    // app/autoload.php
    // ...

    $loader->registerNamespaces(array(
    // ...
    'Sonata'      => __DIR__.'/../src',
    'Knplabs'     => __DIR__.'/../src',
    ));

    // ...

Configuration
-------------

The bundle also contains several routes. Import them by adding the following
code to your application's routing file:

- Add the AdminBundle's routing definition

.. code-block:: yaml

    # app/config/routing.yml
    admin:
        resource: '@SonataAdminBundle/Resources/config/routing/sonata_admin.xml'
        prefix: /admin

    _sonata_admin:
        resource: .
        type: sonata_admin
        prefix: /admin

Now, install the assets of the different bundles: ``php app/console assets:install web --symlink``.
At this point you can access to the dashboard with the url: ``http://yoursite.local/admin/dashboard``.
