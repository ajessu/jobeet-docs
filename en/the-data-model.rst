Day 3: The Data Model
======================

Those of you itching to open your text editor and lay down some PHP
will be happy to know today will get us into some development. We
will define the Jobeet data model and use an ORM to interact with the
database.

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
             * @orm:Column(type="boolean", , name="is_public")
             */
            protected $isPublic;

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
            isPublic:
              type: boolean
              length: null
              column: is_public
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
            <field name="url" type="string" column="url" length="255"/>
            <field name="position" type="string" column="position" length="255"/>
            <field name="location" type="string" column="location" length="255"/>
            <field name="description" type="string" column="description" length="4000"/>
            <field name="howToApply" type="string" column="how_to_apply" length="4000"/>
            <field name="token" type="string" column="token" length="255" unique="1"/>
            <field name="isPublic" type="boolean" column="is_public"/>
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
          lifecycleCallbacks: {  }


    .. code-block:: xml

        <!-- Acme/JobeetBundle/Resources/config/doctrine/metadata/orm/Acme.JobeetBundle.Entity.Category.dcm.xml -->

        <?xml version="1.0" encoding="utf-8"?>
        <doctrine-mapping xmlns="http://doctrine-project.org/schemas/orm/doctrine-mapping" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://doctrine-project.org/schemas/orm/doctrine-mapping http://doctrine-project.org/schemas/orm/doctrine-mapping.xsd">
          <entity name="Acme\JobeetBundle\Entity\Category">
            <change-tracking-policy>DEFERRED_IMPLICIT</change-tracking-policy>
            <id name="id" type="integer" column="id">
              <generator strategy="IDENTITY"/>
            </id>
            <field name="name" type="string" column="name" length="255" unique="1"/>
            <lifecycle-callbacks/>
          </entity>
        </doctrine-mapping>

.. note::

    When using annotations in your Symfony2 project you have to namespace all
    Doctrine ORM annotations with the ``orm:`` prefix.    

The ORM
--------

We need to setup the mapping configution for our bundle. This is done in the
``app/config/config.yml`` file by adding our bundle under the doctrine section.

.. code-block:: yaml

    doctrine:
        dbal:
            # ...

        orm:
            auto_generate_proxy_classes: %kernel.debug%
            default_entity_manager: default
            entity_managers:
                default:
                    mappings:
                        AcmeJobeetBundle: ~   
                           
Create the database and the schema related to your metadata information with
the following commands:

.. code-block:: bash

    $ php app/console doctrine:database:create
    $ php app/console doctrine:schema:create

Getters and Setters
~~~~~~~~~~~~~~~~~~~

You can create your getters and setters manually for your Entity classes,
or let doctrine do that for you.

If you create your Entity class, as shown above, you can run
``php app/console doctrine:generate:entities`` and Symfony will create the
method stubs (your getters/setters), for each of your Entity classes.

.. tip::

    If you use YAML or XML to describe your entities, you can omit the creation
    of the Entity class, and let the ``doctrine:generate:entities AcmeJobeetBundle``
    command do it for you. This will create 2 classes in the 
    ``src/Acme/JobeetBundle/Entity`` folder, one for Job and one for Category.
    
And now you can use those getters and setters to manipulate your
object

.. code-block:: php

    <?php

    // Acme/JobeetBundle/Controller/JobController.php

    namespace Acme\JobeetBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller,
        Acme\JobeetBundle\Entity\Job;

    class JobController extends Controller
    {
        public function createAction()
        {
            $job = new Job();
            $job->setPosition('Web developer');
            // .. set other fields

            // get the entity manager
            $em = $this->get('doctrine.orm.entity_manager');

            // persist the object to database
            $em->persist($job);
            $em->flush();

            // ...
        }

        public function editAction($id)
        {
            $em = $this->get('doctrine.orm.entity_manager');

            $job = $em->find('AcmeJobeetBundle:Job', $id);
            $job->setPosition('Web designer');

            $em->persist($job);
            $em->flush();

            // ...
        }

        public function deleteAction($id)
        {
            $em = $this->get('doctrine.orm.entity_manager');

            $job = $em->find('AcmeJobeetBundle:Job', $id);

            $em->remove($job);
            $em->flush();

            // ...
        }
    }

You can also define foreign keys directly by linking
objects together:

