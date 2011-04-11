WORK IN PROGRESS
================	 	
Fork it on `Github <http://github.com/symfonytuts/jobeet-docs>`_

Day 12: The Admin Bundle
========================

With the addition we made in day 11 on Jobeet, the frontend
application is now fully useable by job seekers and job posters.
It's time to talk a bit about the backend application. Today,
thanks to the AdminBundle, we will develop a complete backend interface for Jobeet in
just one hour.

Many sections of this chapter were taken and modified from the original docs of the
Admin Bundle. You can view their full documentation on their  `github repo <https://github.com/sonata/AdminBundle>`_.

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

Creating the Admin Class
------------------------

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
          arguments: [null, SfTuts\JobeetBundle\Entity\Job, SfTutsJobeetBundle:JobAdmin]
       sf_tuts.jobeet.admin.category:
          class: SfTuts\JobeetBundle\Admin\CategoryAdmin
          tags:
            - { name: sonata.admin, manager_type: orm, group: jobeet, label: category }
          arguments: [null, SfTuts\JobeetBundle\Entity\Category, SfTutsJobeetBundle:CategoryAdmin]

Then we make a clear cache command, so the new routes of the admin modules could be
generated: ``php app/console cache:clear``

.. figure:: ../images/12/dashboard.png
   :alt: Dashboard with the Jobeet group

At this point we can see that in the dashboard appears the Jobeet group. Inside of
it appears the Job and Category modules, with their respectives add and list links.
In the tags of the registration in DIC are defined the group Jobeet and the
label Job and Category. There are also references to the Admin and Entity classes.

Basic configuration of Admin Classes
------------------------------------

At the moment, if we follow any link nothing will appear. That's because we haven't
configure the fields that belong to the list and the form.
Let's do a basic configuration first:

.. code-block:: php

    //src/SfTuts/JobeetBundle/Admin/JobAdmin.php
    //...

    class JobAdmin extends Admin
    {
        protected $list = array(
            'company' => array('identifier' => true),
            'position',
            'location',
            'url',
            'isActivated',
            'email',
            'category',
            'expiresAt',
        );

        protected $maxPerPage = 5;

        protected $form = array(
            'category',
            'type',
            'company',
            'logo',
            'url',
            'position',
            'location',
            'description',
            'howToApply',
            'isPublic',
            'email',
            'isActivated',
        );

        protected $filter = array(
               'category',
               'company',
               'position',
               'description',
               'isActivated',
               'isPublic',
               'email',
    //           'expiresAt',   #Bundle still without date filters
        );
    }

.. code-block:: php

    //src/SfTuts/JobeetBundle/Admin/CategoryAdmin.php
    //...

    class CategoryAdmin extends Admin
    {
        protected $list = array(
            'id' => array('identifier' => true),
            'name',
        );
        protected $form = array(
            'name',
        );
        protected $filter = array(
            'name',
        );
    }

We also should create a __toString function for Category, because it appears in the list
of the JobAdmin class.

.. code-block:: php

    //src/SfTuts/JobeetBundle/Entity/Category.php
    //...

        /**
         * To String
         *
         * @return string
         */
        public function __toString()
        {
            return $this->name;
        }

With this, we got the admin modules with CRUD operations over Job and Category. This
modules have very cool features. Let's see the Job module:

.. figure:: ../images/12/job_list_basic.png
   :alt: Job List with basic functionality

If you see, Breadcrumb, pagination, batch actions and filter comes out of the box 
without too much configuration. At this point the protected properties ``list``, 
``form``, and ``filter`` were defined as arrays with the names of the fields we
want to see. When we define ``identifier`` as true, that means that in that field
will be a link for editing the entity. Also category field, that has a ManyToOne
relation to Job, has a link for editing it. Lets edit the first job:

.. figure:: ../images/12/job_edit_basic.png
   :alt: Editing a Job

Here we see the form for editing the Job, with all the fields that we defined in
the property ``form``. For saving the changes we can choose between two options: 
``update and edit again`` or ``update and return to the list``.

If you notice, at right hand of the category field there is ``add image``, this
is a great feature of the AdminBundle. Normally if the category of the Job was 
missing, you should go to the Category module, add it and the return and create 
the Job. Know, you simply click on the ``add`` it appears a dialog box to add it
 on the fly, without leaving the Job module. This is really great!

.. figure:: ../images/12/add_category_in_job.png
   :alt: Editing a Category inside the Job Module

List Configuration
------------------

List Actions
~~~~~~~~~~~~

It's very simple to add action for each row in the list:

.. code-block:: php

    //src/SfTuts/JobeetBundle/Admin/JobAdmin.php
    //...

        protected $list = array(
            //...
            '_action' => array(
                'actions' => array(
                    'delete' => array(),
                    'edit' => array(),
                )
            ),
        );

Field Description and Custom Fields
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For example, if you want show a shorten url, without the ``http://`` and the ``/``,
you simply add a getter for this:


.. code-block:: php

    //src/SfTuts/JobeetBundle/Entity/Job.php
    //...

        /**
         * Get slimUrl
         *
         * @return string
         */
        public function getSlimUrl()
        {
            return str_replace(array('http://', '/'), '', $this->url);
        }

And replace the ``url`` field for ``slimUrl`` in the list of JobAdmin:

.. code-block:: php

    //src/SfTuts/JobeetBundle/Admin/JobAdmin.php
    //...

        protected $list = array(
            //...
            'slimUrl'  => array('type' => "string", 'name' => 'url'),
            //...
        );

The ``name`` is the option for setting the label of the field. 
The ``type`` is the most important option for each field. In the other fields 
``type`` is not defined, because the ``Admin`` class use the type defined in the 
doctrine mapping definition. For ``slimUrl`` it's obligatory, because it not
exist in that mapping. You can set other options, simply look inside the
``FieldDescription`` class in the AdminBundle. This class is common to list, the
form and the filter.

At this point, the ``Job List`` should look like:


.. figure:: ../images/12/job_list_advanced.png
   :alt: Advanced List in the Job Module

Form Configuration
------------------

Field Description
~~~~~~~~~~~~~~~~~

If we want that ``expiresAt`` looks like a date, and not date time, we change
it on the form property:

.. code-block:: php

    //src/SfTuts/JobeetBundle/Admin/JobAdmin.php
    //...

        protected $form = array(
            'expiresAt' => array('type' => 'date'),
        //...

Like in the list, the ``Admin`` class use the type defined in the doctrine 
mapping definition if it is not defined in the form property. 


Field Description: Edit Option
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If the categories are too many, maybe it would be better to have some kind of
filter. This is pretty easy to configure!

.. code-block:: php

    //src/SfTuts/JobeetBundle/Admin/JobAdmin.php
    //...

        protected $form = array(
            'category' => array('edit' => 'list'),
        //...

With this you will get a list of the categories with a filter:

.. figure:: ../images/12/category_list_filter_in_job.png
   :alt: List for filtering Categories in the Job Form

The edit option could be ``list``, ``standard`` and ``inline``. When it's 
ManyToOne, by default the Admin class will set ``standard``, which render the 
choice that we saw in the basic configuration. ``Inline`` is for embedding form.

Overriding the configureFormFields Method
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If we want to change the field in a form, we can override the ``configureFormFields``
method. For setting a ``UrlField`` to the url:

.. code-block:: php

    //src/SfTuts/JobeetBundle/Admin/JobAdmin.php
    //...

        public function configureFormFields(FormMapper $form)
        {
             $form->add(new \Symfony\Component\Form\UrlField('url'));
        }

With this we will got validation for including ``http://`` in the form field.

