Day 23: Another Look at symfony
===============================

Today is the last stop of our trip to the wonderful world of
symfony. During these twenty-three last days, you learned symfony
by example: from the design patterns used by the framework, to the
powerful built-in features. You are not a symfony master yet, but
you have all the needed knowledge to start building your symfony
applications with confidence.

As we wrap up the Jobeet tutorial, let's have another look at the
framework. Forget Jobeet for an hour, and recall all the features
you learned during this whole book.

What is symfony?
----------------

The symfony \ :sub:`framework\|Framework`\  is a set of
`cohesive but decoupled sub-frameworks <#chapter_11_sidebar_using_the_form_framework_without_symfony>`_,
that forms a full-stack
`MVC framework <#chapter_04_the_mvc_architecture>`_ (Model, View,
Controller).

Before coding head first, take some time to read the
`symfony history and philosophy <#chapter_01_introduction>`_. Then,
check the framework `prerequisites <#chapter_01_prerequisites>`_
and use the
```check_configuration.php`` script <#chapter_01_symfony_installation>`_
to validate your configuration.

Eventually, `install symfony <#chapter_01_symfony_installation>`_.
After some time you will also want to
`upgrade <#chapter_22_sub_upgrading_symfony>`_ to the latest
version of the framework.

The framework also provides tools to ease
`deployment <#chapter_22_deploying>`_.

The \ :sub:`Model`\ 
--------------------

The Model part of symfony can be done with the help of the
`Propel ORM <http://www.propelorm.org/>`_. Based on the
`Doctrine ORM <http://www.doctrine-project.org/>`_. Based on the
`database description <#chapter_03_the_schema>`_, it generates
classes for `objects <#chapter_03_the_orm>`_,
`forms <#chapter_10_##ORM_LOWER##_forms>`_, and
`filters <#chapter_12_filters_configuration>`_. ##ORM## also
`generates the SQL <#chapter_03_the_orm>`_ statements used to
create the tables in the database.

The database configuration can be done with a
`task <#chapter_03_the_database>`_ or by editing a
`configuration file <#chapter_03_the_database>`_. Beside its
configuration, it is also possible to inject initial data, thanks
to `fixture files <#chapter_03_the_initial_data>`_. You can even
make these files `dynamic <#chapter_06_dynamic_fixtures>`_.

Propel objects can also be easily
`internationalized <#chapter_19_sub_propel_objects>`_. Doctrine
objects can also be easily
`internationalized <#chapter_19_sub_doctrine_objects>`_.

The \ :sub:`View`\ 
-------------------

By default, the View layer of the MVC architecture uses plain PHP
files as templates.

Templates can use
`helpers <#chapter_04_the_stylesheets_images_and_javascripts>`_ for
recurrent tasks like creating an
`URL <#chapter_05_routing_in_actions_and_templates>`_ or a
`link <#chapter_05_routing_in_actions_and_templates>`_.

A template can be decorated by a `layout <#chapter_04_the_layout>`_
to abstract the header and footer of pages. To make views even more
reusable, you can define `slots <#chapter_04_slot_s>`_,
`partials <#chapter_07_partials_partial_templates>`_, and
`components <#chapter_19_language_switching>`_.

To speed up things, you can use the
`cache sub-framework <#chapter_21_cache_configuration>`_ to cache a
`whole page <#chapter_21_page_cache>`_, just the
`action <#chapter_21_action_cache>`_, or even just
`partials or components <#chapter_21_partial_partial_templates_and_component_cache>`_.
You can also
`remove the cache <#chapter_21_removing_the_cache_cache_removal>`_
manually.

The \ :sub:`Controller`\ 
-------------------------

The Controller part is managed by
`front controllers <#chapter_01_the_environments>`_ and
`actions <#chapter_03_see_it_in_action_in_the_browser>`_.

Tasks can be used to create
`simple modules <#chapter_07_job_category_module_creation>`_,
`CRUD modules <#chapter_03_see_it_in_action_in_the_browser>`_, or
even to generate fullly working
`admin modules <#chapter_12_backend_modules>`_ for model classes.

