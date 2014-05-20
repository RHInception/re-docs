RE-WORKER-SLEEP
---------------
Release Engine Worker Plugin that sleeps for a period of seconds.

From a fresh checkout of the `re-worker-sleep repo
<git@github.com:RHInception/re-worker-sleep.git>`_, we'll open
``replugin/sleepworker/__init__.py`` in our favorite editor and scroll
to the bottom. We'll see something similar to this:

.. code-block:: python

   if __name__ == '__main__':
       mq_conf = {
           'server': '127.0.0.1',
           'port': 5672,
           'vhost': '/',
           'user': 'guest',
           'password': 'guest',
       }
       worker = SleepWorker(
           mq_conf,
           output_dir='/tmp/logs/')
       worker.run_forever()

* Set the ``mq_conf`` parameters to sane values (see also:
  :ref:`Setting Up The Bus<setting_up_the_bus>`)
* Run the worker: ``python ./replugin/sleepworker/__init__.py``

We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober re-worker-sleep]$ python ./replugin/sleepworker/__init__.py
   2014-05-19 14:39:47,080 - SleepWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - SleepWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - SleepWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - SleepWorker - INFO - Consuming on queue worker.sleepworker


Example
```````

Example: Sleeping for 5 Seconds
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following is just a *snippet* of a complete playbook.

.. code-block:: json

   {
      "steps": [
          {
              "name": "Wait for 5 seconds",
              "plugin": "sleepworker",
              "parameters": {
                  "seconds": 5
              }
          }
      ]
   }


The parameter ``seconds`` must be sent else no sleeping will occur.

* ``seconds`` - The amount of seconds to sleep. This can be an int or float.
