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

Each running func worker requires a configuration file. This file
defines exactly which func modules and methods the worker is allowed
to run.

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
concurrently. Using a name like **funcFOO** is recommended. For
example, if you're using the Nagios plugin, you would create and bind
to a queue such as ``funcnagios`` (which is expanded to
``worker.funcnagios`` internally).


Once we've prepared a configuration file for our worker, we need to
tell the worker that it must use that specific configuration file.

.. note::

   Configuration is kind of hacky right now. In the future we may have
   demonized services which provide a more intelligent method of
   configuration. Until that time comes however, you'll just have to
   accept it as what it is.

From a fresh checkout of the `re-worker-func repo
<https://github.com/RHInception/re-worker-sleep.git>`_, we'll open
``replugin/funcworker/__init__.py`` in our favorite editor and scroll
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
       worker = FuncWorker(
           mq_conf,
           config_file='conf/example.json',
           output_dir='/tmp/logs/')
       worker.run_forever()

.. todo:: Update the previous example with configuration file information once available

* Set the ``mq_conf`` parameters to sane values (see also:
  :ref:`Setting Up The Bus<setting_up_the_bus>`)
* Next, we change the ``config_file`` line to point at our selected
  configuration file. For example:

 * ``config_file='conf/nagios.json',``

* Run the worker: ``python ./replugin/funcworker/__init__.py``

We should see output similar to the following if everything well:

.. code-block:: bash

   [root@frober re-worker-func]# python ./replugin/funcworker/__init__.py
   2014-05-19 14:39:47,080 - FuncWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - FuncWorker - INFO - Attempting connection with amqp://JoeUser:***@mq.example.com:5672/
   2014-05-19 14:39:47,412 - FuncWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - FuncWorker - INFO - Consuming on queue worker.funcnagios


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

The following is an example using the ``yumcmd`` module in a playbook
step.

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
   :emphasize-lines: 12,13

   ---
   group: inception
   name: Setup megafrobber
   execution:
     - description: restart the megafrobber service
       hosts:
         - foo.bar.example.com
       steps:
          - funcworker.service:
              subcommand: restart
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