Admin modules allows you to built a fully functional application
without coding anything.

To abstract the technical implementation of a website, symfony uses
a `routing <#chapter_05_routing_configuration>`_ sub-framework that
generates `pretty URLs <#chapter_05_url_s>`_. To make implementing
web services even easier, symfony supports
`formats <#chapter_14_formats>`_ out of the box. You can also
create your own
`formats <#chapter_15_sub_the_yaml_format_formats_creation>`_.

An action can be
`forwarded <#chapter_04_sidebar_the_forward_action_forwarding_methods_family>`_
to another one, or
`redirected <#chapter_05_sidebar_the_redirect_redirection_methods_family>`_.

\ :sub:`Configuration`\ 
------------------------

The symfony framework makes it easy to have different configuration
\ :sub:`settings\|Settings`\  for different
\ :sub:`environments\|Environments`\ . An
`environment <#chapter_01_the_environments>`_ is a set of settings
that allows different behaviors on the development or production
servers. You can also create new
`environments <#chapter_21_creating_a_new_environment>`_.

The symfony configuration files can be defined at
`different levels <#chapter_04_sidebar_configuration_principles_in_symfony>`_
and most of them are
`environment aware <#chapter_08_sidebar_configuration_principles_in_symfony>`_:


-  ```app.yml`` <#chapter_06_custom_configuration>`_
-  ```cache.yml`` <#chapter_21_cache_configuration>`_
-  ```databases.yml`` <#chapter_03_the_database>`_
-  ```factories.yml`` <#chapter_16_sub_factories>`_
-  ```generator.yml`` <#chapter_12_the_symfony_cache>`_
-  ```routing.yml`` <#chapter_05_routing_configuration>`_
-  ```schema.yml`` <#chapter_03_the_schema>`_
-  ```security.yml`` <#chapter_13_sub_authentication>`_
-  ```settings.yml`` <#chapter_11_sub_built_in_security_features>`_
-  ```view.yml`` <#chapter_04_the_stylesheets_images_and_javascripts>`_

The configuration files mostly use the
`YAML format <#chapter_03_sidebar_the_yaml_format>`_.

Instead of using the default directory structure and organize your
application files by layers, you can also organize them by feature,
and bundle them in
`a plugin <#chapter_20_sub_a_different_way_to_organize_code_code_organization>`_.
Speaking of the default directory structure, you can also
`customize it <#chapter_22_customizing_the_directory_structure>`_
according to your needs.

\ :sub:`Debugging\|Debug`\ 
---------------------------

From `logging <#chapter_06_debugging_propel_generated_sql>`_ to the
`web debug toolbar <#chapter_06_debugging_propel_generated_sql>`_,
and From `logging <#chapter_06_debugging_doctrine_generated_sql>`_
to the
`web debug toolbar <#chapter_06_debugging_doctrine_generated_sql>`_,
and `meaningful exceptions <#chapter_01_the_environments>`_,
symfony provides a lot of useful tools to help the developer debug
problems faster.

Main symfony Objects
--------------------

The symfony framework provides quite a few core objects that
abstract recurrent needs in web projects: the
`request <#chapter_04_sub_the_request>`_, the
`response <#chapter_04_sub_the_response>`_, the
`user <#chapter_13_sub_the_myuser_class>`_, the
`logging <#chapter_22_sub_logging>`_, the
`routing <#chapter_05_routing_configuration>`_, the
`mailer <#chapter_16>`_, and the
`view cache manager <#chapter_16_sidebar_the_code_sfcontext_code_class>`_.

These core objects are managed by the
```sfContext`` object <#chapter_16_sidebar_the_code_sfcontext_code_class>`_,
and they are configured via the
`factories <#chapter_16_sub_factories>`_.

The user manages user
`authentication <#chapter_13_sub_authentication>`_,
`authorization <#chapter_13_sub_authorization>`_,
`flashes <#chapter_13_user_flashes>`_, and
`attributes <#chapter_13_user_attributes>`_ to be serialized in the
session.

