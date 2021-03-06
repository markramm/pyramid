============
Basic Layout
============

The starter files generated by the ``pyramid_routesalchemy`` template
are basic, but they provide a good orientation for the high-level
patterns common to most :term:`url dispatch` -based :mod:`pyramid`
projects.

The source code for this tutorial stage can be browsed at
`http://github.com/Pylons/pyramid/tree/master/docs/tutorials/wiki2/src/basiclayout/
<http://github.com/Pylons/pyramid/tree/master/docs/tutorials/wiki2/src/basiclayout/>`_.

``__init__.py``
---------------

A directory on disk can be turned into a Python :term:`package` by
containing an ``__init__.py`` file.  Even if empty, this marks a
directory as a Python package.

Configuration With ``configure.zcml``
--------------------------------------

:mod:`pyramid` uses a configuration markup language syntactically
the same as Zope's implementation of :term:`ZCML`, but using a
different default XML namespace.  Our sample ZCML file looks like the
following:

   .. literalinclude:: src/basiclayout/tutorial/configure.zcml
      :linenos:
      :language: xml

#. *Line 1*.  The root ``<configure>`` element.

#. *Lines 3-4*. Boilerplate, the comment explains.

#. *Lines 6-11*.  Register a ``<route>`` :term:`route configuration`
   that will be used when the URL is ``/``.  Since this ``<route>``
   has an empty ``pattern`` attribute, it is the "default" route. The
   attribute named ``view`` with the value ``.views.my_view`` is the
   dotted name to a *function* we write (generated by the
   ``pyramid_routesalchemy`` template) that is given a ``request``
   object and which returns a response or a dictionary.  You will use
   mostly ``<route>`` statements in a :term:`URL dispatch` based
   application to map URLs to code.  This ``route`` also names a
   ``view_renderer``, which is a template which lives in the
   ``templates`` subdirectory of the package.  When the
   ``.views.my_view`` view returns a dictionary, a :term:`renderer`
   will use this template to create a response.

#. *Lines 13-16*.  Register a ``<static>`` directive that will match
   any URL that starts with ``/static/``.  This will serve up static
   resources for us, in this case, at
   ``http://localhost:6543/static/`` and below.  With this
   declaration, we're saying that any URL that starts with ``/static``
   should go to the static view; any remainder of its path (e.g. the
   ``/foo`` in ``/static/foo``) will be used to compose a path to a
   static file resource, such as a CSS file.

#. *Line 18*.  The closing ``</configure>`` tag.

Content Models with ``models.py``
---------------------------------

In a SQLAlchemy-based application, a *model* object is an object
composed by querying the SQL database which backs an application.
SQLAlchemy is an "object relational mapper" (an ORM).  The
``models.py`` file is where the ``pyramid_routesalchemy`` Paster
template put the classes that implement our models.

Here is the source for ``models.py``:

   .. literalinclude:: src/basiclayout/tutorial/models.py
      :linenos:
      :language: py

#. *Lines 1-14*.  Imports to support later code.

#. *Line 16*.  We set up a SQLAlchemy "DBSession" object here.  We
   specify that we'd like to use the "ZopeTransactionExtension".  This
   extension is an extension which allows us to use a *transaction
   manager* instead of controlling commits and aborts to database
   operations by hand.

#. *Line 17*.  We create a declarative ``Base`` object to use as a
   base class for our model.

#. *Lines 19-27*.  A model class named ``MyModel``.  It has an
   ``__init__`` that takes a two arguments (``name``, and ``value``).
   It stores these values as ``self.name`` and ``self.value`` within
   the ``__init__`` function itself.  The ``MyModel`` class also has a
   ``__tablename__`` attribute.  This informs SQLAlchemy which table
   to use to store the data representing instances of this class.

#. *Lines 29-34*.  A function named ``populate`` which adds a single
   model instance into our SQL storage and commits a transaction.

#. *Lines 36-44*.  A function named ``initialize_sql`` which sets up
   an actual SQL database and binds it to our SQLAlchemy DBSession
   object.  It also calls the ``populate`` function, to do initial
   database population.

App Startup with ``run.py``
---------------------------

When you run the application using the ``paster`` command using the
``tutorial.ini`` generated config file, the application configuration
points at an Setuptools *entry point* described as
``egg:tutorial#app``.  In our application, because the application's
``setup.py`` file says so, this entry point happens to be the ``app``
function within the file named ``run.py``:

   .. literalinclude:: src/basiclayout/tutorial/run.py
      :linenos:
      :language: py

#. *Lines 1-3*. Imports to support later code.

#. *Line 12*.  Obtain the ``configure_zcml`` setting from a value in
   the ``tutorial.ini`` file's ``[app:sqlalchemy]`` section.  If it
   doesn't exist in the configuration file, default to
   ``configure.zcml``.

#. *Lines 13-15*. Get the database configuration string from the
   ``tutorial.ini`` file's ``[app:sqlalchemy]`` section.  This will be a URI
   (something like ``sqlite://``).

#. *Line 16*. Get the database echo settingf rom ``tutorial.ini``
   file's ``[app:sqlalchemy]`` section.  This will either be ``true``
   or ``false``.  If ``true``, the application will print SQL to the
   console as it is generated and run by SQLAlchemy.  By default, it
   is false.

#. Line *17*. We initialize our SQL database using SQLAlchemy, passing
   it the db string and a variant of the db_echo value.

#. *Line 18*.  We construct a :term:`Configurator`.  ``settings`` is
   passed as a keyword argument with the dictionary values passed by
   PasteDeploy as the ``settings`` argument.  This will be a
   dictionary of settings parsed by PasteDeploy, which contains
   deployment-related values such as ``reload_templates``,
   ``db_string``, etc.

#. *Lines 19-22*.  We then load a ZCML file to do application
   configuration, and use the
   :meth:`pyramid.configuration.Configurator.make_wsgi_app` method
   to return a :term:`WSGI` application.

