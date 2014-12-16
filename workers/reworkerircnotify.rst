RE-WORKER-IRCNOTIFY
-------------------
Release Engine Worker Plugin can send notifications to IRC.

.. note::
   This is a notification worker can handle both notification and step message formats. When receiving step message format it will ignore all items with the exception of the slug, message, phase and target within the parameters key.

This worker takes two configuration files. The first is the IRCNOTIFY configuration file. It should look like this:

.. code-block:: json

  {
    "server": "127.0.0.1",
    "port": 6697,
    "ssl": true,
    "channels": ["#release-engine"],
    "nick": "renotify"
  }

* Set the ``IRCNOTIFY`` parameters to match your IRC server configuration.

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
* Run the worker: ``re-worker-ircnotify -w $YOUR_CONFIG_FILE.json $YOUR_MQ_CONF.json``

We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober re-worker-ircnotify]$ re-worker-ircnotify -w $YOUR_CONFIG_FILE.json $YOUR_MQ_CONF.json
   2014-05-19 14:39:47,080 - IRCNotifyWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - IRCNotifyWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - IRCNotifyWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - IRCNotifyWorker - INFO - Consuming on queue worker.ircnotifyworker
