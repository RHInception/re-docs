RE-WORKER-SQL
-------------
Release Engine Worker Plugin that works with SQL databases.


This worker takes two configuration files. The first is the SQL configuration file. It should look like this:

.. code-block:: json

  {
    "queue": "sql",
    "databases": {
        "memory": {
            "uri": "sqlite:///:memory:",
            "kwargs": {}
        },
        "testdb": {
            "uri": "sqlite:///test.db",
            "kwargs": {
                "echo": true
            }
        }
    }
  }

The important bits are under the database key. Here there are subkeys which define a friendly database name that can be used in playbooks. There are then two required keys under the name: uri and kwargs. uri is the database connection uri (see: http://docs.sqlalchemy.org/en/rel_0_8/core/engines.html#supported-databases). The other is kwargs which are additional arguments to pass to the engine object. In most cases it should be an empty dictionart.


This worker takes the normal MQ configuration as it's only configuration file:

.. code-block:: json

  {
    "server": "127.0.0.1",
    "port": 5672,
    "vhost": "/",
    "user": "guest",
    "password": "guest"
  }

* Set the ``MQ config file`` parameters to sane values (see also:
  :ref:`Setting Up The Bus<setting_up_the_bus>`)
* Run the worker: ``re-worker-sql -w $YOUR_WORKER_CONFIG $YOUR_MQ_CONF.json``

We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober re-worker-sql]$ re-worker-sql -w sql.json mq.json
   2014-05-19 14:39:47,080 - SQLWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - SQLWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - SQLWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - SQLWorker - INFO - Consuming on queue worker.sql

Commands
^^^^^^^^

The SQL Worker steps are documented in :ref:`Worker Steps: SQL <steps_sql>`.
