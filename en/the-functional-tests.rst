NOT CONVERTED TO SYMFONY2 YET
=============================

Want to help out?
Fork it on `Github <https://github.com/sftuts/jobeet-docs>`_

Day 9: The Functional Tests
===========================

Yesterday, we saw how to unit test our Jobeet classes using the
lime testing library packaged with symfony. Today, we will write
functional tests for the features we have already implemented in
the ``job`` and ``category`` modules.

Functional Tests
----------------

Functional tests are a great tool to test your application from
end to end: from the request made by a browser to the response sent
by the server. They test all the layers of an
application: the routing, the model, the actions, and the
templates. They are very similar to what you probably already do
manually: each time you add or modify an action, you need to go to
the browser and check that everything works as expected by clicking
on links and checking elements on the rendered page. In other
words, you run a scenario corresponding to the use case you have
just implemented.

As the process is manual, it is tedious and error prone. Each time
you change something in your code, you must step through all the
scenarios to ensure that you did not break something. That's
insane. Functional tests in symfony provide a way to easily
describe scenarios. Each scenario can then be played automatically
over and over again by simulating the experience a user has in a
browser. Like unit tests, they give you the confidence to code in
peace.

    **NOTE** The functional test framework does not replace tools like
    "`Selenium <http://selenium.seleniumhq.org/>`_".
    Selenium runs directly in the browser to automate testing across
    many platforms and browsers and as such, it is able to test your
    application's JavaScript.


The ``sfBrowser`` class
-----------------------

In symfony, functional tests are run through a special
browser, implemented by the
```sfBrowser`` <http://www.symfony-project.org/api/1_4/sfBrowser>`_
class. It acts as a browser tailored for your application and
directly connected to it, without the need for a web server. It
gives you access to all symfony objects before and after each
request, giving you the opportunity to introspect them and do the
checks you want programatically.

``sfBrowser`` provides methods that simulates navigation done in a
classic browser:

\| Method \| Description \| ------------ \|
------------------------------------------------- \| ``get()`` \|
Gets a URL \| ``post()`` \| Posts to a URL \| ``call()`` \| Calls a
URL (used for ``PUT`` and ``DELETE`` methods) \| ``back()`` \| Goes
back one page in the history \| ``forward()`` \| Goes forward one
page in the history \| ``reload()`` \| Reloads the current page \|
``click()`` \| Clicks on a link or a button \| ``select()`` \|
selects a radiobutton or checkbox \| ``deselect()`` \| deselects a
radiobutton or checkbox \| ``restart()`` \| Restarts the browser

Here are some usage examples of the ``sfBrowser`` methods:

::

    <?php
    $browser = new sfBrowser();
    
    $browser->
      get('/')->
      click('Design')->
      get('/category/programming?page=2')->
      get('/category/programming', array('page' => 2))->
      post('search', array('keywords' => 'php'))
    ;

``sfBrowser`` contains additional methods to configure the browser
behavior:

\| Method \| Description \| ------------------ \|
------------------------------------------------- \|
``setHttpHeader()`` \| Sets an HTTP header \| ``setAuth()`` \| Sets
the basic authentication credentials \| ``setCookie()`` \| Set a
cookie \| ``removeCookie()`` \| Removes a cookie \|
``clearCookies()`` \| Clears all current cookies \|
``followRedirect()`` \| Follows a redirect

The ``sfTestFunctional`` class
------------------------------

We have a browser, but we need a way to introspect the symfony
objects to do the actual testing. It can be done with lime and some
``sfBrowser`` methods like ``getResponse()`` and ``getRequest()``
but symfony provides a better way.

The test methods are provided by another class,
```sfTestFunctional`` <http://www.symfony-project.org/api/1_4/sfTestFunctional>`_
that takes a ``sfBrowser`` instance in its constructor. The
``sfTestFunctional`` class delegates the tests to
**tester** objects. Several testers are bundled
with symfony, and you can also create your own.

