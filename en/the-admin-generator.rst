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

The CRUD Controller
-------------------

A CRUD controller class is just an empty class with no methods. However, you can easily add here
new action or overwrite the default CRUD actions. Just creat a controller for each entity:

.. code-block:: php

    <?php
    
    //src/SfTuts/JobeetBundle/Controller/JobAdminController.php

    namespace SfTuts\JobeetBundle\Controller;

    use Sonata\AdminBundle\Controller\CRUDController as Controller;

    class JobAdminController extends Controller
    {    
    }


.. code-block:: php

    <?php
    
    //src/SfTuts/JobeetBundle/Controller/CategoryAdminController.php

    namespace SfTuts\JobeetBundle\Controller;

    use Sonata\AdminBundle\Controller\CRUDController as Controller;

    class CategoryAdminController extends Controller
    {    
    }

The Admin Class
---------------

The admin class contains all information required to generate the CRUD interface.
By convention, the are in the ``Admin folder``. Let's create them:

.. code-block:: php

    <?php

    //src/SfTuts/JobeetBundle/Admin/JobAdmin.php

    namespace SfTuts\JobeetBundle\Admin;

    use Sonata\AdminBundle\Admin\Admin;
    use Sonata\AdminBundle\Form\FormMapper;
    use Sonata\AdminBundle\Datagrid\DatagridMapper;
    use Sonata\AdminBundle\Datagrid\ListMapper;

    class JobAdmin extends Admin
    {
    }

.. code-block:: php

    <?php

    //src/SfTuts/JobeetBundle/Admin/CategoryAdmin.php

    namespace SfTuts\JobeetBundle\Admin;

    use Sonata\AdminBundle\Admin\Admin;
    use Sonata\AdminBundle\Form\FormMapper;
    use Sonata\AdminBundle\Datagrid\DatagridMapper;
    use Sonata\AdminBundle\Datagrid\ListMapper;

    class CategoryAdmin extends Admin
    {
    }

Register Admin Classes in the DIC
---------------------------------

Now, we need to register each admin class inside de Dependency Injection Container (DIC).

.. code-block:: yaml

    # app/config/config.yml
    services:
       sf_tuts.jobeet.admin.job:
          class: SfTuts\JobeetBundle\Admin\JobAdmin
          tags:
            - { name: sonata.admin, manager_type: orm, group: jobeet, label: job }
          arguments: [SfTuts\JobeetBundle\Entity\Job, SfTutsJobeetBundle:JobAdmin]
       sf_tuts.jobeet.admin.category:
          class: SfTuts\JobeetBundle\Admin\CategoryAdmin
          tags:
            - { name: sonata.admin, manager_type: orm, group: jobeet, label: category }
          arguments: [SfTuts\JobeetBundle\Entity\Category, SfTutsJobeetBundle:CategoryAdmin]

Then we make a clear cache command, so the new routes of the admin modules could be
generated: ``php app/console cache:clear``

.. figure:: ../images/12/dashboard.png
   :alt: Dashboard with the Jobeet group

At this point we can see that in the dashboard appears the Jobeet group. Inside of
it appears the Job and Category modules, with their respectives add and list links.
In the tags of the registration in DIC are defined the group Jobeet and the
label Job and Category. There are also references to the Admin and Entity classes.
