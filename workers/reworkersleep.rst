RE-WORKER-SLEEP
---------------
Release Engine Worker Plugin that sleeps for a period of seconds.

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
* Run the worker: ``python ./replugin/emailworker/__init__.py $YOUR_MQ_CONF.json``

We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober re-worker-sleep]$ python ./replugin/sleepworker/__init__.py
   2014-05-19 14:39:47,080 - SleepWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - SleepWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - SleepWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - SleepWorker - INFO - Consuming on queue worker.sleep

Commands
^^^^^^^^

The Sleep Worker steps are documented in :ref:`Worker Steps: Sleep <steps_sleep>`.