As we saw in day 8, functional tests are stored under the
``test/functional/`` directory. For Jobeet, tests are to be found
in the ``test/functional/frontend/`` sub-directory as each
application has its own subdirectory. This directory already
contains two files: ``categoryActionsTest.php``, and
``jobActionsTest.php`` as all tasks that generate a module
automatically create a basic functional test file:

::

    <?php
    // test/functional/frontend/categoryActionsTest.php
    include(dirname(__FILE__).'/../../bootstrap/functional.php');
    
    $browser = new sfTestFunctional(new sfBrowser());
    
    $browser->
      get('/category/index')->
    
      with('request')->begin()->
        isParameter('module', 'category')->
        isParameter('action', 'index')->
      end()->
    
      with('response')->begin()->
        isStatusCode(200)->
        checkElement('body', '!/This is a temporary page/')->
      end()
    ;

At first sight, the script above may look a bit strange to you.
That's because methods of ``sfBrowser`` and ``sfTestFunctional``
implement a
`fluent interface <http://en.wikipedia.org/wiki/Fluent_interface>`_
by always returning ``$this``. It allows you to chain method calls
for better readability. The above snippet is equivalent to:

::

    <?php
    // test/functional/frontend/categoryActionsTest.php
    include(dirname(__FILE__).'/../../bootstrap/functional.php');
    
    $browser = new sfTestFunctional(new sfBrowser());
    
    $browser->get('/category/index');
    $browser->with('request')->begin();
    $browser->isParameter('module', 'category');
    $browser->isParameter('action', 'index');
    $browser->end();
    
    $browser->with('response')->begin();
    $browser->isStatusCode(200);
    $browser->checkElement('body', '!/This is a temporary page/');
    $browser->end();

Tests are run within a tester block context. A tester block context
begins with ``with('TESTER NAME')->begin()`` and ends with
``end()``:

::

    <?php
    $browser->
      with('request')->begin()->
        isParameter('module', 'category')->
        isParameter('action', 'index')->
      end()
    ;

The code tests that the request parameter ``module`` equals
``category`` and ``action`` equals ``index``.

    **TIP** When you only need to call one test method on a tester, you
    don't need to create a block:
    ``with('request')->isParameter('module', 'category')``.


The Request Tester
~~~~~~~~~~~~~~~~~~

The **request tester** provides tester
methods to introspect and test the ``sfWebRequest`` object:

\| Method \| Description \| ------------------ \|
------------------------------------------------ \|
``isParameter()`` \| Checks a request parameter value \|
``isFormat()`` \| Checks the format of a request \| ``isMethod()``
\| Checks the method \| ``hasCookie()`` \| Checks whether the
request has a cookie with the \| \| given name \| ``isCookie()`` \|
Checks the value of a cookie

The Response Tester
~~~~~~~~~~~~~~~~~~~

There is also a **response tester** class
that provides tester methods against the ``sfWebResponse`` object:

\| Method \| Description \| ------------------ \|
----------------------------------------------------- \|
``checkElement()`` \| Checks if a response CSS selector match some
criteria \| ``checkForm()`` \| Checks an ``sfForm`` form object \|
``debug()`` \| Prints the response output to ease debug \|
``matches()`` \| Tests a response against a regexp \|
``isHeader()`` \| Checks the value of a header \|
``isStatusCode()`` \| Checks the response status code \|
``isRedirected()`` \| Checks if the current response is a redirect
\| ``isValid()`` \| Checks if a response is well-formed XML (you
also validate the response again its document type be passing
``true`` as an argument)

    **NOTE** We will describe more testers classes in the coming days
    (for forms, user, cache, ...).


Running Functional Tests
------------------------

As for unit tests, launching functional tests can be done by
executing the test file directly:

::

    $ php test/functional/frontend/categoryActionsTest.php

Or by using the ``test:functional`` task:

::

    $ php symfony test:functional frontend categoryActions

.. figure:: http://www.symfony-project.org/images/jobeet/1_4/09/cli_tests.png
   :alt: Tests on the command line
   
   Tests on the command line

Test Data
---------

As for ##ORM## unit tests, we need to load test data each time we
launch a functional test. We can reuse the code we have written
previously:

