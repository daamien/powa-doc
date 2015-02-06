Development
===========

This page acts as a central hub for resources useful for PoWA developers.

PoWA
----



Clone the repository:

.. code:: bash

  git clone https://github.com/dalibo/powa/
  cd powa/
  make && sudo make install

Any modification to the background-worker code will need a PostgreSQL restart.

In order to contribute another source of data, you will have to implement the
following functions:


**snapshot**:
  This function is responsible for taking a snapshot of the data source data,
  and store it somewhere. Usually, this is done in a staging table named
  **my_data_source_history_current**. It will be called every `powa.frequency`
  seconds.
  The function signature looks like this:
  
  .. code-block:: plpgsql

    CREATE OR REPLACE FUNCTION my_data_source_take_snapshot() RETURNS void AS $PROC$
    ...
    $PROC$ language plpgsql;

**aggregate**:
  This function will be called after every `powa.coalesce` number of snapshots.
  It is responsible for aggregating the current staging values into another
  table, to reduce the disk usage for PoWA. Usually, this will be done in an
  aggregation table named **my_data_source_history**.
  The function signature looks like this:
  
  .. code-block:: plpgsql

    CREATE OR REPLACE FUNCTION my_data_source_aggregate() RETURNS void AS $PROC$
    ...
    $PROC$ language plpgsql;

**purge**:
  This function will be called after every 10 aggregates, and is responsible for
  purging stale data that should not be kept. The function should take the
  'powa.retention' global parameter into account to prevent removing data that
  would still be valid.
  
  .. code-block:: plpgsql

    CREATE OR REPLACE FUNCTION my_data_source_aggregate() RETURNS void AS $PROC$
    ...
    $PROC$ language plpgsql;

Each of these functions should then be registered:

.. code-block:: sql

  INSERT INTO powa_functions (module, operation, function_name, added_manually)
  VALUES ('my_data_source', 'snapshot', 'powa_mydatasource_snapshot', false),
          ('my_data_source', 'aggregate', 'powa_mydatasource_aggregate', false),
          ('my_data_source', 'purge', 'powa_mydatasource_purge', false);

PoWA-Web
--------

This section only covers the most simple changes one would want to make to PoWA.
For more comprehensive documentation, see the Powa-Web project documentation
itself.

Clone the repository:

.. code:: bash

  git clone https://github.com/dalibo/powa-web/
  cd powa/
  make && sudo make install

To run the application, use run_powa.py, which will run powa in debug mode.
That means the javascript files will not be minified, and will not be compiled
into one giant source file.


CSS files are generated using `sass <http://sass-lang.com>`.
Javascript files are splitted into AMD modules, which are managed by `requirejs
<http://requirejs.org/>` and compiled using `grunt <http://gruntjs.com>`.

These projects depend on NodeJS, and NPM, its package manager, so make sure you are able to install them on your
distribution.

Install the development dependencies:

.. code:: bash

  npm install -g grunt-cli
  npm install grunt
  npm install .

Then, you can run ``grunt`` to update only the css files, or regenerate optimized
javascript builds with ``grunt dist``.
