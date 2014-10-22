RE-WORKER-SATELLITE5
--------------------
Release Engine Worker Plugin to promote packages between Red Hat Satellite 5 channels.

This worker takes two configuration files. The first is the Satellite
5 configuration file. It should look like this:

.. code-block:: json

   {
       "satellite_url": "https://satellite.example.com/rpc/api",
       "satellite_login": "username",
       "satellite_password": "password"
   }



This worker takes the normal MQ configuration as it's other
configuration file:

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
* Run the worker: ``re-worker-satellite5 -w $YOUR_CONFIG_FILE.json $YOUR_MQ_CONF.json``

We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober re-worker-satellite5]$ python ./replugin/satellite5worker/__init__.py
   2014-05-19 14:39:47,080 - Satellite5Worker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - Satellite5Worker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - Satellite5Worker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - Satellite5Worker - INFO - Consuming on queue worker.satellite5

Commands
^^^^^^^^

The Satellite 5 Worker steps are documented in :ref:`Worker Steps: Satellite 5 <steps_satellite5>`.
