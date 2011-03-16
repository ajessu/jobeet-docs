Day 3: The Data Model
======================

Those of you itching to open your text editor and lay down some PHP
will be happy to know today will get us into some development. We
will define the Jobeet data model, use an ORM to interact with the
database, and build the first module of the application. But as
Symfony2 does a lot of the work for us, we will have a fully
functional web module without writing too much PHP code.

The Relational Model 
---------------------

The user stories we saw yesterday describe the main objects of our
project: jobs, affiliates, and categories. Here is the
corresponding entity relationship diagram:

.. figure:: ../images/03/diagram.png
   :alt: Entity relationship diagram

In addition to the columns described in the stories, we have also
added a ``createdAt`` field to some tables. We will set the value to
the current system time when a record is created. We will do the same
for ``updatedAt`` fields: Their value will be set to the system time
whenever the record is updated.

The Schema
-----------

To store the jobs, affiliates, and categories, we obviously need a
relational database.

But as Symfony2 is an Object-Oriented framework, we like to
manipulate objects whenever we can. For example,
instead of writing SQL statements to retrieve records from the
database, we'd rather prefer to use objects.

The relational database information must be mapped to an object
model. This can be done with an
`ORM tool <http://en.wikipedia.org/wiki/Object-relational_mapping>`_
and thankfully, Symfony2 comes bundled with one of them:
`Doctrine 2 <http://www.doctrine-project.org/>`_.

The ORM needs a description of the tables and their relationships
to create the related classes. There are two ways to create this
description schema: by introspecting an existing database or by
creating it by hand.

As the database does not exist yet and as we want to keep Jobeet
database agnostic, let's create our object model by creating Entities
on our project, you can create them in any of the three formats supported
natively by Symfony2:

.. tip::

     When defining your entities, you can omit the getter/setter methods and
     let Doctrine create them for you with the ``doctrine:generate:entities``
     command. This only works after you create the mapping information (see
     below).

