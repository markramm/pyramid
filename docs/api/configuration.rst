.. _configuration_module:

:mod:`pyramid.configuration`
-------------------------------

.. automodule:: pyramid.configuration

  .. autoclass:: Configurator(registry=None, package=None, settings=None, root_factory=None, authentication_policy=None, authorization_policy=None, renderers=DEFAULT_RENDERERS, debug_logger=None, locale_negotiator=None, request_factory=None, renderer_globals_factory=None, default_permission=None)

     .. attribute:: registry

        The :term:`application registry` which holds the configuration
        associated with this configurator.

     .. automethod:: begin

     .. automethod:: end

     .. automethod:: hook_zca()

     .. automethod:: unhook_zca()

     .. automethod:: get_settings

     .. automethod:: with_package

     .. automethod:: maybe_dotted

     .. automethod:: absolute_resource_spec

     .. automethod:: setup_registry(settings=None, root_factory=None, authentication_policy=None, renderers=DEFAULT_RENDERERS, debug_logger=None, locale_negotiator=None, request_factory=None, renderer_globals_factory=None)

     .. automethod:: add_renderer(name, factory)

     .. automethod:: add_route

     .. automethod:: add_static_view(name, path, cache_max_age=3600)

     .. automethod:: add_settings

     .. automethod:: add_subscriber

     .. automethod:: add_translation_dirs

     .. automethod:: add_handler

     .. automethod:: add_view

     .. automethod:: derive_view

     .. automethod:: load_zcml(spec)

     .. automethod:: make_wsgi_app()

     .. automethod:: override_resource(to_override, override_with)

     .. automethod:: scan(package=None, categories=None)

     .. automethod:: set_forbidden_view(view=None, attr=None, renderer=None, wrapper=None)

     .. automethod:: set_notfound_view(view=None, attr=None, renderer=None, wrapper=None)

     .. automethod:: set_locale_negotiator

     .. automethod:: set_default_permission

     .. automethod:: set_request_factory

     .. automethod:: set_renderer_globals_factory

     .. automethod:: testing_securitypolicy

     .. automethod:: testing_models

     .. automethod:: testing_add_subscriber

     .. automethod:: testing_add_renderer