.. code-block:: php

    <?php
    
    // Acme/JobeetBundle/Controller/JobController.php

    namespace Acme\JobeetBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller,
        Acme\JobeetBundle\Entity\Job,
        Acme\JobeetBundle\Entity\Category;

    class JobController extends Controller
    {
        public function createAction()
        {

            $em = $this->get('doctrine.orm.entity_manager');

            $category = new Category();
            $category->setName('Programming');   
            $em->persist($category);

            $job = new Job();

            $job->setCategory($category); 
            $em->persist($job);

            $em->flush();

            // ...
        }
    }

The Initial Data
----------------

The tables have been created in the database but there is no data
in them. For any web application, there are three types of data:


-  **Initial data**: Initial data is needed for the application to
   work. For example, Jobeet needs some initial categories. If not,
   nobody will be able to submit a job. We also need an admin user to
   be able to login to the backend.

-  **Test data**: Test Data is needed for the application to be
   tested. As a developer, you will write tests to ensure that Jobeet
   behaves as described in the user stories, and the best way is to
   write automated tests. So, each time you run your tests, you need a
   clean database with some fresh data to test on.

-  **User data**: User data are created by the users during the
   normal life of the application.


Each time Symfony2 creates the tables in the database, all the data
is lost. To populate the database with some initial data, we could
create a PHP script, or execute some SQL statements with the
``mysql`` program. But as the need is quite common, there is a
better way with Symfony2: create fixture classes in the
``DataFixtures/ORM/`` directory and use the ``doctrine:data:load`` task
to load them into the database.

First, create the following fixture class:

.. code-block:: php

    <?php

    // src/Acme/JobeetBundle/DataFixtures/ORM/JobFixtures.php

    namespace Acme\JobeetBundle\DataFixtures\ORM;

    use Doctrine\Common\DataFixtures\FixtureInterface,
        Acme\JobeetBundleJobeetBundle\Entity\Job,
        Acme\JobeetBundleJobeetBundle\Entity\Category; 

    class JobFixtures implements FixtureInterface
    {
        public function load($em)
        {
            $design = new Category();
            $design->setName('Design');

            $programming = new Category();
            $programming->setName('Programming');

            $manager = new Category();
            $manager->setName('Manager');

            $administrator = new Category();
            $administrator->setName('Administrator');

            $em->persist($design);
            $em->persist($programming);
            $em->persist($manager);
            $em->persist($administrator);

            $sensio = new Job();
            $sensio->setCategory($programming);
            $sensio->setType('full-time');
            $sensio->setCompany('Sensio Labs');
            $sensio->setLogo('sensio-labs.gif');
            $sensio->setUrl('http://www.sensiolabs.com/');
            $sensio->setPosition('Web Developer');
            $sensio->setLocation('Paris, France');
            $sensio->setDescription("You've already developed websites with symfony and you want to work with Open-Source technologies. You have a minimum of 3 years experience in web development with PHP or Java and you wish to participate to development of Web 2.0 sites using the best frameworks available.");
            $sensio->setHowToApply('Send your resume to fabien.potencier [at] sensio.com');
            $sensio->setIsPublic(true);
            $sensio->setIsActivated(true);
            $sensio->setToken('job_sensio_labs');
            $sensio->setEmail('job@example.com');
            $sensio->setExpiresAt(new \DateTime('2012-10-10'));

            $extreme = new Job();
            $extreme->setCategory($design);
            $extreme->setType('part-time');
            $extreme->setCompany('Extreme Sensio');
            $extreme->setLogo('extreme-sensio.gif');
            $extreme->setUrl('http://www.extreme-sensio.com/');
            $extreme->setPosition('Web Designer');
            $extreme->setLocation('Paris, France');
            $extreme->setDescription("Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in.

            Voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.");
            $extreme->setHowToApply('Send your resume to fabien.potencier [at] sensio.com');
            $extreme->setIsPublic(true);
            $extreme->setIsActivated(true);
            $extreme->setToken('job_extreme_sensio');
            $extreme->setEmail('job@example.com');
            $extreme->setExpiresAt(new \DateTime('2011-10-10'));

            $em->persist($sensio);
            $em->persist($extreme);

            $em->flush();
        }
    }


.. note::

    The job fixture file references two images. You can
    download them
    (``http://www.symfony-project.org/get/jobeet/sensio-labs.gif``,
    ``http://www.symfony-project.org/get/jobeet/extreme-sensio.gif``)
    and put them under the ``web/images/`` directory.

Loading the initial data into the database is as simple as running
the ``doctrine:data:load`` task:

.. code-block:: bash

    $ php app/console doctrine:data:load


Final Thoughts
--------------

That's all. I have warned you in the introduction.

Tomorrow we will talk about one of the most used paradigm in web
frameworks, the 
`MVC design pattern <http://en.wikipedia.org/wiki/Model-view-controller>`_.