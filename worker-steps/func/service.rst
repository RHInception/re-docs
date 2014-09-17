.. _steps_func_service:

Service
=======

The **service** module allows you to interact with system services, as
you would with the ``service`` or ``systemctl`` commands. Only one
example is included in this section because the syntax for each of the
**service** module steps are nearly identical.

Example
-------
This example demonstrates how to restart the **megafrobber** service
(see lines **3** and **4**).

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-4

   hosts: ['localhost']
   steps:
       - service:restart:
           service: megafrobber

To use any of the other sub-commands, on line **3** in this example we
would replace ``service:restart`` with the desired subcommand. Such as
``service:stop`` or ``service:reload``.


service:stop
------------
Stop a given service.

service:start
-------------
Start a given service.

service:restart
---------------
Restart a given service.

service:status
--------------
Return the status (running, stopped, etc) of a given service.

service:reload
--------------
Tell a service to reload it's configuration files.

.. note:: Not all system services support all the given subcommands. This is especially true for **reload**.