::

    <?php
    include(dirname(__FILE__).'/../../bootstrap/functional.php');
    
    $browser = new sfTestFunctional(new sfBrowser());

$loader = new sfPropelData();
$loader->loadData(sfConfig::get('sf\_test\_dir').'/fixtures');
Doctrine\_Core::loadData(sfConfig::get('sf\_test\_dir').'/fixtures');

Loading data in a functional test is a bit easier than in unit
tests as the database has already been initialized by the
bootstrapping script.

As for unit tests, we won't copy and paste this snippet of code in
each test file, but we will rather create our own functional class
that inherits from ``sfTestFunctional``:

::

    <?php
    // lib/test/JobeetTestFunctional.class.php
    class JobeetTestFunctional extends sfTestFunctional
    {
      public function loadData()
      {

$loader = new sfPropelData();
$loader->loadData(sfConfig::get('sf\_test\_dir').'/fixtures');
Doctrine\_Core::loadData(sfConfig::get('sf\_test\_dir').'/fixtures');

::

        return $this;
      }
    }

Writing Functional Tests
------------------------

Writing functional tests is like playing a scenario in a browser.
We already have written all the scenarios we need to test as part
of the day 2 stories.

First, let's test the Jobeet homepage by editing the
``jobActionsTest.php`` test file. Replace the code with the
following one:

Expired jobs are not listed
~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    <?php
    // test/functional/frontend/jobActionsTest.php
    include(dirname(__FILE__).'/../../bootstrap/functional.php');
    
    $browser = new JobeetTestFunctional(new sfBrowser());
    $browser->loadData();
    
    $browser->info('1 - The homepage')->
      get('/')->
      with('request')->begin()->
        isParameter('module', 'job')->
        isParameter('action', 'index')->
      end()->
      with('response')->begin()->
        info('  1.1 - Expired jobs are not listed')->
        checkElement('.jobs td.position:contains("expired")', false)->
      end()
    ;

As with ``lime``, an informational message can be inserted by
calling the ``info()`` method to make the output more readable. To
verify the exclusion of expired jobs from the homepage, we check
that the CSS selector ``.jobs td.position:contains("expired")``
does not match anywhere in the response HTML content (remember that
in the fixture files, the only expired job we have contains
"expired" in the position). When the second argument of the
``checkElement()`` method is a Boolean, the method tests the
existence of nodes that match the CSS selector.

    **TIP** The ``checkElement()`` method is able to interpret most
    valid CSS3 selectors.


Only n jobs are listed for a category
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Add the following code at the end of the test file:

::

    <?php
    // test/functional/frontend/jobActionsTest.php
    $max = sfConfig::get('app_max_jobs_on_homepage');
    
    $browser->info('1 - The homepage')->
      get('/')->
      info(sprintf('  1.2 - Only %s jobs are listed for a category', $max))->
      with('response')->
        checkElement('.category_programming tr', $max)
    ;

The ``checkElement()`` method can also check that a CSS selector
matches 'n' nodes in the document by passing an integer as its
second argument.

A category has a link to the category page only if too many jobs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    <?php
    // test/functional/frontend/jobActionsTest.php
    $browser->info('1 - The homepage')->
      get('/')->
      info('  1.3 - A category has a link to the category page only if too many jobs')->
      with('response')->begin()->
        checkElement('.category_design .more_jobs', false)->
        checkElement('.category_programming .more_jobs')->
      end()
    ;

In these tests, we check that there is no "more jobs" link for the
design category (``.category_design .more_jobs`` does not exist),
and that there is a "more jobs" link for the programming category
(``.category_programming .more_jobs`` does exist).

Jobs are sorted by date
~~~~~~~~~~~~~~~~~~~~~~~

::

    <?php

// most recent job in the programming category $criteria = new
Criteria(); $criteria->add(JobeetCategoryPeer::SLUG,
'programming');
:math:`$category = JobeetCategoryPeer::doSelectOne($`criteria);