\ :sub:`Security`\ 
-------------------

The symfony framework has built-in protections against
`XSS <#chapter_01_sub_application_creation>`_ and
`CSRF <#chapter_01_sub_application_creation>`_. These settings can
be configured from the
`command line <#chapter_01_sub_application_creation>`_, or by
editing a
`configuration file <#chapter_11_sub_xss_and_csrf_protection>`_.

The form framework also provides
`built-in security features <#chapter_11_sub_built_in_security_features>`_.

\ :sub:`Forms`\ 
----------------

As managing forms is one of the most tedious task for a web
developer, symfony provides a
`form sub-framework <#chapter_10_the_form_framework>`_. The form
framework comes bundled with a lot of
`widgets <http://www.symfony-project.org/api/1_4/widget>`_ and
`validators <http://www.symfony-project.org/api/1_4/validator>`_.
One of the strength of the form sub-framework is that templates are
very easily
`customizables <#chapter_10_sidebar_customizing_the_look_and_feel_of_a_form>`_.

If you use ##ORM##, the form framework also makes it easy to
`generate forms and filters <#chapter_10_propel_forms>`_ based on
your models.
`generate forms and filters <#chapter_10_doctrine_forms>`_ based on
your models.

Internationalization and Localization
-------------------------------------

`Internationalization <#chapter_19_internationalization>`_ and
`localization <#chapter_19_localization>`_ are supported by
symfony, thanks to the ICU standard. The
`user culture <#chapter_19_sub_the_user_culture>`_ determines the
language and the country of the user. It can be defined by the user
itself, or embedded in the
`URL <#chapter_19_culture_in_the_url>`_.

Tests
-----

The lime library, used for **unit tests**, provides a lot of
`testing methods <#chapter_08_the_lime_lime_testing_framework_testing_framework>`_.
The
`Propel objects can also be tested <#chapter_08_propel_unit_tests>`_
from
`Doctrine objects can also be tested <#chapter_08_doctrine_unit_tests>`_
from a
`dedicated database <#chapter_08_sub_database_configuration>`_ and
with dedicated `fixtures <#chapter_08_sub_test_data>`_.

Unit tests can be run
`one at a time <#chapter_08_running_unit_tests>`_ or
`all together <#chapter_08_unit_tests_harness>`_.

**Functional tests** are written with the
```sfFunctionalTest`` <#chapter_09_the_sftestfunctional_class>`_
class, which uses a
`browser simulator <#chapter_09_the_sfbrowser_class>`_ and allows
symfony core objects introspection through
`Testers <#chapter_09_the_sftestfunctional_class>`_. Testers exist
for the `request object <#chapter_09_sub_the_request_tester>`_, the
`response object <#chapter_09_sub_the_response_tester>`_, the
`user object <#chapter_13_user_testing>`_, the
`current form object <#chapter_11_the_form_tester>`_, the
`cache layer <#chapter_21_testing_the_cache_testing_cache>`_ and
the `Propel objects <#chapter_11_the_propel_tester>`_.
`Doctrine objects <#chapter_11_the_doctrine_tester>`_.

You can also use debugging tools for the
`response <#chapter_09_debugging_functional_tests>`_ and
`forms <#chapter_11_the_form_tester>`_.

As for the unit tests, functional tests can be run
`one by one <#chapter_09_running_functional_tests>`_ or
`all together <#chapter_09_functional_tests_harness>`_.

You can also run
`all tests together <#chapter_09_tests_harness>`_.

Plugins
-------

The symfony framework only provides the foundation for your web
applications and relies on `plugins <#chapter_20_using_plugins>`_
to add more features. In this tutorial, we have talked about
```sfGuardPlugin`` <#chapter_13_plugins>`_,
```sfFormExtraPlugin`` <#chapter_19_language_switching>`_, and
```sfTaskExtraPlugin`` <#chapter_20_contributing_a_plugin>`_.

A plugin must be
`activated <#chapter_20_sidebar_plugin_activation>`_ after
installation.

