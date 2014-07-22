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
RELEASE_LOG_DIR    str    None               Directory for per-release logging (default: ``None``)
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
TABOOT_URL         str    PHASE_NOTIFICATION URL with ``%s`` to taboot tailer EX: http://example.com/taboot/%s/
TOPIC              str    PHASE_NOTIFICATION The topic (`routing key`) to send notification on. EX: ``notify.irc``.
TARGET             list   PHASE_NOTIFICATION The targets to send the notification to. EX: ``["#mychannel", "auser"]``
PRE_DEPLOY_CHECK   list   None               The yes/no checks to make prior to deployment (see below for more information)
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


Per-release Logging
~~~~~~~~~~~~~~~~~~~

By default, the FSM will log to the console and a single logfile
(``LOGFILE``).

Optionally, one may log the FSM activity for **each release** to a
separate file. This is done by configuring the re-core
``RELEASE_LOG_DIR`` setting with the path to the log-holding
directory.

If per-release logging is enabled, the log files will be created as:
``RELEASE_LOG_DIR/FSM-STATE_ID.log``

.. warning::

   Be sure the FSM has permission to write the specified
   directory. You won't find out it can't until the first release is
   attempted.


.. code-block:: json
   :linenos:
   :emphasize-lines: 3

   {
       "LOGFILE": "recore.log",
       "RELEASE_LOG_DIR": "/var/log/recore",
       "MQ": {
           "SERVER": "amqp.example.com"
      }
   }


Pre-Deployment Checks
~~~~~~~~~~~~~~~~~~~~~

An re-core instance may be configured to run one or more scripts prior
to the deployment of any playbook. Each pre-deployment check defines
the command to run and the expected result from the command. If
expected equals observed, then the check is considered to have
passed. If expected is not equal to observed, then the check has
failed and the entire deployment is marked as failed.

.. important:: These checks apply to *all* deployments

Configuration of pre-deployment checks takes place in the re-core
``setting.json`` file.

Example settings

.. code-block:: json
   :linenos:
   :emphasize-lines: 6-16

   {
       "LOGFILE": "recore.log",
       "RELEASE_LOG_DIR": null,

       "PRE_DEPLOY_CHECK": [{
           "Require Change Record": {
               "COMMAND": "servicenow",
               "SUBCOMMAND": "getchangerecord",
               "PARAMETERS": {},
               "EXPECTATION": {
                   "status": "completed",
                   "data": {
                       "exists": true
                   }
               }
           }
       }]
   }


Here we see a new directive, ``PRE_DEPLOY_CHECK`` (line **5**), this
key holds a list whose members are nested dictionaries (lines **6** →
**16**). This example has one nested-dictionary. It has one key, that
is the name of the check, **Require Change Record**. You can give any
name you want to keys as long as it is JSON parsable.

Now let's look at this nested-dictionary closer:

.. code-block:: python

   {
       "COMMAND": "servicenow",
       "SUBCOMMAND": "getchangerecord",
       "PARAMETERS": {},
       "EXPECTATION": {
           "status": "completed",
           "data": {
               "exists": true
           }
       }
   }


* ``COMMAND`` - Name of the worker to run the check with
* ``SUBCOMMAND`` - The specific sub-command to run on that worker
* ``PARAMETERS`` - Dictionary with variable keys depending on what your worker requires
* ``EXPECTATION`` - The result we expected to get back from the check.

**Pass or fail** is determined by comparing the *actual* response
against ``EXPECTATION``. If they are the same then the check
passes. If they differ then the check fails and the deployment is
marked as *failed* and aborted.