::

    $criteria = new Criteria();
    $criteria->add(JobeetJobPeer::EXPIRES_AT, time(), Criteria::GREATER_THAN);
    $criteria->add(JobeetJobPeer::CATEGORY_ID, $category->getId());
    $criteria->addDescendingOrderByColumn(JobeetJobPeer::CREATED_AT);
    
    $job = JobeetJobPeer::doSelectOne($criteria);

$q = Doctrine\_Query::create() ->select('j.\*') ->from('JobeetJob
j') ->leftJoin('j.JobeetCategory c') ->where('c.slug = ?',
'programming') ->andWhere('j.expires\_at > ?', date('Y-m-d',
time())) ->orderBy('j.created\_at DESC');

::

    $job = $q->fetchOne();

$browser->info('1 - The homepage')-> get('/')-> info(' 1.4 - Jobs
are sorted by date')-> with('response')->begin()->
checkElement(sprintf('.category\_programming tr:first
a[href\*="/%d/"]', $job->getId()))-> end() ;

To test if jobs are actually sorted by date, we need to check that
the first job listed on the homepage is the one we expect. This can
be done by checking that the URL contains the expected ~primary
key\|Primary Key~. As the primary key can change between runs, we
need to get the ##ORM## object from the database first.

Even if the test works as is, we need to refactor the code a bit,
as getting the first job of the programming category can be reused
elsewhere in our tests. We won't move the code to the Model layer
as the code is test specific. Instead, we will move the code to the
``JobeetTestFunctional`` class we have created earlier. This class
acts as a Domain Specific functional tester class for
Jobeet:

::

    <?php
    // lib/test/JobeetTestFunctional.class.php
    class JobeetTestFunctional extends sfTestFunctional
    {
      public function getMostRecentProgrammingJob()
      {

// most recent job in the programming category $criteria = new
Criteria(); $criteria->add(JobeetCategoryPeer::SLUG,
'programming');
:math:`$category = JobeetCategoryPeer::doSelectOne($`criteria);

::

        $criteria = new Criteria();
        $criteria->add(JobeetJobPeer::EXPIRES_AT, time(), Criteria::GREATER_THAN);
        $criteria->add(JobeetJobPeer::CATEGORY_ID, $category->getId());
        $criteria->addDescendingOrderByColumn(JobeetJobPeer::CREATED_AT);
    
        return JobeetJobPeer::doSelectOne($criteria);

$q = Doctrine\_Query::create() ->select('j.\*') ->from('JobeetJob
j') ->leftJoin('j.JobeetCategory c') ->where('c.slug = ?',
'programming');
:math:`$q = Doctrine_Core::getTable('JobeetJob')->addActiveJobsQuery($`q);

::

        return $q->fetchOne();

}

::

      // ...
    }

You can now replace the previous test code by the following one:

::

    <?php
    // test/functional/frontend/jobActionsTest.php
    $browser->info('1 - The homepage')->
      get('/')->
      info('  1.4 - Jobs are sorted by date')->
      with('response')->begin()->
        checkElement(sprintf('.category_programming tr:first a[href*="/%d/"]',
          $browser->getMostRecentProgrammingJob()->getId()))->
      end()
    ;

Each job on the homepage is clickable
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    <?php
    $job = $browser->getMostRecentProgrammingJob();
    
    $browser->info('2 - The job page')->
      get('/')->
    
      info('  2.1 - Each job on the homepage is clickable and give detailed information')->
      click('Web Developer', array(), array('position' => 1))->
      with('request')->begin()->
        isParameter('module', 'job')->
        isParameter('action', 'show')->
        isParameter('company_slug', $job->getCompanySlug())->
        isParameter('location_slug', $job->getLocationSlug())->
        isParameter('position_slug', $job->getPositionSlug())->
        isParameter('id', $job->getId())->
      end()
    ;

To test the job link on the homepage, we simulate a click on the
"Web Developer" text. As there are many of them on the page, we
have explicitly to asked the browser to click on the first one
(``array('position' => 1)``).

Each request parameter is then tested to ensure that the routing
has done its job correctly.

Learn by the Example
--------------------

In this section, we have provided all the code needed to test the
job and category pages. Read the code carefully as you may learn
some new neat tricks:

::

    <?php
    // lib/test/JobeetTestFunctional.class.php
    class JobeetTestFunctional extends sfTestFunctional
    {
      public function loadData()
      {

$loader = new sfPropelData();
$loader->loadData(sfConfig::get('sf\_test\_dir').'/fixtures');
Doctrine\_Core::loadData(sfConfig::get('sf\_test\_dir').'/fixtures');

::

        return $this;
      }
    
      public function getMostRecentProgrammingJob()
      {

// most recent job in the programming category $criteria = new
Criteria(); $criteria->add(JobeetCategoryPeer::SLUG,
'programming');
:math:`$category = JobeetCategoryPeer::doSelectOne($`criteria);

::

        $criteria = new Criteria();
        $criteria->add(JobeetJobPeer::EXPIRES_AT, time(), Criteria::GREATER_THAN);
        $criteria->addDescendingOrderByColumn(JobeetJobPeer::CREATED_AT);
    
        return JobeetJobPeer::doSelectOne($criteria);

$q = Doctrine\_Query::create() ->select('j.\*') ->from('JobeetJob
j') ->leftJoin('j.JobeetCategory c') ->where('c.slug = ?',
'programming');
:math:`$q = Doctrine_Core::getTable('JobeetJob')->addActiveJobsQuery($`q);

::

        return $q->fetchOne();

}

