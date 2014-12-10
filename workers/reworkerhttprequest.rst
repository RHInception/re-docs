RE-WORKER-HTTPREQUEST
---------------------
Release Engine Worker Plugin that executes HTTP requests.


This worker takes two configuration files. The first is the worker configuration file. It should look like this:

.. code-block:: json

  {
    "queue": "httprequest"
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
* Run the worker: ``re-worker-httprequest -w $YOUR_WORKER_CONFIG $YOUR_MQ_CONF.json``

We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober re-worker-httprequest]$ re-worker-httprequest -w httprequest.json mq.json
   2014-05-19 14:39:47,080 - HTTPRequestsWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - HTTPRequestsWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - HTTPRequestsWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - HTTPRequestsWorker - INFO - Consuming on queue worker.httprequest

Commands
^^^^^^^^

The HTTPRequest Worker steps are documented in :ref:`Worker Steps: HTTPRequest <steps_httprequest>`.