Plugins are the best way to
`contribute back <#chapter_20_contributing_a_plugin>`_ to the
symfony project.

\ :sub:`Tasks`\ 
----------------

The symfony CLI provides a lot of tasks, and the most useful have
been discussed in this tutorial:


-  ```app:routes`` <#chapter_05_route_debugging>`_
-  ```cache:clear`` <#chapter_21_clearing_the_cache>`_
-  ```configure:database`` <#chapter_03_the_database>`_
-  ```generate:project`` <#chapter_01_sub_project_creation>`_
-  ```generate:app`` <#chapter_01_sub_application_creation>`_
-  ```generate:module`` <#chapter_07_job_category_module_creation>`_
-  ```help`` <#chapter_03_the_orm>`_
-  ```i18n:extract`` <#chapter_19_sub_i18n_extract>`_
-  ```list`` <#chapter_13_backend_security>`_
-  ```plugin:install`` <#chapter_13_plugins>`_
-  ```plugin:publish-assets`` <#chapter_20_sub_the_assets>`_
-  ```project:deploy`` <#chapter_22_sub_deploying_strategies>`_
-  ```propel:build --all`` <#chapter_03_the_orm>`_
-  ```propel:build --all -and-load`` <#chapter_07_update_the_database>`_
-  ```propel:build-forms`` <#chapter_10_propel_forms>`_
-  ```doctrine:build --all`` <#chapter_03_the_orm>`_
-  ```doctrine:build --all -and-load`` <#chapter_07_update_the_database>`_
-  ```doctrine:build --forms`` <#chapter_10_doctrine_forms>`_
-  ```propel:build-model`` <#chapter_03_the_orm>`_
-  ```propel:build-sql`` <#chapter_03_the_orm>`_
-  ```propel:data-load`` <#chapter_03_the_initial_data>`_
-  ```propel:generate-admin`` <#chapter_12_backend_modules>`_
-  ```propel:generate-module`` <#chapter_03_see_it_in_action_in_the_browser>`_
-  ```propel:insert-sql`` <#chapter_03_the_orm>`_
-  ```test:all`` <#chapter_09_tests_harness>`_
-  ```test:coverage`` <#chapter_08_sidebar__code_coverage_>`_
-  ```test:functional`` <#chapter_09_running_functional_tests>`_
-  ```test:unit`` <#chapter_08_running_unit_tests>`_

You can also
`create your own tasks <#chapter_11_maintenance_tasks>`_.

See you soon
------------

Learning by Practicing
~~~~~~~~~~~~~~~~~~~~~~

The symfony framework, as does any piece of software, has a
learning curve. In the learning process, the first step is to learn
from practical examples with a book like this one. The second step
is to **practice**. Nothing will ever replace practicing.

That's what you can start doing today. Think about the simplest web
project that still provides some value: a todo list manager, a
simple blog, a time or currency converter, whatever... Choose one
and start implementing it with the knowledge you have today. Use
the task help messages to learn the different options, browse the
code generated by symfony, use a text editor that has PHP
auto-completion support like `Eclipse <http://www.eclipse.org/>`_,
and refer to the
`reference guide <http://www.symfony-project.org/reference/1_4/>`_
to browse all the configuration provided by the framework.

Enjoy all the free material you have at your disposal to learn more
about symfony.

The community
~~~~~~~~~~~~~

Before you leave, I would like to talk about one last thing about
symfony. The framework has a lot of great features and a lot of
free documentation. But, one of the most valuable asset an
Open-Source can have is its community. And symfony has one of the
most amazing and active community around. If you start using
symfony for your projects, consider joining the symfony community:


-  Subscribe to the
   `user mailing-list <http://groups.google.com/group/symfony-users>`_
-  Subscribe to the official
   `blog feed <http://feeds.feedburner.com/symfony/blog>`_
-  Subscribe to the symfony
   `planet feed <http://feeds.feedburner.com/symfony/planet>`_
-  Come and chat on the
   `#symfony IRC <irc://irc.freenode.net/symfony>`_ channel on
   freenode

**ORM**


