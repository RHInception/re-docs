RE-WORKER-GIT
-------------
Release Engine Worker Plugin does basic git work.

This worker takes two configuration files. The first is the GIT configuration file. It should look like this:

.. code-block:: json

  {
    "workspace_dir": "/tmp/",
    "scripts": {
        "nameofscript": "/full/path/to/script"
    }
  }



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
* Run the worker: ``re-worker-git -w $YOUR_CONFIG_FILE.json $YOUR_MQ_CONF.json``

We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober re-worker-git]$ python ./replugin/gitworker/__init__.py
   2014-05-19 14:39:47,080 - GitWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - GitWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - GitWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - GitWorker - INFO - Consuming on queue worker.git

Commands
^^^^^^^^

The Git Worker steps are documented in :ref:`Worker Steps: Git <steps_git>`.
