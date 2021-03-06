******************
Configuration file
******************

Brunch uses configuration file (``config.coffee`` or ``config.js``) located in the root directory to control various aspects of your application.

You can see all config default values in ``setConfigDefaults`` function of ``src/helpers.coffee`` in brunch source code.

``paths``
=============

``Object``: ``paths`` contains application paths to key directories. Paths are simple strings.

* ``public`` key: path to build directory that would contain output.
* ``test`` key: path to test files.

* Other valid keys, but not recommended to use: ``app``, ``vendor``, ``root``.

Example:

::

    paths:
      public: '/user/www/deploy'
      test: 'spec'

``files``
=========

``Required, object``: ``files`` configures handling of application files: which compiler would be used on which file, what name should output file have etc. 

* <type>: ``javascripts``, ``stylesheets`` or ``templates``
    * joinTo: (required) describes how files will be compiled & joined together. Available formats:
        * 'outputFilePath'
        * map of ('outputFilePath': /regExp that matches input path/)
        * map of ('outputFilePath': function that takes input path)
    * order: (optional) defines compilation order. ``vendor`` files will be compiled before other ones even if they are not present here.
        * before: list of files that will be loaded before other files
        * after: list of files that will be loaded after other files

.. note::

    all files from ``vendor`` directory are automatically (by-default) loaded before all files from ``app`` directory. So, ``vendor/scripts/jquery.js`` would be loaded before ``app/script.js`` even if order config is empty.

Example:

::

    files:
      javascripts:
        joinTo:
          'javascripts/app.js': /^app/
          'javascripts/vendor.js': /^vendor/
        order:
          before: [
            'vendor/scripts/console-helper.js',
            'vendor/scripts/jquery-1.7.0.js',
            'vendor/scripts/underscore-1.3.1.js',
            'vendor/scripts/backbone-0.9.0.js'
          ]

      stylesheets:
        joinTo: 'stylesheets/app.css'
        order:
          before: ['vendor/styles/normalize.css']
          after: ['vendor/styles/helpers.css']

      templates:
        joinTo: 'javascripts/app.js'

``conventions``
===============

``Object``: ``conventions`` define tests, against which all file pathnames will be checked.

* ``ignored`` key: regExp or function. Default value is a function that checks if filename starts with ``_`` (see implementation at ``setConfigDefaults`` function of ``src/helpers.coffee`` in brunch source code). Will check against files that would be ignored by brunch compilator, but that still be watched by watcher.
* ``assets`` key: regExp or function. Default value: ``/assets(\/|\\)/``. If test gives true, file won't be compiled and will be just moved to public directory instead.
* ``vendor`` key: regExp or function. Default value: ``/vendor(\/|\\)/``. If test gives true, file won't be wrapped in module, if there are any.
* ``tests`` key: regExp or function. Default value: ``/_test\.\w+$/``. If test gives true, the file will be auto-loaded in test environment.

Keep in mind that default brunch regexps, as you see, consider **all** ``vendor/`` (etc.) directories as vendor (etc.) files. So, ``app/views/vendor/thing/chaplin_view.coffee`` will be treated as vendor file.

Example:

::

    conventions:
      ignored: -> false       # no ignored files
      assets: /files(\/|\\)/  # vendor/jquery/files/jq.img
      tests: /_spec\.\w+$/    # user_spec.js etc

``modules``
===========

``Object``: consists of ``wrapper`` and ``definition`` subsettings.

``modules.wrapper``: ``String, Boolean or Function``: a wrapper that will be wrapped around compiled-to-javascript code in non-vendor directories. Values:

* ``commonjs`` (Default) — CommonJS wrapper.
* ``amd`` — AMD wrapper.
* ``false`` — no wrapping. Files will be compiled as-is.
* Function that takes path and data

``modules.definition``: ``String, Boolean or Function`` a code that will be added on top of every generated JavaScript file. Values:

* ``commonjs`` (Default) — CommonJS require definition.
* ``false`` — no definition.
* Function that takes path and data

Example:

::

    # Same as 'commonjs', but in function implementation.
    modules:
      wrapper: (path, data) ->
        """
    window.require.define({#{path}: function(exports, require, module) {
      #{data}
    }});\n\n
        """
      definition: false

``notifications``
=================

``Boolean``: Enables or disables Growl / inotify notifications. Default value is true (enabled).

``minify``
==========

`Optional, boolean`: determines if minifiers should be enabled or not.

Default value is ``false`` (``true`` if you run ``brunch build --minify``).

``server``
==========

``Object``: contains params of webserver that runs on ``brunch watch --server``.

* ``path``: (optional) path to nodejs file that will be loaded. The file must contain ``exports.startServer`` function.
* ``port``: (optional) port on which server will run
* ``base``: (optional) base URL from which to serve the app
* ``run``: should the server be launched with ``brunch watch``?

Example:

::

    server:
      path: 'server.coffee'
      port: 6832
      base: '/myapp'
      run: yes
