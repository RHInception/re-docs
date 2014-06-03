.. _re_core:

RE-CORE
-------
The core is essentially a finite state machine (**FSM**) hooked into a message bus and a database.

The core oversees the execution of all *release steps* for any given project. The core is separate from the actual execution of each release step. Execution is delegated to the **worker** component.


Running From Source
~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   $ . ./hacking/setup-env
   $ re-core -c ./examples/settings-example.json


.. _recore-conf:

RE-CORE Configuration
~~~~~~~~~~~~~~~~~~~~~
Configuration of the server is done in JSON. You can find an example configuration file in the `examples/ <https://github.com/RHInception/re-core/tree/master/examples>`_ directory.

You must point to a specific configuration file using the ``-c`` command-line option to start the FSM:

.. code-block:: bash

   $ re-core -c settings.json

Descriptions of all settings directives:

================== ====== ================== ===========================================
Name               Type   Parent             Value
================== ====== ================== ===========================================
LOGFILE            str    None               File name for the application level log
MQ                 dict   None               Where all of the MQ connection settings are
SERVER             str    MQ                 Hostname or IP of the server
NAME               str    MQ                 Username to connect with
PASSWORD           str    MQ                 Password to authenticate with
QUEUE              str    MQ                 Queue on the server to bind
DB                 dict   None               Where all the DB connection settings are
SERVERS            list   DB                 List of all of the MongoDB hostname/IPs
DATABASE           str    DB                 Name of the MongoDB database
NAME               str    DB                 Username to connect with
PASSWORD           str    DB                 Password to authenticate with
PHASE_NOTIFICATION dict   None               Notifications that will always happen in a phase
TABOOT_URL         str    PHASE_NOTIFICATION URL with %s to taboot tailer EX: http://example.com/taboot/%s/
TOPIC              str    PHASE_NOTIFICATION The topic (routing key) to send notification on. EX: notify.irc.
TARGET             list   PHASE_NOTIFICATION The targets to send the notification to. EX: ["#mychannel", "auser"]
================== ====== ================== ===========================================

For an example see `example-config.json <https://github.com/RHInception/re-core/blob/master/examples/settings-example.json>`_.


.. _recore-deployment:

RE-CORE Deployment
~~~~~~~~~~~~~~~~~~

.. note::

   The release engine is only deployable via source code at this time.


.. note::

   Release engine components are no fully demonized at this
   time. Therefore, deployment requires running each component in
   something like **screen**.

* Change into the directory you cloned **re-core** into
* Run **screen**
* Update your :ref:`re-core config file <recore-conf>` with appropriate values
* Update your paths by running: ``. ./hacking/setup-env``
* Run ``re-core -c path/to/settings.json``

You should see output similar to the following:

.. code-block:: bash

   [~/release-engine/re-core] $ re-core -c ./real-settings.json
   2014-05-19 13:56:00,179 - __init__:start_logging:43 - DEBUG - initialized stdout logger
   2014-05-19 13:56:00,180 - __init__:parse_config:53 - DEBUG - Parsed configuration file

Additional output will be directed to the log file you configured in
the ``settings.json`` file. The default log file is called
``recore.log`` and will be in your present working directory.
