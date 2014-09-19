.. _re_worker_func:

RE-WORKER-FUNC
--------------
Release Engine Worker Plugin to run commands over `FUNC <https://fedorahosted.org/func/>`_.

.. contents::
   :depth: 1
   :local:


.. admonition:: What's FUNC?

   Func stands for *Fedora Unified Network Controller*. Func allows
   for running commands on remote systems in a secure way, like SSH,
   but offers several improvements.

Func is extensible, as such it comes with several modules. Each module
gives you more options for what you do with func. Here's a few of the
`modules <https://fedorahosted.org/func/wiki/ModulesList>`_ which Func
ships with:

* `Command <https://fedorahosted.org/func/wiki/CommandModule>`_ for running arbitrary commands somewhere remote
* `Nagios <https://fedorahosted.org/func/wiki/NagiosServerModule>`_ for handling common tasks in Nagios related to downtime and notifications
* `Service <https://fedorahosted.org/func/wiki/ServiceModule>`_ for starting, stopping, and checking the status of system services

This plugin allows you to run any number of func worker
instances. Each instance is configured to allow for calling specific
func module commands through it. However, this requires configuration
before it can work.

.. note::

   Installation and configuration of a func infrastructure is outside
   of the scope of this documentation. Please refer to `the upstream
   documentation
   <https://fedorahosted.org/func/wiki/InstallAndSetupGuide>`_ for
   additional information.

.. _worker_func_configuration:

Configuration
~~~~~~~~~~~~~

Each running func worker requires a worker and an MQ configuration file.
The worker configuration file defines exactly which func modules and methods
the worker is allowed to run.

The configuration file uses the following **pattern** in JSON format:

.. code-block:: json
   :linenos:

   {
       "queue": "QUEUE_NAME",
       "FUNC_MODULE": {
           "METHOD_1": ["REQUIRED", "PARAMETERS"],
           "METHOD_2": ["ONE_ITEM"],
           "METHOD_N": []
   }


In this example on line 2 we see a parameter ``queue``. This is how we
set a specific name for the queue to bind to on the message bus. This
parameter is **required** if you are running more than one func worker
concurrently. Using a name that defines what the workers does. For
example, if you're using the Nagios plugin, you would create and bind
to a queue such as ``nagios`` (which is expanded to ``worker.nagios``
internally).


The second configuration file is the normal MQ configuration used for
connecting to the bus:

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
* Run the worker: ``python ./replugin/funcworker/__init__.py` -w $YOUR_CONFIG_FILE.json $YOUR_MQ_CONF.json``

We should see output similar to the following if everything well:

.. code-block:: bash

   [root@frober re-worker-func]# python ./replugin/funcworker/__init__.py
   2014-05-19 14:39:47,080 - FuncWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - FuncWorker - INFO - Attempting connection with amqp://JoeUser:***@mq.example.com:5672/
   2014-05-19 14:39:47,412 - FuncWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - FuncWorker - INFO - Consuming on queue worker.nagios


Example Configuration
`````````````````````

Here is a real-life example of a func worker which may be used to run
the ``yumcmd`` modules ``install``, ``remove``, and ``update``
methods.

.. code-block:: json

   {
       "yumcmd": {
           "install": ["package"],
           "remove": ["package"],
           "update": []
       }
   }

In the above example we see on the ``install`` line that there is a
list, ``["package"]``, with one item in it. This means that when used
as a step in a playbook a **single** ``package`` parameter must also
be provided.

In contrast, we can see that the ``update`` method has an empty list,
``[]``, following it. This indicates that the ``yumcmd.update`` method
accepts no parameters. Using this method in a playbook step would
update all packages on the target system.

It is also possible to define when the result of a command will not follow
the normal 0 success/any other return code failure. To do this add a
``return_codes`` section listing the any subcommands which do not match
the traditional return codes. Any subcommands not listed will default
to 0 as success and anything else as failure.

As an example, if one wanted the subcommand ``update`` to be successful
if it exits with a 0, 10 or a 100 the following could be used:

.. code-block:: json

   {
       "yumcmd": {
           "install": ["package"],
           "remove": ["package"],
           "update": []
       },
       "return_codes": {
           "update": [0, 10, 100]
       }
   }


The following is an example using the ``yumcmd`` module in a playbook
step.

Commands
~~~~~~~~

The FUNC Worker steps are documented in :ref:`Worker Steps: FUNC <steps_func>`.


Example: Installing a package
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following is an example of a :ref:`playbook <playbooks>` which
installs a single package:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 9,10

   ---
   group: inception
   name: Setup megafrobber
   execution:
     - description: install the megafrobber package
       hosts:
         - foo.bar.example.com
       steps:
          - yumcmd:install:
              package: megafrobber

Here we can see in lines **9** → **10** how to call the ``install``
sub-command for the **funcworker**.


Example: Stopping a Service
~~~~~~~~~~~~~~~~~~~~~~~~~~~

In this example :ref:`playbook <playbooks>` we will use the
**service** sub-command to restart the **megafrobber** system
service. For reference, first we'll look at the **funcworker**
configuration for the **service** module:

.. code-block:: json
   :linenos:

   {
       "service": {
           "stop": ["service"],
           "start": ["service"],
           "restart": ["service"],
           "reload": ["service"],
           "status": ["service"]
       }
   }

Recall from what we learned in the :ref:`configuration
<worker_func_configuration>` section that this defines one module,
``service``.

As we can see above, the ``service`` module has 5 sub-commands, each
requires one parameter, ``service``, which is the name of the service
to control.

The following example shows how to use the
``funcworker.service.restart`` method to restart the ``megafrobber``
service. This happens in lines **9** → **10**:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 9,10

   ---
   group: inception
   name: Setup megafrobber
   execution:
     - description: restart the megafrobber service
       hosts:
         - foo.bar.example.com
       steps:
          - service:restart:
              service: megafrobber

Example: Trying/Checking
~~~~~~~~~~~~~~~~~~~~~~~~

We can also add optional parameters ``tries`` and ``check_scripts``. ``check_scripts``
is an array of scripts that will be run after the command. If they all return success
(a zero return value) the whole command is considered successful. However if any
return a non zero value the step is considered failed. The ``tries`` parameter tells
the worker to try the step `X` number of times before giving up.

The following example will attempt the restart ``megafrobber`` and run the check_script
``/usr/bin/diditwork``. If the either the restart or the check script return a failure
it will try again until it's limit of 5 tries has been hit (at which point it returns
failure back to the bus).

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 11,12

   ---
   group: inception
   name: Setup megafrobber
   execution:
     - description: restart the megafrobber service
       hosts:
         - foo.bar.example.com
       steps:
          - service:restart:
              service: megafrobber
              tries: 5
              check_scripts: ["/usr/bin/diditwork"]


More Modules
~~~~~~~~~~~~

The func worker ships with support for several other func modules
out-of-the-box. To see them all, check out `GitHub:
re-worker-func/conf/
<https://github.com/RHInception/re-worker-func/tree/master/conf>`_


----

 See `Func - Module List <https://fedorahosted.org/func/wiki/ModulesList>`_ for more information.
