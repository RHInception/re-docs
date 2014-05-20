RE-WORKER-FUNC
--------------
Release Engine Worker Plugin to run commands over `FUNC <https://fedorahosted.org/func/>`_.

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


Configuration
~~~~~~~~~~~~~

Each running func worker requires a configuration file. This file
defines exactly which func modules and methods the worker is allowed
to run.

The configuration file uses the following **pattern** in JSON format:

.. code-block:: json

   {
       "FUNC_MODULE": {
           "METHOD_1": ["REQUIRED", "PARAMETERS"],
           "METHOD_2": ["ONE_ITEM"],
           "METHOD_N": []
   }

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
   2014-05-19 14:39:47,413 - FuncWorker - INFO - Consuming on queue worker.funcworker


Example
```````

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

The following is just a *snippet* of a complete playbook.

.. code-block:: json

   {
      "steps": [
          {
              "name": "install the megafrobber command",
              "plugin": "funcworker",
              "parameters": {
                  "command": "yumcmd",
                  "subcommand": "install",
                  "package": "megafrobber",
              }
          }
      ]
   }


Example: Stopping a Service
~~~~~~~~~~~~~~~~~~~~~~~~~~~
The following is just a *snippet* of a complete playbook.

.. code-block:: json

   {
      "steps": [
          {
              "name": "stop foo service",
              "plugin": "funcworker",
              "parameters": {
                  "command": "service",
                  "subcommand": "stop",
                  "service": "megafrobber",
              }
          }
      ]
   }

We'll work backwards now and describe the func worker configuration
file which would define this release step:

All the important parts we're concerned with are in the ``parameters``
object: ``command``, ``subcommand``, and ``service``.

* ``command`` - The name of the func module to use
* ``subcommand`` - The name of the method to call in the selected func module
* ``service`` - This the name of the service to run ``subcommand`` on.

In this case we would be calling ``service.stop`` with the argument
``megafrobber``, indicating we wish to stop the ``megafrobber``
service on the remote host.


Notes
~~~~~
 See `Func - Module List <https://fedorahosted.org/func/wiki/ModulesList>`_ for more information.
