.. index::
   single: event
   single: subscriber
   single: INewRequest
   single: INewResponse
   pair: subscriber; ZCML directive

.. _events_chapter:

Using Events
=============

An *event* is an object broadcast by the :mod:`pyramid` framework
at interesting points during the lifetime of an application.  You
don't need to use events in order to create most :mod:`pyramid`
applications, but they can be useful when you want to perform slightly
advanced operations.  For example, subscribing to an event can allow
you to run some code as the result of every new request.

Events in :mod:`pyramid` are always broadcast by the framework.
However, they only become useful when you register a *subscriber*.  A
subscriber is a function that accepts a single argument named `event`:

.. code-block:: python
   :linenos:

   def mysubscriber(event):
       print event

The above is a subscriber that simply prints the event to the console
when it's called.

The mere existence of a subscriber function, however, is not
sufficient to arrange for it to be called.  To arrange for the
subscriber to be called, you'll need to use the
:meth:`pyramid.configuration.Configurator.add_subscriber` method to
register the subscriber imperatively, or via a decorator, or you'll
need to use ZCML for the same purpose:

.. topic:: Configuring an Event Listener Imperatively

   You can imperatively configure a subscriber function to be called
   for some event type via the
   :meth:`pyramid.configuration.Configurator.add_subscriber`
   method (see also :term:`Configurator`):

   .. code-block:: python
      :linenos:

      from pyramid.interfaces import INewRequest

      from subscribers import mysubscriber

      # "config" below is assumed to be an instance of a 
      # pyramid.configuration.Configurator object

      config.add_subscriber(mysubscriber, INewRequest)

   The first argument to
   :meth:`pyramid.configuration.Configurator.add_subscriber` is the
   subscriber function (or a :term:`dotted Python name` which refers
   to a subscriber callable); the second argument is the event type.

.. topic:: Configuring an Event Listener Using a Decorator

   You can configure a subscriber function to be called for some event
   type via the :func:`pyramid.events.subscriber` function.

   .. code-block:: python
      :linenos:

      from pyramid.interfaces import INewRequest
      from pyramid.events import subscriber

      @subscriber(INewRequest)
      def mysubscriber(event):
          event.request.foo = 1

   When the :func:`pyramid.subscriber` decorator is used a
   :term:`scan` must be performed against the package containing the
   decorated function for the decorator to have any effect.  See
   :func:`pyramid.subscriber` for more information.

.. topic:: Configuring an Event Listener Through ZCML

   You can configure an event listener by modifying your application's
   ``configure.zcml``.  Here's an example of a bit of XML you can add
   to the ``configure.zcml`` file which registers the above
   ``mysubscriber`` function, which we assume lives in a
   ``subscribers.py`` module within your application:

   .. code-block:: xml
      :linenos:

      <subscriber
         for="pyramid.interfaces.INewRequest"
         handler=".subscribers.mysubscriber"
       />

   See also :ref:`subscriber_directive`.

Either of the above registration examples implies that every time the
:mod:`pyramid` framework emits an event object that supplies an
:class:`pyramid.interfaces.INewRequest` interface, the
``mysubscriber`` function will be called with an *event* object.

As you can see, a subscription is made in terms of an
:term:`interface`.  The event object sent to a subscriber will always
be an object that possesses an interface.  The interface itself
provides documentation of what attributes of the event are available.

The return value of a subscriber function is ignored.  Subscribers to
the same event type are not guaranteed to be called in any particular
order relative to each other.

All the concrete :mod:`pyramid` event types are documented in the
:ref:`events_module` API documentation.

An Example
----------

If you create event listener functions in a ``subscribers.py`` file in
your application like so:

.. code-block:: python
   :linenos:

   def handle_new_request(event):
       print 'request', event.request   

   def handle_new_response(event):
       print 'response', event.response

You may configure these functions to be called at the appropriate
times by adding the following ZCML to your application's
``configure.zcml`` file:

.. code-block:: xml
   :linenos:

   <subscriber
      for="pyramid.interfaces.INewRequest"
      handler=".subscribers.handle_new_request"
    />

   <subscriber
      for="pyramid.interfaces.INewResponse"
      handler=".subscribers.handle_new_response"
    />

If you're not using ZCML, the
:meth:`pyramid.configuration.Configurator.add_subscriber` method
can alternately be used to perform the same job:

.. ignore-next-block
.. code-block:: python
   :linenos:

   from pyramid.interfaces import INewRequest
   from pyramid.interfaces import INewResponse

   from subscribers import handle_new_request
   from subscribers import handle_new_response

   # "config" below is assumed to be an instance of a 
   # pyramid.configuration.Configurator object

   config.add_subscriber(handle_new_request, INewRequest)
   config.add_subscriber(handle_new_response, INewResponse)

Either mechanism causes the functions in ``subscribers.py`` to be
registered as event subscribers.  Under this configuration, when the
application is run, each time a new request or response is detected, a
message will be printed to the console.

Each of our subscriber functions accepts an ``event`` object and
prints an attribute of the event object.  This begs the question: how
can we know which attributes a particular event has?

We know that :class:`pyramid.interfaces.INewRequest` event objects
have a ``request`` attribute, which is a :term:`request` object,
because the interface defined at
:class:`pyramid.interfaces.INewRequest` says it must.  Likewise, we
know that :class:`pyramid.interfaces.INewResponse` events have a
``response`` attribute, which is a response object constructed by your
application, because the interface defined at
:class:`pyramid.interfaces.INewResponse` says it must
(:class:`pyramid.interfaces.INewResponse` objects also have a
``request``).

