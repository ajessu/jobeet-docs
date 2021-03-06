NOT CONVERTED TO SYMFONY2 YET
=============================

Want to help out?
Fork it on `Github <https://github.com/sftuts/jobeet-docs>`_

Day 16: The Mailer
=============================

Yesterday, we added a read-only web service to Jobeet. Affiliates
can now create an account but it needs to be activated by the
administrator before it can be used. In order for the affiliate to
get its token, we still need to implement the email
notification. That's what we will start doing in the coming lines.

The symfony framework comes bundled with one of the best PHP
emailing solution: `Swift Mailer <http://www.swiftmailer.org/>`_.
Of course, the library is fully integrated with symfony, with some
cool features added on top of its default features.

    **NOTE** Symfony 1.3/1.4 uses Swift Mailer version 4.1.


Sending simple Emails
---------------------

Let's start by sending a simple email to notify the affiliate when
his account has been confirmed and to give him the affiliate
token.

Replace the ``activate`` action with the following code:

::

    <?php
    // apps/backend/modules/affiliate/actions/actions.class.php
    class affiliateActions extends autoAffiliateActions
    {
      public function executeListActivate()
      {
        $affiliate = $this->getRoute()->getObject();
        $affiliate->activate();
    
        // send an email to the affiliate
        $message = $this->getMailer()->compose(
          array('jobeet@example.com' => 'Jobeet Bot'),
          $affiliate->getEmail(),
          'Jobeet affiliate token',
          <<<EOF
    Your Jobeet affiliate account has been activated.
    
    Your token is {$affiliate->getToken()}.
    
    The Jobeet Bot.
    EOF
        );
    
        $this->getMailer()->send($message);
    
        $this->redirect('jobeet_affiliate');
      }
    
      // ...
    }

    **NOTE** For the code to work properly, you should change the
    ``jobeet@example.com`` email address to a real one.


Email management in symfony is centered around a mailer object,
which can be retrieved from an action with the
``getMailer()`` method.

The ``compose()`` method takes four arguments and
returns an email message object:


-  the sender email address (``from``);
-  the recipient email address(es) (``to``);
-  the subject of the message;
-  the body of the message.

Sending the message is then as simple as calling the ``send()``
method on the mailer instance and passing the message as an
argument. As a shortcut, you can only compose and send an email in
one go by using the ``composeAndSend()`` method.

    **TIP** The email message is an instance of the ``Swift_Message``
    class. Refer to the Swift Mailer official
    `documentation <http://www.swiftmailer.org/docs>`_ to learn more
    about this object, and how to do more advanced stuff like attaching
    files.


Configuration
-------------

By default, the ``send()`` method tries to use a local SMTP server
to send the message to the recipient. Of course, as many things in
symfony, this is totally configurable.

Factories
~~~~~~~~~~~~~~~~~~~~

During the previous days, we have already talked about symfony core
objects like the ``user``, ``request``, ``response``, or the
``routing``. These objects are automatically created, configured,
and managed by the symfony framework. They are always accessible
from the ``sfContext`` object, and like many things in
the framework, they are configurable via a configuration file:
``factories.yml``. This file is configurable by
environment.

When the ``sfContext`` initializes the core factories, it reads the
``factories.yml`` file for the class names (``class``) and the
parameters (``param``) to pass to the constructor:

::

    [yml]
    response:
      class: sfWebResponse
      param:
        send_http_headers: false

In the above snippet, to create the response factory, symfony
instantiates a ``sfWebResponse`` object and passes the
``send_http_headers`` option as a parameter.

    **SIDEBAR** The ``sfContext`` class

    The ``sfContext`` object contains references to symfony
    core objects like the request, the response, the user, and so on.
    As ``sfContext`` acts like a singleton, you can use the
    ``sfContext::getInstance()`` statement to get it from anywhere and
    then have access to any symfony core objects:

    ::

        <?php
        $mailer = sfContext::getInstance()->getMailer();

    Whenever you want to use the ``sfContext::getInstance()`` in one of
    your class, think twice as it introduces a ~strong coupling\|Strong
    Coupling~. It is quite always better to pass the object you need as
    an argument.

    You can even use ``sfContext`` as a registry and add
    your own objects using the ``set()`` methods. It takes a name and
    an object as arguments and the ``get()`` method can be used later
    on to retrieve an object by name:

    ::

        <?php
        sfContext::getInstance()->set('job', $job);
        $job = sfContext::getInstance()->get('job');


Delivery Strategy
~~~~~~~~~~~~~~~~~~~

Like many other core symfony objects, the mailer is a factory. So,
it is configured in the ``factories.yml`` configuration file. The
default configuration reads as follows:

