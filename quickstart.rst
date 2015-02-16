.. _quickstart:

Quickstart
==========


Install PoWA-archivist on the PostgreSQL instance
*************************************************


Prerequisites
-------------

You will need a compiler, and the appropriate PostgrESQL development packages.

On Debian:

.. code-block:: bash

  apt-get install postgresql-server-dev-9.4 postgresql-contrib-9.4

On RHEL / CentOS:

.. code-block:: bash

  yum install postgresql94-devel postgresql94-contrib


Installation
------------

Then, download it:

.. parsed-literal::
  wget |download_link|

A convenience script is offered to build every project that PoWA can take
advantage of:

.. code-block:: bash

  ./install_all.sh

This script will ask you for your super user password, provided the sudo command
is available, and install powa, pg_qualstats and pg_stat_kcache for you.

Once done, you should modify your PostgreSQL configuration as mentioned by the
script, putting the following line in your `postgresql.conf` file:

.. code-block:: ini

  shared_preload_libraries='pg_stat_statements,powa,pg_stat_kcache,pg_qualstats'

And restart your server, according to your distribution's preferred way of doing
so, for example:

Init scripts:

.. code-block:: bash

    /etc/init.d/postgresql-9.4 restart

Debian pg_ctlcluster wrapper:

.. code-block:: bash

    pg_ctlcluster 9.4 main restart

Systemd:

.. code-block:: bash

    systemctl restart postgresql

The last step is to create a database dedicated to the PoWA repository, and
create every extension in it. The install_all.sql file performs this task:

.. code-block:: bash

  psql -U postgres -f install_all.sql
  CREATE DATABASE
  You are now connected to database "powa" as user "postgres".
  CREATE EXTENSION
  CREATE EXTENSION
  CREATE EXTENSION
  CREATE EXTENSION
  CREATE EXTENSION


Install powa-web anywhere
*************************

You do not have to install the GUI on the same machine your instance is running.

Prerequisites
-------------

* The Python language, either 2.7 or > 3
* The pip installer for Python. It is usually packaged as "python-pip", for example:


Debian:

.. code-block:: bash

  sudo apt-get install python-pip

RHEL / Centos:

.. code-block:: bash

  sudo yum install python-pip


Installation
------------

To install powa-web, just issue the following comamnd:

.. code-block:: bash

  sudo pip install powa-web

Then you'll have to configure a config file somewhere, in one of those location:

* /etc/powa-web.conf
* ~/.config/powa-web.conf
* ~/.powa-web.conf
* ./powa-web.conf

The configuration file is a simple JSON one. Copy the following content to one
of the above locations:

.. code-block:: json

  servers={
    'main': {
      'host': 'localhost',
      'port': '5432',
      'database': 'powa'
    }
  }
  cookie_secret="SUPERSECRET_THAT_YOU_SHOULD_CHANGE"

The servers key define a list of server available for connection by PoWA-web.
You should ensure that the pg_hba.conf file is properly configured.

The cookie_secret is used as a key to crypt cookies between the client and the
server. You should DEFINETLY not keep the default if you value your security.

Then, run powa-web:

.. code-block:: bash

  powa-web

The UI is now available on the 8888 port.