::

      public function getExpiredJob()
      {

// expired job $criteria = new Criteria();
$criteria->add(JobeetJobPeer::EXPIRES\_AT, time(),
Criteria::LESS\_THAN);

::

        return JobeetJobPeer::doSelectOne($criteria);

$q = Doctrine\_Query::create() ->from('JobeetJob j')
->where('j.expires\_at < ?', date('Y-m-d', time()));

::

        return $q->fetchOne();

} }

::

    // test/functional/frontend/jobActionsTest.php
    include(dirname(__FILE__).'/../../bootstrap/functional.php');
    
    $browser = new JobeetTestFunctional(new sfBrowser());
    $browser->loadData();
    
    $browser->info('1 - The homepage')->
      get('/')->
      with('request')->begin()->
        isParameter('module', 'job')->
        isParameter('action', 'index')->
      end()->
      with('response')->begin()->
        info('  1.1 - Expired jobs are not listed')->
        checkElement('.jobs td.position:contains("expired")', false)->
      end()
    ;
    
    $max = sfConfig::get('app_max_jobs_on_homepage');
    
    $browser->info('1 - The homepage')->
      info(sprintf('  1.2 - Only %s jobs are listed for a category', $max))->
      with('response')->
        checkElement('.category_programming tr', $max)
    ;
    
    $browser->info('1 - The homepage')->
      get('/')->
      info('  1.3 - A category has a link to the category page only if too many jobs')->
      with('response')->begin()->
        checkElement('.category_design .more_jobs', false)->
        checkElement('.category_programming .more_jobs')->
      end()
    ;
    
    $browser->info('1 - The homepage')->
      info('  1.4 - Jobs are sorted by date')->
      with('response')->begin()->
        checkElement(sprintf('.category_programming tr:first a[href*="/%d/"]', $browser->getMostRecentProgrammingJob()->getId()))->
      end()
    ;
    
    $job = $browser->getMostRecentProgrammingJob();
    
    $browser->info('2 - The job page')->
      get('/')->
    
      info('  2.1 - Each job on the homepage is clickable and give detailed information')->
      click('Web Developer', array(), array('position' => 1))->
      with('request')->begin()->
        isParameter('module', 'job')->
        isParameter('action', 'show')->
        isParameter('company_slug', $job->getCompanySlug())->
        isParameter('location_slug', $job->getLocationSlug())->
        isParameter('position_slug', $job->getPositionSlug())->
        isParameter('id', $job->getId())->
      end()->
    
      info('  2.2 - A non-existent job forwards the user to a 404')->
      get('/job/foo-inc/milano-italy/0/painter')->
      with('response')->isStatusCode(404)->
    
      info('  2.3 - An expired job page forwards the user to a 404')->
      get(sprintf('/job/sensio-labs/paris-france/%d/web-developer', $browser->getExpiredJob()->getId()))->
      with('response')->isStatusCode(404)
    ;
    
    // test/functional/frontend/categoryActionsTest.php
    include(dirname(__FILE__).'/../../bootstrap/functional.php');
    
    $browser = new JobeetTestFunctional(new sfBrowser());
    $browser->loadData();
    
    $browser->info('1 - The category page')->
      info('  1.1 - Categories on homepage are clickable')->
      get('/')->
      click('Programming')->
      with('request')->begin()->
        isParameter('module', 'category')->
        isParameter('action', 'show')->
        isParameter('slug', 'programming')->
      end()->
    
      info(sprintf('  1.2 - Categories with more than %s jobs also have a "more" link', sfConfig::get('app_max_jobs_on_homepage')))->
      get('/')->
      click('27')->
      with('request')->begin()->
        isParameter('module', 'category')->
        isParameter('action', 'show')->
        isParameter('slug', 'programming')->
      end()->
    
      info(sprintf('  1.3 - Only %s jobs are listed', sfConfig::get('app_max_jobs_on_category')))->
      with('response')->checkElement('.jobs tr', sfConfig::get('app_max_jobs_on_category'))->
    
      info('  1.4 - The job listed is paginated')->
      with('response')->begin()->
        checkElement('.pagination_desc', '/32 jobs/')->
        checkElement('.pagination_desc', '#page 1/2#')->
      end()->
    
      click('2')->
      with('request')->begin()->
        isParameter('page', 2)->
      end()->
      with('response')->checkElement('.pagination_desc', '#page 2/2#')
    ;

