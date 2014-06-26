RE-WORKER-BIGIP
---------------
Release Engine Worker Plugin that interfaces with F5 BigIP devices.

.. attention::
   This  plugin is internal to Red Hat only.

This worker takes the normal MQ configuration
(``conf/mq_settings.json``) as it's only configuration file:

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

   [user@frober re-worker-bigip]$ python ./replugin/bigipworker/__init__.py
   2014-05-19 14:39:47,080 - BigipWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - BigipWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - BigipWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - BigipWorker - INFO - Consuming on queue worker.bigip


Commands
````````

The BigIP worker has the following commands available.

InRotation
==========

Enable the current host in the BigIP.

**Example**

.. code-block:: yaml
   :linenos:

   hosts: ["example01.com", "example02.com"]
   steps:
       - bigip:InRotation




OutOfRotation
=============

Disable the current host in the BigIP.

**Example**

.. code-block:: yaml
   :linenos:

   hosts: ["example01.com", "example02.com"]
   steps:
       - bigip:OutOfRotation



ConfigSync
==========

Sync the BigIP configuration from a primary to a secondary.


**Parameters**

* ``envs`` (type: list)

  * **Required:** True

  * **Items:** Strings of environment names


**Example**

.. code-block:: yaml
   :linenos:

   hosts: ["example01.com", "example02.com"]
   steps:
       - bigip:ConfigSync:
             envs: ["qa", "stage", "prod"]