.. configuration-block::

    .. code-block:: php

        <?php
        // src/Acme/JobeetBundle/Entity/Job.php

        namespace Acme\JobeetBundle\Entity;

        /**
         * @orm:Entity
         */
        class Job
        {
            /**
             * @orm:Id
             * @orm:Column(type="integer")
             * @orm:GeneratedValue(strategy="IDENTITY")
             */
            protected $id;
            
            /**
             * @orm:ManyToOne(targetEntity="Category")
             * @orm:JoinColumn(name="category_id", referencedColumnName="id")
             */
            protected $category;

            /**
             * @orm:Column(type="string", length="255")
             */
            protected $type;

            /**
             * @orm:Column(type="string", length="255", nullable=true)
             */
            protected $company;

            /**
             * @orm:Column(type="string", length="255")
             */
            protected $logo;

            /**
             * @orm:Column(type="string", length="255", nullable=true)
             */
            protected $url;

            /**
             * @orm:Column(type="string", length="255", nullable=true)
             */
            protected $position;

            /**
             * @orm:Column(type="string", length="255")
             */
            protected $location;

            /**
             * @orm:Column(type="string", length="4000")
             */
            protected $description;

            /**
             * @orm:Column(type="string", length="4000", name="how_to_apply")
             */
            protected $howToApply;

            /**
             * @orm:Column(type="string", length="255", unique=true)
             */
            protected $token;

            /**
             * @orm:Column(type="boolean")
             */
            protected $is_public;

            /**
             * @orm:Column(type="boolean", name="is_activated")
             */
            protected $isActivated;

            /**
             * @orm:Column(type="string", length="255")
             */
            protected $email;

            /**
             * @orm:Column(type="datetime", name="created_at")
             */
            protected $createdAt;

            /**
             * @orm:Column(type="datetime", name="expires_at")
             */
            protected $expiresAt;

            public function __construct()
            {
                $this->createdAt = new \DateTime();
                $this->updatedAt = new \DateTime();
            }
        }

    .. code-block:: yaml

        # Acme/JobeetBundle/Resources/config/doctrine/metadata/orm/Acme.JobeetBundle.Entity.Job.dcm.yml

        Acme\JobeetBundle\Entity\Job:
          type: entity
          table: null
          fields:
            id:
              type: integer
              id: true
              generator:
                strategy: IDENTITY
            type:
              type: string
              length: '255'
            company:
              type: string
              length: '255'
              nullable: true
            logo:
              type: string
              length: '255'
            url:
              type: string
              length: '255'
              nullable: true
            position:
              type: string
              length: '255'
              nullable: true
            location:
              type: string
              length: '255'
            description:
              type: string
              length: '4000'
            howToApply:
              type: string
              length: '4000'
              column: how_to_apply
            token:
              type: string
              length: '255'
              unique: true
            is_public:
              type: boolean
              length: null
            isActivated:
              type: boolean
              length: null
              column: is_activated
            email:
              type: string
              length: '255'
            createdAt:
              type: datetime
              column: created_at
            expiresAt:
              type: datetime
              column: expires_at
          oneToOne:
            category:
              targetEntity: Acme\JobeetBundle\Entity\Category
              cascade: {  }
              mappedBy: null
              inversedBy: null
              joinColumns:
                category_id:
                  referencedColumnName: id
              orphanRemoval: false
          lifecycleCallbacks: {  }


    .. code-block:: xml
        
        <!-- Acme/JobeetBundle/Resources/config/doctrine/metadata/orm/Acme.JobeetBundle.Entity.Job.dcm.xml -->

        <?xml version="1.0" encoding="utf-8"?>
        <doctrine-mapping xmlns="http://doctrine-project.org/schemas/orm/doctrine-mapping" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://doctrine-project.org/schemas/orm/doctrine-mapping http://doctrine-project.org/schemas/orm/doctrine-mapping.xsd">
          <entity name="Acme\JobeetBundle\Entity\Job">
            <change-tracking-policy>DEFERRED_IMPLICIT</change-tracking-policy>
            <id name="id" type="integer" column="id">
              <generator strategy="IDENTITY"/>
            </id>
            <field name="type" type="string" column="type" length="255"/>
            <field name="company" type="string" column="company" length="255"/>
            <field name="logo" type="string" column="logo" length="255"/>
            <field name="url" type="string" column="url" length="255"$/>
            <field name="position" type="string" column="position" length="255"/>
            <field name="location" type="string" column="location" length="255"/>
            <field name="description" type="string" column="description" length="4000"/>
            <field name="howToApply" type="string" column="how_to_apply" length="4000"/>
            <field name="token" type="string" column="token" length="255" unique="1"/>
            <field name="is_public" type="boolean" column="is_public"/>
            <field name="isActivated" type="boolean" column="is_activated"/>
            <field name="email" type="string" column="email" length="255"/>
            <field name="createdAt" type="datetime" column="created_at"/>
            <field name="expiresAt" type="datetime" column="expires_at"/>
            <many-to-one field="category" target-entity="Acme\JobeetBundle\Entity\Category" orphan-removal="">
              <join-columns>
                <join-column name="category_id" referenced-column-name="id" nullable="1"/>
              </join-columns>
            </many-to-one>
            <lifecycle-callbacks/>
          </entity>
        </doctrine-mapping>


.. configuration-block::

    .. code-block:: php

        <?php       
        // src/Acme/JobeetBundle/Entity/Category.php
        
        namespace Acme\JobeetBundle\Entity;

        /**
         * @orm:Entity
         */
        class Category
        {
            /**
             * @orm:Id
             * @orm:Column(type="integer")
             * @orm:GeneratedValue(strategy="IDENTITY")
             */
            protected $id;

            /**
             * @orm:Column(type="string", length="255", unique=true)
             */
            protected $name;
        }

    .. code-block:: yaml

        # Acme/JobeetBundle/Resources/config/doctrine/metadata/orm/Acme.JobeetBundle.Entity.Category.dcm.yml

        Acme\JobeetBundle\Entity\Category:
          type: entity
          table: null
          fields:
            id:
              type: integer
              id: true
              generator:
                strategy: IDENTITY
            name:
              type: string
              length: '255'
              unique: true
            createdAt:
              type: datetime
              column: created_at
            expiresAt:
              type: datetime
              column: expires_at
          lifecycleCallbacks: {  }


    .. code-block:: xml

        <!-- Acme/JobeetBundle/Resources/config/doctrine/metadata/orm/Acme.JobeetBundle.Entity.Job.dcm.xml -->

        <?xml version="1.0" encoding="utf-8"?>
        <doctrine-mapping xmlns="http://doctrine-project.org/schemas/orm/doctrine-mapping" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://doctrine-project.org/schemas/orm/doctrine-mapping http://doctrine-project.org/schemas/orm/doctrine-mapping.xsd">
          <entity name="Acme\JobeetBundle\Entity\Category">
            <change-tracking-policy>DEFERRED_IMPLICIT</change-tracking-policy>
            <id name="id" type="integer" column="id">
              <generator strategy="IDENTITY"/>
            </id>
            <field name="name" type="string" column="name" length="255" unique="1"/>
            <field name="createdAt" type="datetime" column="created_at"/>
            <field name="expiresAt" type="datetime" column="expires_at"/>
            <lifecycle-callbacks/>
          </entity>
        </doctrine-mapping>

