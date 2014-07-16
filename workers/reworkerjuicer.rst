.. _re_worker_juicer:

RE-WORKER-JUICER
----------------

.. contents::
   :depth: 2
   :local:


Release Engine Worker Plugin to run Juicer commands

.. admonition:: What's Juicer?

   The **Juicer** worker allows you to upload and promote batches of
   RPMs into `Yum <http://yum.baseurl.org/>`_ repositories. In juicer
   terminology, these batches of RPMs are referred to as `release
   carts
   <https://github.com/juicer/juicer/wiki/cart-json-specification>`_.


About & Setup
~~~~~~~~~~~~~

A **juicer** worker allows you to upload/promote RPMs as just another
step in your release process. **Note** however that the juicer plugin
requires that additional information is passed to it when the release
is started. See :ref:`Dynamic Variables <rerest_dynamic_variables>`
for more information on this topic.


To run the worker the normal MQ configuration must be defined and used.

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
* Run the worker

  * **From source:** ``python ./replugin/juicerworker/__init__.py $YOUR_MQ_CONF.json``
  * **From install:** ``re-worker-juicer $YOUR_MQ_CONF.json``


We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober re-worker-juicerworker]$ re-worker-juicer mq_conf.json
   2014-05-19 14:39:47,080 - JuicerWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - JuicerWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - JuicerWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - JuicerWorker - INFO - Consuming on queue worker.juicer


Juicer Client Configuration
```````````````````````````
See the upstream `juicer configuration
<https://github.com/juicer/juicer/blob/master/docs/markdown/config.md>`_
documentation for instructions on how to setup a system accounts
juicer configuration file.


Dependencies
````````````
Use of the juicer worker requires a configured and running `Pulp
Server <http://www.pulpproject.org/>`_ installation. Setup and
maintenance of pulp servers is out of scope for this
documentation. However, they provide `detailed setup instructions
<https://pulp-user-guide.readthedocs.org/en/pulp-2.3/installation.html>`_
to help get you started.


Commands
~~~~~~~~

The Juicer Worker steps are documented in :ref:`Worker Steps: Juicer <steps_juicer>`.