Debugging Functional Tests
--------------------------

Sometimes a functional test fails. As symfony simulates a browser
without any graphical interface, it can be hard to diagnose the
problem. Thankfully, symfony provides the ``~debug|Debug~()``
method to output the response header and content:

::

    <?php
    $browser->with('response')->debug();

The ``debug()`` method can be inserted anywhere in a ``response``
tester block and will halt the script execution.

Functional Tests Harness
------------------------

The ``test:functional`` task can also be used to launch all
functional tests for an application:

::

    $ php symfony test:functional frontend

The task outputs a single line for each test file:

.. figure:: http://www.symfony-project.org/images/jobeet/1_4/09/test_harness.png
   :alt: Functional tests harness
   
   Functional tests harness

Tests Harness
-------------

As you may expect, there is also a task to launch all tests for a
project (unit and functional):

::

    $ php symfony test:all

.. figure:: http://www.symfony-project.org/images/jobeet/1_4/09/tests_harness.png
   :alt: Tests harness
   
   Tests harness

When you have a large suite of tests, it can be very time consuming
to launch all tests every time you make a change, especially if
some tests fail. That's because each time you fix a test, you
should run the whole test suite again to ensure that you have not
break something else. But as long as the failed tests are not
fixed, there is no point in re-executing all other tests. The
``test:all`` tasks have a ``--only-failed`` option that forces the
task to only re-execute tests that failed during the previous run:

::

    $ php symfony test:all --only-failed

The first time you run the task, all tests are run as usual. But
for subsequent test runs, only tests that failed last time are
executed. As you fix your code, some tests will pass, and will be
removed from subsequent runs. When all tests pass again, the full
test suite is run... you can then rinse and repeat.

    **TIP** If you want to integrate your test suite in a continuous
    integration process, use the ``--xml`` option to force the
    ``test:all`` task to generate a JUnit compatible XML output.

    ::

         $ php symfony test:all --xml=log.xml


Final Thoughts
--------------

That wraps up our tour of the symfony test tools. You have no
excuse anymore to not test your applications! With the lime
framework and the functional test framework, symfony provides
powerful tools to help you write tests with little effort.

We have just scratched the surface of functional tests. From now
on, each time we implement a feature, we will also write tests to
learn more features of the test framework.

Tomorrow, we will talk about yet another great feature of symfony:
the **form framework**.

**ORM**