.. note::

    When using annotations in your Symfony2 project you have to namespace all
    Doctrine ORM annotations with the ``orm:`` prefix.

.. tip::

    If you use YAML or XML to describe your entities, you can omit the creation
    of the Entity class, and let the ``doctrine:generate:entities`` command do
    it for you. To create the entities for Job and Category as describe above you would
    run the following command ``php app/console doctrine:generate:entities AcmeJobeetBundle``.
    This will create 2 classes in the ``src/Acme/JobeetBundle/Entity`` folder, one for
    Job and one for Category

The ORM
--------

We need to setup the mapping configution for our bundle. This is done in the
``app/config/config.yml`` file by adding our bundle under the doctrine section.

.. code-block:: yaml

    doctrine:
        dbal:
            ...

    orm:
        auto_generate_proxy_classes: %kernel.debug%
        mappings:
            AcmeDemoBundle: ~
            AcmeJobeetBundle: ~
            
Create the database and the schema related to your metadata information with
the following commands:

.. code-block:: bash

    $ php app/console doctrine:database:create
    $ php app/console doctrine:schema:create

UP TO HERE
----------

The column values of a record can be manipulated with a model
object by using some accessors (``get*()``
methods) and mutators (``set*()`` methods):

::

    [php]
    $job = new JobeetJob();
    $job->setPosition('Web developer');
    $job->save();
    
    echo $job->getPosition();
    
    $job->delete();

You can also define foreign keys directly by linking
objects together:

::

    [php]
    $category = new JobeetCategory();
    $category->setName('Programming');
    
    $job = new JobeetJob();
    $job->setCategory($category);

The ``propel:build --all`` task is a shortcut for the tasks we have
run in this section and some more. So, run this task now to
generate forms and validators for the Jobeet model classes:

::

    $ php Symfony2 propel:build --all --no-confirmation

You will see validators in action today and forms will be explained
in great details on day 10.

The Initial Data
----------------

The tables have been created in the database but there is no data
in them. For any web application, there are three types of data:


-  **Initial data**: Initial data are needed for the application to
   work. For example, Jobeet needs some initial categories. If not,
   nobody will be able to submit a job. We also need an admin user to
   be able to login to the backend.

-  **Test data**: Test Data are needed for the application to be
   tested. As a developer, you will write tests to ensure that Jobeet
   behaves as described in the user stories, and the best way is to
   write automated tests. So, each time you run your tests, you need a
   clean database with some fresh data to test on.

-  **User data**: User data are created by the users during the
   normal life of the application.


Each time Symfony2 creates the tables in the database, all the data
are lost. To populate the database with some initial data, we could
create a PHP script, or execute some SQL statements with the
``mysql`` program. But as the need is quite common, there is a
better way with Symfony2: create YAML files in the
``data/fixtures/`` directory and use the ``propel:data-load`` task
to load them into the database.

First, create the following fixture files:

[yml] # data/fixtures/010\_categories.yml JobeetCategory: design: {
name: Design } programming: { name: Programming } manager: { name:
Manager } administrator: { name: Administrator }

::

    # data/fixtures/020_jobs.yml
    JobeetJob:
      job_sensio_labs:
        category_id:  programming
        type:         full-time
        company:      Sensio Labs
        logo:         sensio-labs.gif
        url:          http://www.sensiolabs.com/
        position:     Web Developer
        location:     Paris, France
        description:  |
          You've already developed websites with Symfony2 and you want to
          work with Open-Source technologies. You have a minimum of 3
          years experience in web development with PHP or Java and you
          wish to participate to development of Web 2.0 sites using the
          best frameworks available.
        how_to_apply: |
          Send your resume to fabien.potencier [at] sensio.com
        is_public:    true
        is_activated: true
        token:        job_sensio_labs
        email:        job@example.com
        expires_at:   2010-10-10
    
      job_extreme_sensio:
        category_id:  design
        type:         part-time
        company:      Extreme Sensio
        logo:         extreme-sensio.gif
        url:          http://www.extreme-sensio.com/
        position:     Web Designer
        location:     Paris, France
        description:  |
          Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do
          eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut
          enim ad minim veniam, quis nostrud exercitation ullamco laboris
          nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor
          in reprehenderit in.
    
          Voluptate velit esse cillum dolore eu fugiat nulla pariatur.
          Excepteur sint occaecat cupidatat non proident, sunt in culpa
          qui officia deserunt mollit anim id est laborum.
        how_to_apply: |
          Send your resume to fabien.potencier [at] sensio.com
        is_public:    true
        is_activated: true
        token:        job_extreme_sensio
        email:        job@example.com
        expires_at:   2010-10-10