::

    [yml]
    mailer:
      class: sfMailer
      param:
        logging:           %SF_LOGGING_ENABLED%
        charset:           %SF_CHARSET%
        delivery_strategy: realtime
        transport:
          class: Swift_SmtpTransport
          param:
            host:       localhost
            port:       25
            encryption: ~
            username:   ~
            password:   ~

When creating a new application, the local ``factories.yml``
configuration file overrides the default configuration with some
sensible defaults for the ``env`` and ``test`` environments:

::

    [yml]
    test:
      mailer:
        param:
          delivery_strategy: none
    
    dev:
      mailer:
        param:
          delivery_strategy: none

The ``delivery_strategy`` setting tells symfony how to deliver
emails. By default, symfony comes with four different strategies:


-  ``realtime``: Messages are sent in realtime.
-  ``single_address``: Messages are sent to a single address.
-  ``spool``: Messages are stored in a queue.
-  ``none``: Messages are simply ignored.

Whatever the strategy, emails are always logged and available in
the "mailer" panel in the web debug toolbar.

Mail Transport
~~~~~~~~~~~~~~~~

Mail messages are actually sent by a transport. The transport is
configured in the ``factories.yml`` configuration file, and the
default configuration uses the SMTP server of the local machine:

::

    [yml]
    transport:
      class: Swift_SmtpTransport
      param:
        host:       localhost
        port:       25
        encryption: ~
        username:   ~
        password:   ~

Swift Mailer comes bundled with three different transport classes:


-  ``Swift_SmtpTransport``: Uses a SMTP server to send
   messages.

-  ``Swift_SendmailTransport``: Uses ``sendmail`` to
   send messages.

-  ``Swift_MailTransport``: Uses the native PHP
   ``mail()`` function to send messages.


    **TIP** The
    `"Transport Types" <http://swiftmailer.org/docs/transport-types>`_
    section of the Swift Mailer official documentation describes all
    you need to know about the built-in transport classes and their
    different parameters.


Testing Emails
--------------

Now that we have seen how to send an email with the symfony mailer,
let's write some functional tests to ensure we did the right thing.
By default, symfony registers a ``mailer`` tester
(``sfMailerTester``) to ease mail testing in functional
tests.

First, change the ``mailer`` factory's configuration for the
``test`` environment if your web server does not have a local SMTP
server. We have to replace the current ``Swift_SmtpTransport``
class by ``Swift_MailTransport``:

::

    [yaml]
    # apps/backend/config/factories.yml
    test:
    
      # ...
    
      mailer:
        param:
          delivery_strategy: none
          transport:
            class:  Swift_MailTransport

Then, add a new ``test/fixtures/administrators.yml`` file
containing the following YAML definition:

::

    [yaml]
    sfGuardUser:
      admin:
        email_address: admin@example.com
        username: admin
        password: admin
        first_name: Fabien
        last_name: Potencier
        is_super_admin: true

Finally, replace the ``affiliate`` functional test file for the
backend application with the following code:

::

    <?php
    // test/functional/backend/affiliateActionsTest.php
    include(dirname(__FILE__).'/../../bootstrap/functional.php');
    
    $browser = new JobeetTestFunctional(new sfBrowser());
    $browser->loadData();
    
    $browser->
      info('1 - Authentication')->
      get('/affiliate')->
      click('Signin', array(
        'signin' => array('username' => 'admin', 'password' => 'admin'),
        array('_with_csrf' => true)
      ))->
      with('response')->isRedirected()->
      followRedirect()->
    
      info('2 - When validating an affiliate, an email must be sent with its token')->
      click('Activate', array(), array('position' => 1))->
      with('mailer')->begin()->
        checkHeader('Subject', '/Jobeet affiliate token/')->
        checkBody('/Your token is symfony/')->
      end()
    ;

Each sent email can be tested with the help of the
``checkHeader()```\  and \ :sub:```checkBody()``
methods. The second argument of ``checkHeader()`` and the first
argument of ``checkBody()`` can be one of the following:


-  a string to check an exact match;
-  a regular expression to check the value against it;
-  a negative regular expression (a regular expression starting
   with a ``!``) to check that the value does not match.

    **NOTE** By default, checks are done on the first email sent. If
    several emails have been sent, you can choose the one you want to
    test with the ``withMessage()`` method. The
    ``withMessage()`` takes a recipient as its first argument. It also
    takes a second argument to indicate which email you want to test if
    several ones have been sent to the same recipient.


-

    **TIP** Like other built-in testers, you can see the raw message by
    calling the ``debug()`` method.


Final Thoughts
--------------

Tomorrow, we will implement the last missing feature of the Jobeet
website, the search engine.

**ORM**


