RE-WORKER-SERVICENOW
--------------------
Release Engine Worker Plugin that does basic interaction with Service Now.


This worker takes two configuration files. The first is the SERVICE NOW configuration file. It should look like this:

.. code-block:: json

  {
      "servicenow_user": "username",
      "servicenow_password": "secret",
      "api_root_url": "https://127.0.0.1/api/now/v1"
  }

* Set the ``MQ config file`` parameters to sane values (see also:
  :ref:`Setting Up The Bus<setting_up_the_bus>`)
* Run the worker

  * **From source:** ``python ./replugin/servicenowworker/__init__.py -w $YOUR_SERVICE_NOW_CONF.json $YOUR_MQ_CONF.json``
  * **From install:** ``re-worker-servicenow -w $YOUR_SERVICE_NOW_CONF.json $YOUR_MQ_CONF.json``

We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober]$ re-worker-servicenow -w servicenow.json mq.json`
   2014-05-19 14:39:47,080 - ServiceNowWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - ServiceNowWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - ServiceNowWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - ServiceNowWorker - INFO - Consuming on queue worker.servicenow

Commands
^^^^^^^^

The ServiceNow Worker steps are documented in :ref:`Worker Steps: ServiceNow <steps_servicenow>`.