[yml] # data/fixtures/categories.yml JobeetCategory: design: name:
Design programming: name: Programming manager: name: Manager
administrator: name: Administrator

::

    # data/fixtures/jobs.yml
    JobeetJob:
      job_sensio_labs:
        JobeetCategory: programming
        type:         full-time
        company:      Sensio Labs
        logo:         sensio-labs.gif
        url:          http://www.sensiolabs.com/
        position:     Web Developer
        location:     Paris, France
        description:  |
          You've already developed websites with Symfony2 and you want to work
          with Open-Source technologies. You have a minimum of 3 years
          experience in web development with PHP or Java and you wish to
          participate to development of Web 2.0 sites using the best
          frameworks available.
        how_to_apply: |
          Send your resume to fabien.potencier [at] sensio.com
        is_public:    true
        is_activated: true
        token:        job_sensio_labs
        email:        job@example.com
        expires_at:   '2010-10-10'
    
      job_extreme_sensio:
        JobeetCategory:  design
        type:         part-time
        company:      Extreme Sensio
        logo:         extreme-sensio.gif
        url:          http://www.extreme-sensio.com/
        position:     Web Designer
        location:     Paris, France
        description:  |
          Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do
          eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut
          enim ad minim veniam, quis nostrud exercitation ullamco laboris
          nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor
          in reprehenderit in.
    
          Voluptate velit esse cillum dolore eu fugiat nulla pariatur.
          Excepteur sint occaecat cupidatat non proident, sunt in culpa
          qui officia deserunt mollit anim id est laborum.
        how_to_apply: |
          Send your resume to fabien.potencier [at] sensio.com
        is_public:    true
        is_activated: true
        token:        job_extreme_sensio
        email:        job@example.com
        expires_at:   '2010-10-10'

    **NOTE** The job fixture file references two images. You can
    download them
    (``http://www.Symfony2-project.org/get/jobeet/sensio-labs.gif``,
    ``http://www.Symfony2-project.org/get/jobeet/extreme-sensio.gif``)
    and put them under the ``web/uploads/jobs/`` directory.


A fixtures file is written in YAML, and defines model objects,
labelled with a unique name (for instance, we have defined two jobs
labelled ``job_sensio_labs`` and ``job_extreme_sensio``). This
label is of great use to link related objects without having to
define primary keys (which are often
auto-incremented and cannot be set). For instance, the
``job_sensio_labs`` job category is ``programming``, which is the
label given to the 'Programming' category.

    **TIP** In a YAML file, when a string contains line breaks (like
    the ``description`` column in the job fixture file), you can use
    the pipe (``|``) to indicate that the string will span several
    lines.


Although a fixture file can contain objects from one or several
models, we have decided to create one file per model for the Jobeet
fixtures.

>**TIP** >Notice the numbers prefixing the
filenames. This is a simple way >to control the order of data
loading. Later in the project, if we need to >insert some new
fixture file, it will be easy as we have some free numbers >between
existing ones. >**NOTE** >Propel requires that the fixtures files
be prefixed with numbers to determine >the order in which the files
will be loaded. With Doctrine this is not required >as all fixtures
will be loaded and saved in the correct order to make sure >foreign
keys are set properly.

In a fixture file, you don't need to define all columns values. If
not, Symfony2 will use the default value defined in the database
schema. And as Symfony2 uses ##ORM## to load the data into the
database, all the built-in behaviors (like
automatically setting the ``created_at`` or ``updated_at`` columns)
and the custom behaviors you might have added to the model classes
are activated.

Loading the initial data into the database is as simple as running
the ``propel:data-load`` task:

::

    $ php Symfony2 propel:data-load

    **TIP** The ``propel:build --all --and-load`` task is a shortcut
    for the ``propel:build --all`` task followed by the
    ``propel:data-load`` task.


Run the ``doctrine:build --all --and-load`` task to make sure
everything is generated from your schema. This will generate your
forms, filters, models, drop your database and re-create it with
all the tables.

::

    $ php Symfony2 doctrine:build --all --and-load

See it in Action in the Browser
-------------------------------

We have used the command line interface a lot but that's not really
exciting, especially for a web project. We now have everything we
need to create Web pages that interact with the database.

Let's see how to display the list of jobs, how to edit an existing
job, and how to delete a job. As explained during the first day, a
Symfony2 project is made of applications. Each
application is further divided into
**modules**. A module is a self-contained set of
PHP code that represents a feature of the application (the API
module for example), or a set of manipulations the user can do on a
model object (a job module for example).

Symfony2 is able to automatically generate a module for a given
model that provides basic manipulation features:

::

    $ php Symfony2 propel:generate-module --with-show
      ➥ --non-verbose-templates frontend job JobeetJob

The ``propel:generate-module`` generates a ``job`` module in the
``frontend`` application for the ``JobeetJob`` model. As with most
Symfony2 tasks, some files and directories have been created for you
under the ``apps/frontend/modules/job/`` directory:

\| Directory \| Description \| ------------ \| --------------------
\| ``actions/`` \| The module actions \| ``templates/`` \| The
module templates

The ``actions/actions.class.php`` file defines all the available
**action** for the ``job`` module:

\| Action name \| Description \| -------------- \|
------------------------------------------------------- \|
``index`` \| Displays the records of the table \| ``show`` \|
Displays the fields and their values for a given record \| ``new``
\| Displays a form to create a new record \| ``create`` \| Creates
a new record \| ``edit`` \| Displays a form to edit an existing
record \| ``update`` \| Updates a record according to the user
submitted values \| ``delete`` \| Deletes a given record from the
table

You can now test the job module in a browser:

::

     http://www.jobeet.com.localhost/frontend_dev.php/job

.. figure:: http://www.Symfony2-project.org/images/jobeet/1_4/03/job.png
   :alt: Job module
   
   Job module

If you try to edit a job, you will have an exception because
Symfony2 needs a text representation of a category. A PHP object
representation can be defined with the PHP ``__toString()`` magic
method. The text representation of a category record should be
defined in the ``JobeetCategory`` model class:

::

    [php]
    // lib/model/JobeetCategory.php
    class JobeetCategory extends BaseJobeetCategory
    {
      public function __toString()
      {
        return $this->getName();
      }
    }

Now each time Symfony2 needs a text representation of a category, it
calls the ``__toString()`` method which returns the
category name. As we will need a text representation of all model
classes at one point or another, let's define a ``__toString()``
method for every model class: If you try to edit a job, you will
notice the Category id drop down has a list of all the category
names. The value of each option is gotten from the ``__toString()``
method.

Doctrine will try and provide a base ``__toString()``
method by guessing a descriptive column name like, ``title``,
``name``, ``subject``, etc. If you want something custom then you
will need to add your own ``__toString()`` methods like below. The
``JobeetCategory`` model is able to guess the ``__toString()``
method by using the ``name`` column of the ``jobeet_category``
table.

::

    [php]

// lib/model/JobeetJob.php //
lib/model/doctrine/JobeetJob.class.php class JobeetJob extends
BaseJobeetJob { public function \_\_toString() { return sprintf('%s
at %s (%s)', $this->getPosition(), ➥ $this->getCompany(),
$this->getLocation()); } }

// lib/model/JobeetAffiliate.php //
lib/model/doctrine/JobeetAffiliate.class.php class JobeetAffiliate
extends BaseJobeetAffiliate { public function \_\_toString() {
return $this->getUrl(); } }

You can now create and edit jobs. Try to leave a required field
blank, or try to enter an invalid date. That's right, Symfony2 has
created basic validation rules by introspecting the database
schema.

.. figure:: http://www.Symfony2-project.org/images/jobeet/1_4/03/validation.png
   :alt: validation
   
   validation

Final Thoughts
--------------

That's all. I have warned you in the introduction. Today, we have
barely written PHP code but we have a working web module for the
job model, ready to be tweaked and customized. Remember, no PHP
code also means no bugs!

If you still have some energy left, feel free to read the generated
code for the module and the model and try to understand how it
works. If not, don't worry and sleep well, as tomorrow we will talk
about one of the most used paradigm in web frameworks, the
`MVC design pattern <http://en.wikipedia.org/wiki/Model-view-controller>`_.