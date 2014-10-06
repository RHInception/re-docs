.. _re_worker_output:

RE-WORKER-OUTPUT
----------------
Release Engine Worker Plugin consumes output from other works on the bus and writes it to files.

.. note::
   This is an output consumer and is not meant to be used in steps.


This worker takes two configuration files. The first is the worker configuration file. It should look like this:

.. code-block:: json

  {
      "queue": "output",
      "output_dir": "/tmp/",
      "redactions': [
          "somethingtomakealineasredacted",
          "valid[rR]?egex"
      ]
  }

* Set the ``output_dir`` to where the output files should reside.

.. versionadded:: 0.0.2
* ``redactions`` is an optional config option. If set it should be a list of strings or valid regular expressions which will be used to find lines that will be redacted.


The second configuration file is the normal MQ configuration:

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
  * **From source:** ``python ./replugin/outputworker/__init__.py` -w $YOUR_CONFIG_FILE.json $YOUR_MQ_CONF.json``
  * **From install:** ``re-worker-output -w $YOUR_SERVICE_NOW_CONF.json $YOUR_MQ_CONF.json``


.. note::
   You may need to add the following to your PYTHONPATH to be able to use Jinja2 on RHEL6: ``/usr/lib/python2.6/site-packages/Jinja2-2.6-py2.6.egg``


We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober]$ re-worker-output -w myconf.json mq.json
   2014-05-19 14:39:47,080 - OutputWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - OutputWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - OutputWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - OutputWorker - INFO - Consuming on queue worker.output
